# php-mysql-server-setup
Apache + PHP + MySQL + PHPMyAdmin server setup in windows 11

## What is a web server for Windows
The web server itself is Apache – it can process requests from the user and send him the requested files, such as static HTML pages, pictures, files with CSS and JavaScript. Since this functionality is usually not enough and most users need support for dynamically created pages, PHP is connected to Apache as a web server module for these purposes. To store data, one or another database management system (DBMS) is used, usually MySQL or MariaDB. The DBMS is not an Apache or PHP module, it is a separate network service to which various programs can connect, it is important for us that PHP can work with MySQL.

These three components are the most typical of what is commonly referred to as a “web server”, although, again, the actual web server is just Apache. Quite often, phpMyAdmin is added to these three components. At its core, phpMyAdmin is a set of PHP scripts designed to make working with databases easier. phpMyAdmin is a web interface for working with databases.

In fact, you can add/enable other scripting languages to the web server, for example, Python, Perl, Ruby and others – the corresponding links will be given at the end of the article.

This is a step by step guide for installing a web server on Windows. Here it will be shown in detail how to install, configure and run Apache, MySQL, PHP and phpMyAdmin without using extraneous assemblies. This approach will allow you to have the latest versions of components and not worry about the security of running programs. After understanding how Apache modules work and connect, you can add any components you need, as well as customize it exactly to your needs.

## Download web server (Apache, MySQL, PHP and phpMyAdmin) for Windows 11

Apache: https://www.apachelounge.com/download/
PHP: https://windows.php.net/download/
MySQL: https://dev.mysql.com/downloads/mysql/
PHPMyAdmin: https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.zip

Install the VC_redist.x64.exe file.
https://aka.ms/vs/17/release/VC_redist.x64.exe

## Create the structure of the web server

  C:.
	├───bin
	│   ├───-Apache24
	│   │   └───conf
	│   ├───-mysql-8.0
	│   ├───-PHP
	│   └───-Sendmail
	├───certs
	├───data
	│   ├───DB
	│   │   └───data
	│   └───htdocs
	│       └───-phpmyadmin
	└───manage
  
## Installing Apache 2.4

	- Unzip the contents of the downloaded archive (to be more precise, only the Apache24 directory) to C:\Server\bin\.
	- Go to the c:\Server\bin\Apache24\conf\ directory and open the httpd.conf file with any text editor.
	
	Replace: 	Define SRVROOT "c:/Apache24"
	with:	Define SRVROOT "c:/Server/bin/Apache24"
	
	replace:	#LoadModule rewrite_module modules/mod_rewrite.so
	replace:	LoadModule rewrite_module modules/mod_rewrite.so
	
	replace:	#ServerName www.example.com:80
	with:	ServerName localhost

	replace
	```
  # AllowOverride controls what directives may be placed in .htaccess files.
	# It can be "All", "None", or any combination of the keywords:
	#   AllowOverride FileInfo AuthConfig Limit
	#
	AllowOverride none
  ```

	with
  ```
	# AllowOverride controls what directives may be placed in .htaccess files.
	# It can be "All", "None", or any combination of the keywords:
	#   AllowOverride FileInfo AuthConfig Limit
	#
	AllowOverride All
  ```

	replace:	DocumentRoot "${SRVROOT}/htdocs"
	with:	DocumentRoot "c:/Server/data/htdocs"
	
	replace:	<Directory "${SRVROOT}/htdocs">
	with:	<Directory "c:/Server/data/htdocs">

	replace:	DirectoryIndex index.html
	with:	DirectoryIndex index.php index.html index.htm
	
	That's it, Apache setup is complete!
	
	-> Win+x
		Windows Terminal (Admin)
		
	```c:\Server\bin\Apache24\bin\httpd.exe -k install```
  
	```c:\Server\bin\Apache24\bin\httpd.exe -k start```
