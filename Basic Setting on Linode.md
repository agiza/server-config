## Set hostname to "dripstone"

    echo "reservoir" > /etc/hostname
    hostname -F /etc/hostname

## Set hosts

    nano /etc/hosts

Normal setting

    127.0.0.1          localhost.localdomain    localhost
    173.255.210.214    dripstone.bhuntr.com     dripstone
    74.207.245.122     reservoir.bhuntr.com     reservoir

If using MYSQL standalond server

    # Hostnames
    127.0.0.1          localhost.localdomain    localhost
    74.207.245.122     reservoir.bhuntr.com     reservoir

    # Private network
    192.168.176.242    app.bhuntr.com    app
    192.168.158.132    mysql.bhuntr.com   mysql

## Set timezone

    dpkg-reconfigure tzdata

## Add user hana

    adduser hana
    usermod -a -G sudo hana

## Set key login to ~/.ssh/authorized_keys and set permission

    chown -R hana:hana .ssh
    chmod 700 .ssh
    chmod 600 .ssh/authorized_keys

## Disable password login and root login

    sudo nano /etc/ssh/sshd_config
