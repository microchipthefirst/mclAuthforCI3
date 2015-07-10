mclAuth bare for CI3

INTRODUCTION

This is a simple authentication system intended for intranet use.  There is no public sign on feature.  All users are created by a member of the admin group.
Authentication is based on login name and password.  The password is stored as a string encrypted by Bcrypt.
New users are, by default added to the Users groups.
New users and groups are disabled by default and need to be Activated before logon.

ASSUMPTIONS

It doesn't really matter which webserver you use but I am assuming apache is your choice.
Codeigniter uses a default page called index.php to handle user page requests. 
I use .htaccess in the root of the project to hide the index.php string from the url.

Instead of http://www.yourdomain.com/index.php/controllername/method,

we can use http://www.yourdomain.com/controllername/method
Your choice of webserver may need additional work to achieve the same result.
Running your own server separate to your dev machine improves performance on your dev machine.  The more similar your dev server is to your deployment server, the easier your deployment will be.
If you can't run your own internal server, sign up to Microsoft Azure for a 30-day free trail and create your own websites there.
Install a LAMP, WAMP or MAMP server or similar to get this system working. (Linux, Windows or Mac with Apache, Mysql and PHP)

INSTALL CI3

This version of mclAuth is a bare system that needs to be added to a copy of Codeigniter 3.  So you need to install CI3 first.
Simply download the latest version of Codeigniter 3, unzip it, copy the application and system folders to the root of your new project created in whichever IDE you use. I use Netbeans.

Also copy the index.php and license files to the project root.  If you need a copy of .htaccess that works on my server (and may not work on yours) you will find a copy in the mclAuth-with-CI3 folder on github.  The .htaccess also goes into the project root.


PREPARE CI3

There are three steps to be followed to set up Codeigniter to operate with the mclAuth system.

1.  Create a database and run the sql code included with the project.  This creates and populates two tables.  Please don't remove the default groups as that will break the system.
2.  In your IDE, open the application/config/autoload.php file.
Fill in the libraries and helpers lines so that they look like this:
```
		$autoload['libraries'] = array('database','form_validation','session');
		$autoload['helpers'] = array('form','url',);
```
Save and close the file.
 
Open the config.php file in the same folder.

Enter your domain name into the base_url section:
```
	$config['base_url'] = 'http://www.yourdomain.com/';
```
Note, the final slash is required.
If you want to remove the 'index.php' from the url, remove the 'index.php' string from the index_page section.
Down towards the bottom of the file is the section into which the session_key is entered.  Obtain a suitable session encryption key from the internet (search for Codeigniter encryption key) and enter that into the encryption_key section.

We are using session to control access to secure pages and the session class requires an encryption key.

Save the file and close it.
            
Open database.php from the same folder.
            
You will need the database user's name and password and the name of the database.  Please create a new user exclusively for this database, Don't use root.
            
Save and close the file.
        
CREATE A TEST SITE
            
Now we add the mclAuth files to the project.  
            
Having downloaded the files from github, you need to copy the admin folder from the controllers folder to your controllers folder.  Similarly copy the views admin folder to your views folder.  Finally copy the contents of the model folder to your models folder.
        
Now we need to create some pages that will use the mclAuth system.

CI3 insists on UCFirst() on class names and filenames for controllers and models.  
            
We will start slowly and build a few pages that will display a menu, the login system, the user admin system and use bootstrap to layout the page.
            
We are going to start with:
1.  Create a controller and view
2.  Create a template header and footer file containing unchanging code.  Also we will create pages to hold the on-demand content.
3.  Add menu items for login and admin.
            
First let's see if your system is working correctly.  Upload the project to your server and then open a browser and type in your domain name into the address bar.  If all is well, you should get the Codeigniter welcome screen.  If not, rename the .htaccess file and add back to the index_page section in config.php, the string 'index.php'.  Try again.

Success?  Problem lies with the .htaccess file.  Still fails? A more fundamental problem exist which you will have to fix.  Sorry I can't you more.
            
Under the application folder, you will find a controllers folder, a model folder and a views folder.  These are where the vast majority of your files will go.  We are only going to use the controller and views folders here.
            
In the controllers folder create a new php file called Main.php.  In it create the class Main and the function called index.  It should look like this:
``` 
	<?php
		class Main extends CI_Controller{
			function index(){
				echo 'Main controller';
			}
		}
```            
The default controller is set to 'welcome' so we need to change that.  Open the routes.php file from the application/config folder.
            
Change the default controller to 'main'.  Save the file and close it.
            
Refresh the browser.  You should see 'Main controller' in your browser.
            
You should never print directly to the screen from a controller, that is what views are for.  I use this technique only to check that the controller is responding and then create a view.
            
Delete the echo line in the controller and add:
 ```       
	$this->load->view('home');
 ```       
Now create a new php file in the views folder called home.php
            
In it, type:
```        
	echo 'Main view';
```        
Save it and refresh the browser.  You should see 'Main view'.
            
The home.php page contains no html code.  We need to add enough to make it an html compliant page.
            
Delete all the content of home.php and then add the following code:
```
	<!DOCTYPE html>
		<html>
			<head>
				<meta charset="utf-8">
				<meta name="viewport" content="width=device-width, initial-scale=1.0">
	                        <title></title>
			</head>
			<body>
				<p>Home page</p>
			</body>
		</html>
```
This code is needed on every webpage taht displays content.  But the only bit that changes page by page is the body content.  We only need on copy of the head code and closing body and html tags.
We are going to create two files to hold the unchanging html and create new files to contain the changing on-demand code.

Create a new folder called 'templates' in the view folder.
Inside that folder, create two php files called 'header.php' and 'footer.php'.
Cut and paste all the code in home.php down to and including the opening body tag into header.php.
Cut and paste the closing body and html tags into the footer.php file.  You should be left with just then &lt;p&gt; tag.

In the views folder, create a new file called 'start.php'.  Inside it type in the following three lines:
```
	include('templates/header.php');
	include $page . ('.php');
	include('footer.php');
```	
 Save and close	it.  We shouldn't have to touch it again.
 
 Open the main.php controller. Add a new line and edit the existing line to show:
``` 
 	function index(){
 		$data['page'] = 'home';
 		$this->load->view('start',$data);
 	}
```
Save it and refresh the browser.  You should see the 'Main page' text in your browser.
We have separated the static html into the header and footer files and generated a controller to call up the view, which consists of three files: header.php which sets up the html page (we have more to add to header), the content page, this time called home.php and the footer which finishes the html code.  We have more to add to the footer.  Once our project is complete, we don't touch the header or footer again.

Just to demostrate the dynamic nature of what we are doing, add the following line above the $this->load line:
```
	$data['message'] = 'This is the ';
```	
Save it open home.php.

Edit the echo line to show:
```
	<p><?php echo $message; ?>Home page</p>
```	
Save it and refresh the browser.  You should get 'This is the Main page'.
If you are going to use this technique, you must either ensure that $data['message'] is initialised before clling that page, or check to see if the $message exists before trying to print it.  Otherwise you'll get a PHP error.



	
ADDDING jQuery and BOOTSTRAP
We need to add a link to a css file in header.php and two links to javascript files in footer.php.
Open header.php and add this line just below the &lt;title&gt; tag:
```
	<link rel="stylesheet" type="text/css" href="<?php echo base_url('css/bootstrap.min.css'); ?>">
```        
Save and close.  Open footer.php and add these lines before the closing &lt;body&gt; tag.
```
	<script type="text/javascript" src="<?php echo base_url('js/jquery-2.1.3.min.js'); ?>"></script>
	<script type="text/javascript" src="<?php echo base_url('js/bootstrap.min.js'); ?>"></script>
```	
Save and close the file, refresh to browser. The text should appear in a slightly different font if all is working.  If not use SHIFT-F12 network tab to see if the files are loading.

Let's get bootstrap organising our page.  Close all files except home.php

Type in the following lines and then cut and paste the existing &lt;p&gt; tage into the centre div.
```
	<div class="container">
		<div class="row">
			
		</div>
	</div>
```	
Save the file and refresh the browser.
You should see the home page message, but this time it is nearer the centre of the page.  That is the container tag defining the display field for the page.

I prefer to set the container tag in the header file and close it in the footer.  I only need one container tag and it will include the menu which is added to the header file.  So please delete the container tag and its closing tag from home.php.

Open the header.php file.  Below the opening &lt;body&gt; tag type in the following:
```
	<div class="container">
		<nav class="navbar navbar-default" role="navigation">
		<!-- Brand and toggle are grouped for better mobile display -->
		<div class="navbar-header">
			<button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-ex1-collapse">
				<span class="sr-only">Toggle navigation</span>
				<span class="icon-bar"></span>
				<span class="icon-bar"></span>
				<span class="icon-bar"></span>
			</button>
			<a class="navbar-brand" href="#">Organisation</a>
		</div>
		<!-- Navlinks etc for toggling -->
		<div class="collapse navbar-collapse navbar-ex1-collapse nav-HeaderCollapse">
			<ul class="nav navbar-nav">
				<li class="active"><a href="<?php echo site_url('/'); ?>"></a>Home</li>
				<li><a href="<?php echo site_url('#'); ?>">Example menu item</a></li>
			</ul>
			<ul class="nav navbar-nav navbar-right">
				<li><a href="<?php echo base_url('about'); ?>">About</a></li>
				<li><a href="<?php echo base_url('contactus'); ?>">Contact Us</a></li>
			</ul>
		</div>
		</nav>	
```				
We've opened the container tag in header, now open footer.php and add 
```
	</div>
```	
above the closing &lt;/body&gt; tag.
Save all and refersh the browser.  You should get a grey menu bar.

This is a fairly standard menu.  The 'Example menum item' doesn't actually do anything except server as a template should you wish to start adding your own menu items.  The text type into the base_url brackets will be a controller name in an MVC based system.

ADDING SECURE PAGES

First we need to be able to access the login page.  We will add it as a menu item to the header file.
Immediately after the Contact Us menu item add the following code.
```
	<?php
		if($this->session->userdata('loggedin'){
			echo "<li><a href=";
			echo base_url('admin/login.logout') . ">Logout</a></li>";
		}else{
			echo "<li></a href=";
			echo base_url('admin/login') . ">Login</a></li>";
		}
	?>
```	
Save the file and refresh the browser.  You should see the new Login item to the right of the menu bar.
Click it.  The logon page should appear.  Login using the name 'admin' and password 'Pa$$w@rd'.
Note that the Login has changed to Logout.  Click Logout.

As admins we need to manage the user and group accounts.  We need a menu item but it should only show if a member of the admin group is logged on.  We will add another menu item.

In between the Home and Example menu items add this code:
```
	<?php
		if($this->session->userdata('loggedin') && $this->session->userdata('group') == '1'){
			echo "<li><a href=";
			echo base_url('/admin/admin_view') . ">Admin</a></li>";
		}
	?>
```
The code checks to see if the seesion variable 'group' exists and is set to '1' for the admin group.  If both are true the Admin menu appears.	
Save it, refresh the browser and login as admin.  Before the login, there is no Admin menu item.  After login, the Admin menu item appears.

Click the Admin menu.  A new page opens.  Here you can list the users and groups, create a new user and Activate or deactivate users and groups.

Create a new user.  The first and last names are your choice, so is the login name except that it must be unique.  You will be warned if the login name is already used.  The password must be 8 characters long but currently no password complexity is enforced.  That is a future project.Note that the default group membership is User.  If you need to create an admin user you must explicitly select Admin.
Before the new user can log in, you must explicitly Activate that user.

Logout and then login as your new user.  Does the Admin menu item appear?  No?  Good.

Now we can secure some pages so that only logged in users can access them.

Create a new controler called Secrets.php and add the following class and functions.
```
	<?php
	class Secrets extends CI_Controller{
		function __construct(){
			parent::__construct();
			session_start();
			if(!$this->session->userdata('loggedin')){
				redirect('Location'admin/login');
			}else{
				if($this->session->userdata('group') != '1' {
					redirect('Location:admin/login');
				}
			}
		}
		function index(){
			$data['page'] = 'secrets/secret_hoard';
			$this->load->view('start',$data);
		}
	}
```
Save and close the file and create a new folder in the views folder called 'secrets'.  In the secrets folder create a new view called 'secret_hoard.php'.  Type in something secret that only admins should see. Save the file.
In your browser type in the doamin name and add secrets:

	www.yourdomain.com/secrets
	
Your secret page should be displayed.
Logout and login with your ordinary user.  Try to display your secrets, the user should be shown the login page, even though they are still logged in.  It is up to your to create a better 'access denied' page rather than dumping the user back in the login page.

OK, that is a simple website with login and authentication.  I hope you like it.





