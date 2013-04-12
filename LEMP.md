## Create Instance and Connect

1. Launch Instance: choose "Ubuntu Server 12.04.1 LTS 64 bit"
2. Remember to create a key and put it on your computer
3. Right click the name of the instance, click connect
4. Choose a way to connect, for example:

       $ ssh -i aws.pem ubuntu@ec2-54-248-66-77.ap-northeast-1.compute.amazonaws.com

5. Login as root as it saves some energy to type sudo every time

       ubuntu@ip-x-x-x-x:~$ sudo -i

6. On your computer, edit hosts to test the websites by domain, on Mac

       $ sudo nano /etc/hosts

7. Substitue hana.com to any domain you want to use on trying

       x.x.x.x example.com


## Quick Quide

Don't want any noise? Use this. It will install everything.

    $ apt-get update; apt-get upgrade --show-upgraded; apt-get install nginx mysql-server mysql-client php5-fpm php5-mysql drush php5-gd --assume-yes;

    $ /etc/init.d/nginx start; mysql_secure_installation; drush dl drush --destination='/usr/share'; /etc/init.d/php5-fpm restart;

## Basic Installation

    $ apt-get update
    $ apt-get upgrade --show-upgraded

## LEMP Installation

    $ apt-get install nginx --assume-yes
    $ /etc/init.d/nginx start

    $ apt-get install mysql-server mysql-client --assume-yes
    $ mysql_secure_installation

    $ apt-get install php5-fpm php5-mysql --assume-yes
    $ /etc/init.d/php5-fpm restart

### Testing Nginx

Now go to the public DNS like:

    http://ec2-x-x-x-x.ap-northeast-1.compute.amazonaws.com/

You'll see "Welcome to nginx!".

## Basic Configuration

Edit default configurations

    $ nano /etc/nginx/sites-available/default

Add index.php to index:

    index index.html index.htm index.php;

Uncomment the location php section, for example:

    location ~ \.php$ {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini

    #       # With php5-cgi alone:
    #       fastcgi_pass 127.0.0.1:9000;
            # With php5-fpm:
            fastcgi_pass unix:/var/run/php5-fpm.sock;
            fastcgi_index index.php;
            include fastcgi_params;
    }

Edit PHP configuration

    $ nano /etc/php5/fpm/pool.d/www.conf

Find:

    listen = 127.0.0.1:9000

Replace it with:

    listen = /var/run/php5-fpm.sock

Restart Services

    $ /etc/init.d/php5-fpm restart; /etc/init.d/nginx restart

### Testing LEMP

Print phpinfo to nginx index page to test.

    $ echo "<?php phpinfo(); ?>" > /usr/share/nginx/www/index.php

Change the default "Welcome to nginx!" index.html to index2.html

    $ mv /usr/share/nginx/www/index.html /usr/share/nginx/www/index2.html

On your browser, go to the Public DNS.

    http://ec2-x-x-x-x.ap-northeast-1.compute.amazonaws.com/

You'll see phpinfo.


## Virtual Host Configuration

    $ nano /etc/nginx/sites-available/example.com

Copy and pasted the Nginx configuration for Drupal. Then create a link.

    $ ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled

Remove the default site

    $ rm /etc/nginx/sites-enabled/default

Reload nginx configuration

    $ /etc/init.d/nginx reload
    or
    $ service nginx reload

Create the root document for this website and start putting files

    $ mkdir -p /var/www/example.com

Test it

    $ echo "<?php phpinfo(); ?>" > /var/www/example.com/index.php

And go to example.com, if it works, remove the file.

    $ rm /var/www/example.com/index.php

Restart Nginx.

    $ /etc/init.d/nginx restart

## Create database

    $ mysql -u root -p

In one go

    mysql> create database DBNAME; grant all on DBNAME.* to 'USERNAME'@'HOSTNAME' identified by 'PASSWORD'; flush privileges;

Or one by one

    mysql> create database DBNAME
    mysql> grant all on USERNAME.* to 'USERNAME' identified by 'PASSWORD';
    mysql> flush privileges;
    mysql> quit

## Installing Varnish

Install Varnish

    $ apt-get install varnish

Check the version

    $ varnishd -V

Configure Varnish vcl

    $ nano /etc/varnish/default.vcl

Startup settings

    $ nano /etc/default/varnish

Uncomment these

    DAEMON_OPTS="-a :80
             -T localhost:6082
             -f /etc/varnish/default.vcl
             -S /etc/varnish/secret
             -s malloc,256M"

Edit Nginx

    $ nano /etc/nginx/sites-available/example.com

Add

    listen 8080;

    $ /etc/init.d/nginx reload

Restart services

    $ service nginx restart; service varnish restart; /etc/init.d/php5-fpm restart

Check which ports are open

    $ netstat -anp --tcp --udp | grep LISTEN

Should look like this

    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      604/sshd
    tcp        0      0 127.0.0.1:6082          0.0.0.0:*               LISTEN      1105/varnishd
    tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      1654/mysqld
    tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN      2072/memcached
    tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      1137/nginx
    tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1107/varnishd
    tcp6       0      0 :::22                   :::*                    LISTEN      604/sshd
    tcp6       0      0 :::80                   :::*                    LISTEN      1107/varnishd

See Varnish log

    $ varnishlog

Varnish stat

    $ varnishstat

Varnish history

    $ varnishhist


## Installing Memcached

Memcached

    $ apt-get install memcached libmemcached-dev php-pear php5-dev build-essential apache2-threaded-dev
    $ pecl install memcache
    $ echo "extension=memcache.so" > sudo /etc/php5/conf.d/memcache.ini

Memcached Status

    $ ps aux | grep memcache
    $ nmap -p 70-10000 127.0.0.1


## References

Configurations for Nginx & Varnish
http://todsul.com/configure-nginx-varnish

Install & Configure Varnish on Ubuntu Lucid Linux
http://www.mormanski.net/2010/08/14/install-configure-varnish-on-ubuntu-lucid-linux


