# ampacheNginxServerConfig

So these are my notes from when I set up the ampache server:
Steps to install ampache on centos7

Script to install:
Nginx
PHP
MariaDB
Ampache
Composer

I get a default Nginx page

moved ampanche server block configuration file into nginx's default config and commented out
the nginx default server block

created the ampance error log file at /var/log/ampache/ as nginx won't start without it

Now I get 404; 
I had to configure the path in the ampanche server block to point to the application @
/var/www/html/ampache  (I may want to consider changing this location to where 
nginx likes to store its default site files)

Now I get 502 Bad Gateway
I cannot access the php-fpm socket for some reason....

I have no luck with creating the missing folder /var/run/php/ where the socket is
supposed to run.  I was not able to restart php-fpm and have it create the socket file
there.

So I go to the php-fpm socket config file at /etc/php-fpm.d/www.conf and direct the listen
path to the path of where nginx is looking for the socket and....

restarted fpm first to see if that is what creates the socket (at runtime?)
and the answer is yes, I checked the /var/run/php/ location and the file is there.
Next i suspect I'll run into permissions errors...

But I did not, I ran into nginx not being able to find the socket as it is looking
for it under /var/run/php5-fpm.sock and I set the php-fpm to create/listen to 
/var/run/php/php5-fpm.sock.
So what I'll do is correct the php-fpm listen path and the extra php folder will not
get created needlessly

I did get a permissions error from nginx or is it really ampache that gets the error
message out?  And can I increase the error logging in a useful way?

Well, I changed the user and the permission to nginx in the php-fpm config file and I have the default Ampache site; however I got an error I would like to follow up with;

2019/04/22 00:38:53 [error] 5811#0: *2 open() "/var/www/html/ampache/manager/html" failed (2: No such file or directory), client: 190.64.204.122, server: my_server_name, request: "GET /manager/html HTTP/1.1", host: "172.104.10.72"

Now I need to configure the database with:


mysql_secure_installation
mysql -u root
once in mysql prompt:
CREATE USER '$USERNAME'@'localhost' IDENTIFIED BY '$PASSWORD';
I don't think I'll need to run this but it is here if I needed it
GRANT ALL PRIVILEGES ON database_name.* TO 'database_user'@'localhost';
or
GRANT ALL PRIVILEGES ON ampache.* TO '$USERNAME'@'localhost';
so I do need to do this after all...


I'll skip over the ampache.cfg.php file is writeable for now...

and see if I can configure the db to front end...

so it maybe that I did not give proper permissions. they are outlined at the top of the page...

ampache.cfg.php is writable		This tests whether PHP can write to config/. This is not strictly necessary, but will help streamline the installation process.

Ah no luck, I keep getting stopped at "Error: Config files not found or unreadable"  I at least got rid of the initial test of if php can write to config/.  I just chown "apache:apache" recursivly through the /var/www/html/ampache folder as well as chmod 755 everything as well.

Seems the database is good as the installer will create a database and a user.  But for some reason, it balks at the config file... maybe I'll try to chmod 777 just to see if it can work... brb

no that really did not work; next i forget what I did but I changed a whole directories permissions to nginx; I think it was and I'll change it back as the Linuxize site has some great instructions....

https://linuxize.com/post/install-php-7-on-centos-7/

So I did those instructions regarding permissions and the www.conf file.  Time to see if the changes worked for getting started with ampache and what do you know, it worked!  I also reverted the ampache folder back to root:root and changed it to chmod 755.
