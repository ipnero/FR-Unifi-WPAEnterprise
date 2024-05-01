SO Ubuntu Server 22.04

sudo apt update
sudo apt upgrade -y

sudo apt install php apache2 php8.1-fpm freeradius libapache2-mod-php mariadb-server freeradius-mysql freeradius-utils php-{gd,common,mail,mail-mime,mysql,pear,db,mbstring,xml,curl} -y

sudo apt install phpmyadmin

sudo systemctl enable --now apache2 && sudo systemctl enable freeradius

sudo mysql_secure_installation


--

Enter current password for root (enter for none): enter

Switch to unix_socket authentication [Y/n] n

Change the root password? [Y/n] n

Remove anonymous users? [Y/n] y

Disallow root login remotely? [Y/n] y

Remove test database and access to it? [Y/n] y

Reload privilege tables now? [Y/n] y

--

sudo mysql -u root -p
CREATE DATABASE radius;
CREATE USER 'radius'@'localhost' IDENTIFIED by 'PASSWORD';
GRANT ALL PRIVILEGES ON radius.* TO 'radius'@'localhost';
FLUSH PRIVILEGES;
quit;

sudo su -
mysql -u root -p radius < /etc/freeradius/3.0/mods-config/sql/main/mysql/schema.sql
exit

Clonar el REPO con los archivos de configuracion

-- BASE DE DATOS --
- La tabla NAS
INSERT INTO `nas` (`id`, `nasname`, `shortname`, `type`, `ports`, `secret`, `server`, `community`, `description`) VALUES
(1, 'IP-AP', NULL, 'other', 2, 'Clave Compartida ejemplo "secret123"', '', NULL, 'Descripcion Ejemplo AP-Oficina 10');

- Los usuarios se agregan en la tabla radcheck 
INSERT INTO `radcheck` (`id`, `username`, `attribute`, `op`, `value`) VALUES
(1, 'Usuario', 'Cleartext-Password', ':=', 'Password');

- En la tabla radusergroup ( se asigna el usuario a un grupo qeu va a determinar en que vlan se conecta )
INSERT INTO `radusergroup` (`id`, `username`, `groupname`, `priority`) VALUES
(2, 'Usuario', 'Nombre del grupo Ejemplo vlan20',IMPORTATE va  0);

- En la tabla radgroupreply los valores para asignarlo a la vlan 
INSERT INTO `radgroupreply` (`id`, `groupname`, `attribute`, `op`, `value`) VALUES
(1, 'vlan20', 'Tunnel-Type', '=', '13'),
(2, 'vlan20', 'Tunnel-Medium-Type', '=', '6'),
(3, 'vlan20', 'Tunnel-Private-Group-Id', '=', '20' Este ultimo es el ID de vlan);

