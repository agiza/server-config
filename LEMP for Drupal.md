## Update system

    $ apt-get update
    $ apt-get upgrade --show-upgraded

## Drush

Install Drush

    $ apt-get install drush

Upgrade to the latest version

    $ drush dl drush --destination='/usr/share'

## Upload progress

http://pecl.php.net/package/uploadprogress

    apt-get install php-pear
    apt-get install make
    pecl install uploadprogress

Edit php.ini

    nano /etc/php5/fpm/php.ini

Add

    extension=uploadprogress.so

Restart

    $ service php5-fpm restart


## Other packages for Drupal

Most of Drupal sites need GD

    $ apt-get install drush php5-gd

For Commerce Kickstart

    $ apt-get install php5-curl

Other packages that people suggested

    $ apt-get install php-apc
    $ apt-get install php5-common

Other packages

    $ apt-get install git


## Virtual Host Configuration

Edit the nginx config file

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

## Install Drupal directly

Go to the directory

    $ cd /var/www

Download Drupal 7 the latest version

    $ drush dl

Or, if you'd like to install from some distribution:

    $ drush dl commerce_kickstart

Move the files to www, note that the version of commerce kickstart might change.

    $ mv commerce_kickstart-7.x-2.5/* commerce_kickstart-7.x-2.5/.htaccess example.com/

Create the directories for Drupal. Do it in one go:
(replace example.com with your domain)

    $ mkdir /var/www/example.com/sites/default/files; chmod 777 /var/www/example.com/sites/default/files; cp /var/www/example.com/sites/default/default.settings.php /var/www/example.com/sites/default/settings.php; chmod 777 /var/www/example.com/sites/default/settings.php

Or one by one

    $ mkdir /var/www/example.com/sites/default/files
    $ chmod 777 /var/www/example.com/sites/default/files
    $ cp /var/www/example.com/sites/default/default.settings.php /var/www/example.com/sites/default/settings.php
    $ chmod 777 /var/www/example.com/sites/default/settings.php

Then go to example.com to install, after installation,

    $ chmod 444 /var/www/example.com/sites/default/settings.php

## Multisites

    $ cd /var/www/example.com
    $ mv default/settings.php example.com/
    $ mkdir sites/example.com/files
    $ chmod 777 sites/example.com/files

## Migrate Drupal for another server

On the server

    $ cd ~/.ssh
    $ ssh-keygen
    $ cat id_rsa.pub

Copy it!

    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCttw4qZn5Kw+B8aiB13UUyIprxQQ5CGBtUZDahHT/bw5kr1kw9e90nR+PkJcdduxTFpd69ggPTCCo2l18Akvadzvxcbxqaw412523wryfdshrtFSDRgo7pWBlO2T8WUqfs0ag6Nc+ZHQGpyaI88ZhNlGFECiqHp9mh8+HeDEyyCunnAl9zjibAMZ+fEXSZmycUovQa/+jsvsoMl2tOG1Qi5+JKFGgyb8cpnlD2svIAuyUNkd HANA@ip-10-132-193-87

Add to the other server

Log in via sftp

    $ cd /var/www
    $ sftp root@example.com

    > get /example/PUBLIC.tar.gz
    > get /example/DB_DUMP.sql

Uncompress

    $ tar -jxv -f PUBLIC.tar.gz -C /var/www/example.com
    $ mysql -u DBUSER -pDBPASS bhuntr < DB_DUMP.sql

## Varnish In Drupal

Edit VCL file and paste the code from varnish3-for-drupal7.vcl

    $ nano /etc/varnish/default.vcl

Get the key

    $ cat /etc/varnish/secret

Install varnish

    $ drush dl varnish; drush en varnish -y

And set up on drupal admin, remember to set the life

Edit Drupal 7 settings.php

    $ nano /var/www/example.com/sites/default/settings.php

Make sure these are not commented.

    $conf['reverse_proxy'] = TRUE;
    $conf['reverse_proxy_header'] = 'HTTP_X_CLUSTER_CLIENT_IP';
    $conf['reverse_proxy_addresses'] = array('127.0.0.1');

Add this to the end

    // Varnish
    $conf['cache_backends'][] = 'sites/all/modules/varnish/varnish.cache.inc';
    $conf['cache_class_cache_page'] = 'VarnishCache';
    // Drupal 7 does not cache pages when we invoke hooks during bootstrap.
    // This needs to be disabled.
    $conf['page_cache_invoke_hooks'] = FALSE;

Check response header to see if

    X-Drupal-Cache:HIT
    X-Varnish-Cache:HIT

## References

Configure Varnish 3 for Drupal 7
https://fourkitchens.atlassian.net/wiki/display/TECH/Configure+Varnish+3+for+Drupal+7

Caching in Drupal 7: Varnish, APC, and Memcache
http://www.rklawson.com/blog/2012/04/14/caching-drupal-7-varnish-apc-and-memcache

Linux – How to configure NGINX + PHP-FPM + Drupal 7.0
http://blog.celogeek.com/201209/202/how-to-configure-nginx-php-fpm-drupal-7-0/
