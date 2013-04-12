## Nginx Configuration

Replace all "miss-hana.com" to the url for wordpress.

    $ nano /etc/nginx/sites-available/miss-hana.com

Copy and pasted "ngnix-for-wordpress.conf". Then create a link.

    $ ln -s /etc/nginx/sites-available/miss-hana.com /etc/nginx/sites-enabled

Remove the default site if you haven't.

    $ rm /etc/nginx/sites-enabled/default

Reload nginx configuration

    $ service nginx reload

Create the root document for this website and start putting files

    $ mkdir -p /var/www/miss-hana.com

Test it:

    $ echo "<?php phpinfo(); ?>" > /var/www/miss-hana.com/index.php

And go to miss-hana.com, if it works, remove the file.

    $ rm /var/www/miss-hana.com/index.php

Restart Nginx.

    $ service nginx restart

## Create database

Login to mysql

    $ mysql -u root -p

Create and grant permission.

    $ mysql> create database DBNAME; grant all on DBNAME.* to 'DBUSER' identified by 'DBPASSWORD'; flush privileges;

### Import SQL from existing wordpress installation

Restore a database.

    $ mysql -u DBUSER -pDBPASSWORD DBNAME < DB_DUMP.sql

Edit database settings in wp-config.php.

    $ nano /var/www/miss-hana.com/wp-config.php

## References

EC2 + nginx + WordPress + Varnish + Ubuntu
http://blog.webjames.co.uk/ec2-nginx-wordpress-varnish-ubuntu/71/
