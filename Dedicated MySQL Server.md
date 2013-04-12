## Setting up two servers

One with Nginx and PHP, One with MySQL. Edit network interfaces to both.

    nano /etc/network/interfaces

Set network interfaces to both.

    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # The primary network interface
    auto eth0 eth0:0
    iface eth0 inet dhcp

    iface eth0:0 inet static
    address 192.168.176.242
    netmask 255.255.128.0

Restart

    /etc/init.d/networking restart

From the Linode, ping each of the default gateways listed on the "Remote Access" tab of the Linode Manager:

    ping 12.34.56.1
    ping 98.76.54.1

## Set alias for private network

Set private ip to some alias.

    192.168.176.242 app.bhuntr.com app
    192.168.158.132 mysql.bhuntr.com mysql

## On MySQL server

Edit /etc/mysql/my.cnf,

    $ nano /etc/mysql/my.cnf

Search for bind-address (Ctrl+W to search). Make it 0.0.0.0.

    bind-address = 0.0.0.0
    #skip-networking

Restart mysql,

    /etc/init.d/mysql restart

Enter mysql,

    mysql -u root -p

Grant permission (app is the private ip for php serve),

    GRANT ALL ON *.* TO 'USERNAME'@'app' IDENTIFIED BY 'PASSWORD';

## Testing connection

Create a php file on php server,

    nano test.php

Copy and paste the following, edit username and password for mysql.

    <?php

    $link = mysql_connect('MYSQL_SERVER_IP', 'USERNAME', 'PASSWORD');
    if (!$link) {
        die('Could not connect: ' . mysql_error());
    }
    echo 'Connected successfully';
    mysql_close($link);

    ?>

Run the php. (This will need php5-cli)

    php test.php

## Reference

Standalone MySQL Server
http://library.linode.com/databases/mysql/standalone-mysql-server
