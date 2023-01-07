# php-mysql-server-setup
Apache + PHP + MySQL + PHPMyAdmin server setup in windows 11

[Ref: https://miloserdov.org/?p=7703](https://miloserdov.org/?p=7703)

## What is a web server for Windows
The web server itself is Apache – it can process requests from the user and send him the requested files, such as static HTML pages, pictures, files with CSS and JavaScript. Since this functionality is usually not enough and most users need support for dynamically created pages, PHP is connected to Apache as a web server module for these purposes. To store data, one or another database management system (DBMS) is used, usually MySQL or MariaDB. The DBMS is not an Apache or PHP module, it is a separate network service to which various programs can connect, it is important for us that PHP can work with MySQL.

These three components are the most typical of what is commonly referred to as a “web server”, although, again, the actual web server is just Apache. Quite often, phpMyAdmin is added to these three components. At its core, phpMyAdmin is a set of PHP scripts designed to make working with databases easier. phpMyAdmin is a web interface for working with databases.

In fact, you can add/enable other scripting languages to the web server, for example, Python, Perl, Ruby and others – the corresponding links will be given at the end of the article.

This is a step by step guide for installing a web server on Windows. Here it will be shown in detail how to install, configure and run Apache, MySQL, PHP and phpMyAdmin without using extraneous assemblies. This approach will allow you to have the latest versions of components and not worry about the security of running programs. After understanding how Apache modules work and connect, you can add any components you need, as well as customize it exactly to your needs.

## Download web server (Apache, MySQL, PHP and phpMyAdmin) for Windows 11

Apache: https://www.apachelounge.com/download

PHP: https://windows.php.net/download

MySQL: https://dev.mysql.com/downloads/mysql

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

Unzip the contents of the downloaded archive (to be more precise, only the Apache24 directory) to C:\Server\bin\.

Go to the c:\Server\bin\Apache24\conf\ directory and open the httpd.conf file with any text editor.

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

```
c:\Server\bin\Apache24\bin\httpd.exe -k install
```

```
c:\Server\bin\Apache24\bin\httpd.exe -k start
```

## Installing and configuring MySQL 8.0

Unpack the MySQL files into the bin directory (from the mysql-8.0.28-winx64.zip archive). Rename the mysql-8.0.28-winx64 folder to mysql-8.0 (for brevity). By the way, the unpacked mysql-8.0 folder takes up about 800 megabytes!

We go into this folder and create the my.ini file there. Now open this file with any text editor.

Add the following lines there:

[mysqld]

```
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 
datadir="c:/Server/data/DB/data/"
default_authentication_plugin=mysql_native_password
```

Save and close it.

The setup is completed, but you still need to perform initialization and installation, for this we open the command line as an administrator and sequentially enter there:

```
C:\Server\bin\mysql-8.0\bin\mysqld --initialize-insecure --user=root
```
```
C:\Server\bin\mysql-8.0\bin\mysqld --install
```
```
net start mysql
```

At the end of this process, automatically generated files should appear in the C:\Server\data\DB\data\ directory:

The MySQL service will now start every time you turn on Windows.

## Installing and configuring PHP 8

In the c:\Server\bin\ folder, create a PHP directory and copy the contents of the php-8.1.2-Win32-vs16-x64.zip archive into it.

In the c:\Server\bin\Apache24\conf\httpd.conf file, add the lines at the very end

```
PHPIniDir "C:/Server/bin/PHP"
AddHandler application/x-httpd-php .php
LoadModule php_module "C:/Server/bin/php/php8apache2_4.dll"
```

Then restart Apache
```c:\Server\bin\Apache24\bin\httpd.exe -k restart```

In the directory c:\Server\data\htdocs\ create a file called i.php

Copy to this file:
```php
<?php
phpinfo ();
```

### PHP 8 setup

Open the php.ini file with any text editor, look for the line

```;extension_dir = "ext"```

replace it with

```extension_dir = "C:\Server\bin\PHP\ext\"```

```
;extension=bz2
;extension=curl
;extension=ffi
;extension=ftp
;extension=fileinfo
;extension=gd
;extension=gettext
;extension=gmp
;extension=intl
;extension=imap
;extension=ldap
;extension=mbstring
;extension=exif      ; Must be after mbstring as it depends on it
;extension=mysqli
;extension=oci8_12c  ; Use with Oracle Database 12c Instant Client
;extension=oci8_19  ; Use with Oracle Database 19 Instant Client
;extension=odbc
;extension=openssl
;extension=pdo_firebird
;extension=pdo_mysql
;extension=pdo_oci
;extension=pdo_odbc
;extension=pdo_pgsql
;extension=pdo_sqlite
;extension=pgsql
;extension=shmop
```

Replace it with

```
extension=bz2
extension=curl
extension=ffi
extension=ftp
extension=fileinfo
extension=gd
extension=gettext
extension=gmp
extension=intl
extension=imap
extension=ldap
extension=mbstring
extension=exif      ; Must be after mbstring as it depends on it
extension=mysqli
;extension=oci8_12c  ; Use with Oracle Database 12c Instant Client
;extension=oci8_19  ; Use with Oracle Database 19 Instant Client
extension=odbc
extension=openssl
;extension=pdo_firebird
extension=pdo_mysql
;extension=pdo_oci
extension=pdo_odbc
extension=pdo_pgsql
extension=pdo_sqlite
extension=pgsql
extension=shmop
```

Save the file and restart Apache.

## Installing and configuring phpMyAdmin

Copy the contents of the **phpMyAdmin-5.1.2-all-languages.zip** archive to the **c:\Server\data\htdocs** directory. Rename phpMyAdmin-5.1.2-all-languages to **phpmyadmin** (for brevity)

In the directory **c:\Server\data\htdocs\phpmyadmin** create a file **config.inc.php** and copy it there:

```php
<?php
 
/* Servers configuration */
$i = 0;
 
/* Server: localhost [1] */
$i++;
$cfg['Servers'][$i]['verbose'] = '';
$cfg['Servers'][$i]['host'] = 'localhost';
$cfg['Servers'][$i]['port'] = '';
$cfg['Servers'][$i]['socket'] = '';
$cfg['Servers'][$i]['connect_type'] = 'tcp';
$cfg['Servers'][$i]['auth_type'] = 'cookie';
$cfg['Servers'][$i]['user'] = 'root';
$cfg['Servers'][$i]['password'] = '';
$cfg['Servers'][$i]['nopassword'] = true;
$cfg['Servers'][$i]['AllowNoPassword'] = true;
 
/* End of servers configuration */
 
$cfg['blowfish_secret'] = 'HACKWARE.RU ==== WRITE DOWN WHAT YOU WANT HERE';
$cfg['DefaultLang'] = 'en';
$cfg['ServerDefault'] = 1;
$cfg['UploadDir'] = '';
$cfg['SaveDir'] = '';
 
?>
```

In the browser we type http://localhost/phpmyadmin/

Enter root as the username. Leave the password field blank. If everything is done correctly, then everything should look like this:
![image](https://user-images.githubusercontent.com/51959574/211142403-c160e4e8-afe1-42d8-aec7-902d42cb0d37.png)

## Server usage and data backup

In the directory **c:\Server\data\htdocs\\** create folders and files, for example:

c:\Server\data\htdocs\test\ajax.php – this file, respectively, will be available at http://localhost/test/ajax.php, etc.
To create a full backup of all sites and databases, just copy the C:\Server\data\ directory.

Before updating modules, make a backup of the bin folder – in case of problems, it will be easy to roll back to previous versions.

When you reinstall the server or upgrade it, you must reconfigure the configuration files. If you have copies of these files, then the process can be greatly accelerated. It is recommended to back up the following files:

- c:\Server\bin\Apache24\conf\httpd.conf
- c:\Server\bin\mysql-8.0\my.ini
- c:\Server\bin\PHP\php.ini
- c:\Server\data\htdocs\\phpmyadmin\config.inc.php

All settings are stored in them.

## PHP tweaking
c:\Server\bin\PHP\php.ini

```
memory_limit = 128M
post_max_size = 8M
;default_charset = "UTF-8"	# sets the encoding (by default, the line is commented out)
upload_max_filesize = 2M
max_file_uploads = 20
max_execution_time = 30
```

## Setting up the mail plug
In the **C:\Server\bin\\** directory, create a new directory called **Sendmail**. Now in this directory create a **sendmail.php** file with the following content:

```
<?php
/*  PHP.INI
 *  [mail function]
 *  ;SMTP = localhost
 *  ;smtp_port = 25
 *  ;sendmail_from = me@example.com
 *  sendmail_path = php.exe sendmail.php --dir C:\mail --open
 */
  
$is_windows = stristr(PHP_OS, 'WIN');
$options = getopt("", ['open', 'prepend', 'file:', 'dir:']);
$is_open = isset($options['open']);
$is_prepend = isset($options['prepend']);
$is_onefile = isset($options['file']);
$mail_dir = isset($options['dir']) ? $options['dir'] : sys_get_temp_dir() . '/mail';
$file_name = isset($options['file']) ? $options['file'] : mkname();
$file_path = $mail_dir . '/' . $file_name;
  
if (!is_dir($mail_dir)) {
    mkdir($mail_dir, 0777, TRUE);
    if (!is_dir($mail_dir)) {
        die('Mail folder [' . $mail_dir . '] not created');
    }
}
  
$stream = $is_onefile ? PHP_EOL . str_repeat("-=", 10) . date('Y-m-d H:i:s') . str_repeat("-=", 10) . PHP_EOL : '';
while (false !== ($line = fgets(STDIN))) {
    //$stream .= ($is_windows ? str_replace("\n", PHP_EOL, $line) : $line);
    $stream .= $line;
}
  
if ($is_prepend && file_exists($file_path)) {
    $file_contents = file_get_contents($file_path);
    $stream .= $file_contents;
}
  
file_put_contents($file_path, $stream, $is_prepend ? 0 : FILE_APPEND);
  
if ($is_open && $is_windows) {
    pclose(popen("start /B notepad " . $file_path, "r"));
}
  
function mkname($i = 0) {
    global $mail_dir;
    $fn = 'mail_' . date('Y-m-d_H-i-s_') . $i . '.eml';
    return file_exists($mail_dir . '/' . $fn) ? mkname( ++$i) : $fn;
}
```

Open the PHP configuration file, it is located here **C:\Server\bin\PHP\php.ini**. And add one line there:
```
sendmail_path = "C:\Server\bin\PHP\php.exe C:\Server\bin\Sendmail\sendmail.php --dir C:\Server\bin\Sendmail\emails"
```

Save the file and restart the server. Great, now all sent emails will be stored in **C:\Server\bin\Sendmail\emails\\**

## Setting up cURL in Apache Web Server on Windows

For cURL to work in Apache on Windows you need:

- 1) Be sure to add PHP directory to PATH (system environment variables). How to do this is said a little higher: https://miloserdov.org/?p=7703#12
- 2) In the **C:\Server\bin\PHP\php.ini** file, the “**extension=curl**” line must be uncommented
- 3) You need to download the file **https://curl.haxx.se/ca/cacert.pem**, then in the **C:\Server\\** folder create a new folder called certs and move it to this new folder (**C:\Server\certs\\**) downloaded file.
- 4) In the file **C:\Server\bin\PHP\php.ini** find the line
	```;curl.cainfo =``` And replace it with ```curl.cainfo = C:\Server\certs\cacert.pem```
- 5) Restart the server.
