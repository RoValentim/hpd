--- Aula 2 - Wordpress

mkdir hpd-wp
cd hpd-wp
mkdir roles
cd roles
cp /etc/hosts .

mkdir -p common/tasks
mkdir -p mysql/tasks
mkdir -p nginx/tasks
mkdir -p php-fpm/tasks
mkdir -p wordpress/tasks

mkdir mysql/handlers
mkdir nginx/handlers
mkdir php-fpm/handlers
mkdir wordpress/handlers

mkdir mysql/templates
mkdir nginx/templates
mkdir php-fpm/templates
mkdir wordpress/templates

cd common/tasks

vim main.yml

---
- name: Install libselinux-python
  yum: name=libselinux-python state=present

- name: Install epel-release
  yum: name=epel-release state=present

- name: Reload ansible_facts
  setup:



cd ../../mysql/tasks


vim main.yml

---
- name: Install MySQL package
  yum: name={{ item }} state=present
  with_items:
    - mysql-server
    - MySQL-python
    - libselinux-python
    - libsemanage-python

- name: Configure SELinux to start mysql on any port
  seboolean: name=mysql_connect_any state=true persistent=yes
  when: ansible_selinux.status == "enabled"

- name: Create MySQL configuration file
  template: src=my.cnf.j2 dest=/etc/my.cnf
  notify:
  - restart mysql

- name: Start MySQL Service
  service: name=mysqld state=started enabled=yes




cd ../handlers

vim main.yml

---
- name: restart mysql
  service: name=mysqld state=restarted



cd ../templates

vim my.cnf.j2

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
port={{ mysql_port }}

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid




cd ../../nginx/tasks

vim main.yml

---
- name: Install nginx
  yum: name=nginx state=present

- name: Copy nginx configuration for wordpress
  template: src=default.conf dest=/etc/nginx/conf.d/default.conf
  notify: restart nginx



cd ../handlers

vim main.yml

---
- name: restart nginx
  service: name=nginx state=restarted enabled=yes


cd ../templates

vim default.conf

server {
        listen       80 default_server;
        server_name  {{ server_hostname }};
        root /srv/wordpress/ ;

        client_max_body_size 64M;

        # Deny access to any files with a .php extension in the uploads directory
        location ~* /(?:uploads|files)/.*\.php$ {
                deny all;
        }

        location / {
                index index.php index.html index.htm;
                try_files $uri $uri/ /index.php?$args;
        }

        location ~* \.(gif|jpg|jpeg|png|css|js)$ {
                expires max;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_index index.php;
                fastcgi_pass  unix:/var/run/php-fpm/wordpress.sock;
                fastcgi_param   SCRIPT_FILENAME
                                $document_root$fastcgi_script_name;
                include       fastcgi_params;
        }
}



cd ../../php-fpm/tasks

vim main.yml

---
- name: Install php-fpm and deps
  yum: name={{ item }} state=present
  with_items:
    - php
    - php-fpm
    - php-enchant
    - php-IDNA_Convert
    - php-mbstring
    - php-mysql
    - php-PHPMailer
    - php-process
    - php-simplepie
    - php-xml
- name: Disable default pool
  command: mv /etc/php-fpm.d/www.conf /etc/php-fpm.d/www.disabled creates=/etc/php-fpm.d/www.disabled
  notify: restart php-fpm

- name: Copy php-fpm configuration
  template: src=wordpress.conf dest=/etc/php-fpm.d/
  notify: restart php-fpm



cd ../handlers

vim main.yml

---
- name: restart php-fpm
  service: name=php-fpm state=restarted




cd ../templates

vim wordpress.conf

[wordpress]
listen = /var/run/php-form/wordpress.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0600
user = wordpress
group = wordpress
pm = dynamic
pm.max_children = 10
pm.start_servers = 1
pm.min_spare_servers = 1
pm.max_spare_servers = 3
pm.max_requests = 500
chdir = /srv/wordpress/
php_admin_value[open_basedir] = /srv/wordpress/:/tmp




cd ../../wordpress/tasks

vim main.yml

---
- name: Download WordPress
  get_url: url=http://wordpress.org/wordpress-{{ wp_version }}.tar.gz dest=/srv/wordpress-{{ wp_version }}.tar.gz
           sha256sum="{{ wp_sha256sum }}"

- name: Extract archive
  command: chdir=/srv/ /bin/tar xvf wordpress-{{ wp_version }}.tar.gz creates=/srv/wordpress

- name: Add group "wordpress"
  group: name=wordpress

- name: Add user "wordpress"
  user: name=wordpress group=wordpress home=/srv/wordpress/

- name: Fetch random salts for WordPress config
  local_action: command curl https://api.wordpress.org/secret-key/1.1/salt/
  register: "wp_salt"
  become: no
  become_method: sudo

- name: Create WordPress database
  mysql_db: name={{ wp_db_name }} state=present

- name: Create WordPress database user
  mysql_user: name={{ wp_db_user }} password={{ wp_db_password }} priv={{ wp_db_name }}.*:ALL host='localhost' state=present

- name: Copy WordPress config file
  template: src=wp-config.php dest=/srv/wordpress/

- name: Change ownership of WordPress installation
  file: path=/srv/wordpress/ owner=wordpress group=wordpress state=directory recurse=yes setype=httpd_sys_content_t

- name: Start php-fpm Service
  service: name=php-fpm state=started enabled=yes



cd ../templates

vim wp-config.php

<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, WordPress Language, and ABSPATH. You can find more information
 * by visiting {@link http://codex.wordpress.org/Editing_wp-config.php Editing
 * wp-config.php} Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', '{{ wp_db_name }}');

/** MySQL database username */
define('DB_USER', '{{ wp_db_user }}');

/** MySQL database password */
define('DB_PASSWORD', '{{ wp_db_password }}');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */

{{ wp_salt.stdout }}

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * WordPress Localized Language, defaults to English.
 *
 * Change this to localize WordPress. A corresponding MO file for the chosen
 * language must be installed to wp-content/languages. For example, install
 * de_DE.mo to wp-content/languages and set WPLANG to 'de_DE' to enable German
 * language support.
 */
define('WPLANG', '');

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', false);

/** Disable Automatic Updates Completely */
define( 'AUTOMATIC_UPDATER_DISABLED', {{auto_up_disable}} );

/** Define AUTOMATIC Updates for Components. */
define( 'WP_AUTO_UPDATE_CORE', {{core_update_level}} );

/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
	define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');




cd ../../..
mkdir group_vars
cd group_vars

vim all

---
# Which version of WordPress to deploy
wp_version: 4.2.4
wp_sha256sum: 42ca594afc709cbef8528a6096f5a1efe96dcf3164e7ce321e87d57ae015cc82

# These are the WordPress database settings
wp_db_name: wordpress
wp_db_user: wordpress
wp_db_password: secret

# You shouldn't need to change this.
mysql_port: 3306

# This is used for the nginx server configuration, but access to the
# WordPress site is not restricted by a named host.
server_hostname: www.example.com

# Disable All Updates
# By default automatic updates are enabled, set this value to true to disable all automatic updates
auto_up_disable: false

#Define Core Update Level
#true  = Development, minor, and major updates are all enabled
#false = Development, minor, and major updates are all disabled
#minor = Minor updates are enabled, development, and major updates are disabled
core_update_level: true




cd ..

vim site.yml

---
- name: Install WordPress, MySQL, Nginx and PHP-FPM
  hosts: 10.142.0.17
  become: yes

  roles:
    - common
    - mysql
    - nginx
    - php-fpm
    - wordpress



ansible-playbook -i hosts site.yml

wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
rpm -ivh mysql57-community-release-el7-11.noarch.rpm
