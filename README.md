Ansible Role: Dokuwiki
=========

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki.svg)](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki)


This role installs, configures, hardens and/or upgrades Dokuwiki on a server.
The main focus is on provisioning Dokuwiki instances in a repeatable and secure
fashion. It does not install a webserver by itself, but it can add and enable an
Apache configuration file (which is included as template in the role).


Requirements
------------

A webserver having PHP installed. For Apache, we suggest using [PeterMosmans.apache2](https://github.com/PeterMosmans/ansible-role-apache2). If you prefer Nginx, please use [Nginxinc.nginx](https://github.com/nginxinc/ansible-role-nginx).


Role Variables
--------------



### /vars

Dependencies and package related variables are defined in `vars/*`. This includes all dependencies for dokuwiki and recommended plugins, as well as the location of all dokuwiki branches. You can select which branch to install with `dokuwiki_version`

| Variable                          | Comments                                                                |
| :---                              | :---                                                                    |
| `dokuwiki_stable_url`       | `dokuwiki_stable_url: https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz`       |
| `dokuwiki_old_stable_url`       | `dokuwiki_old_stable_url: https://download.dokuwiki.org/src/dokuwiki/dokuwiki-oldstable.tgz`       |
| `dokuwiki_development_url`       | `dokuwiki_development_url: http://github.com/splitbrain/dokuwiki/tarball/master`       |

### /defaults

All other available variables are listed below, along with default values. The default values are specified in `default/main.yml`.

| Variable                          | Comments                                                                |
| :---                              | :---                                                                    |
| `dokuwiki_server_name`       | Dokuwiki ServerName Directive. `dokuwiki_server_name: "localhost"`      |
| `dokuwiki_base`       | Dokuwiki base directory. `dokuwiki_base: /var/www/dokuwiki`      |
| `dokuwiki_savedir`       | Dokuwiki data directory. `dokuwiki_savedir: /var/www/dokuwiki/data`      |
| `dokuwiki_version`       | Version to install. `dokuwiki_version: stable`  |
| `dokuwiki_configure_apache2`       | When true, will deploy an Apache configuration (`dokuwiki.conf.j2`) to Apache, and enable the site. `dokuwiki_configure_apache2: false`.       |
| `dokuwiki_name`       | The 'internal' name of the dokuwiki, which is e.g. used for Apache logfiles and the cleanup cronjob. (when `dokuwiki_configure_apache2` is true). This allows the Ansible role to be used for multiple Dokuwiki sites on the same server. Default: `dokuwiki_name: dokuwiki`       |
| `dokuwiki_base`       | The local path where Dokuwiki will be installed. `dokuwiki_base: /var/www/html`       |
| `dokuwiki_user`       | The user owning the Dokuwiki files. `dokuwiki_user: root`       |
| `dokuwiki_group`       | The group owning the Dokuwiki files. `dokuwiki_group: www-data`       |
| `dokuwiki_plugins` (1)      | List of name / source pairs, with plugins to automatically install via dokuwiki's gittool.       |
| `dokuwiki_plugins_remove` (2)      |  A list of plugins to automatically remove upon installation or upgrade.       |
| `dokuwiki_templates` (3)      | A list of templates to automatically install.     |
| `dokuwiki_provision` (4)      | When true, apply configuration templates to provision Dokuwiki. `dokuwiki_provision: true`      |
| `dokuwiki_savedir`       | The directory where all files (content) will be stored. See [Dokuwiki Security - Move Directories out of DocRoot](https://www.dokuwiki.org/security#move_directories_out_of_docroot) for more info. `dokuwiki_savedir: /var/www/html/data`       |



(1) `dokuwiki_plugins`:  The current, opinionated list of default plugins is:
```Yaml
dokuwiki_plugins:
  - name: pagelist
  - name: fastwiki
  - name: edittable
  - name: dw2pdf
  - name: bookcreator
  - name: tag
  - name: discussion
  - name: dropfiles
  - name: color
  - name: nspages
  - name: sortablejs
```

(2) `dokuwiki_plugins_remove`: The default list of plugins to remove is:
```Yaml
dokuwiki_plugins_remove:
  - name: authad
  - name: authldap
  - name: authmysql
  - name: authpdo
  - name: authpgsql
  - name: info
  - name: popularity
```

(3) `dokuwiki_templates`: A list of templates to install
```Yaml
dokuwiki_templates:
 - name: bootstrap3
```



(4) `dokuwiki_provision`: If not specified or false, Dokuwiki will be unprovisioned, a default
installation. See below in the provisioning chapter which variables can be used
in the configuration templates. Note that when this variable is true, it will
(re-)template and overwrite the current Dokuwiki configuration.



Configuration templates
--------------
The following configuration files, located at `conf/*`, are templated:


| Template                          | Comments                                                                |
| :---                              | :---                                                                    |
| `acl.auth.php.j2`       | Dokuwiki ACL Config file      |
| `cleanup.sh.j2`       | Cron job to cleanup DokuWiki installations      |
| `local.php.j2`       | Dokuwiki's Main Configuration File - Local Settings      |
| `php-fpm.www.conf.j2`       | PHP FPM config file - altered to make this role php version agnostic      |
| `plugins.local.php.j2`       | Local plugin enable/disable settings - recommended to be used with `dokuwiki_plugins_removed`     |
| `users.auth.php.j2`       | If dokuwiki_users is defined, this file is provisioned with those local users    |
| `apache.dokuwiki.conf.j2`       | Apache config for dokuwiki      |
| `nginx.dokuwiki.conf.j2`       | Nginx config for dokuwiki      |


## Provisioning
The following variables will be used in the configuration templates, and therefore will only be applied if
`dokuwiki_provision` is set to `true`.

| Template                          | Comments                                                                |
| :---                              | :---                                                                    |
| `dokuwiki_title`       | The Dokuwiki title   |
| `dokuwiki_acl_all`       | The ACL bits for the default (@ALL) group. By default, only logged on users are allowed access (0).     |
| `dokuwiki_acl_user`       | The ACL bits for the user (@user) group. By default, users have upload, create, edit, and read permissions (8).     |
| `dokuwiki_disableactions`       | Which actions to disable. By default,  user auto registering is disabled.     |
| `dokuwiki_local` (1)   | A list of name / value configuration pairs to be added to the `local.php` configuration file.   |
| `dokuwiki_users ` (2)      | A list of users     |
| `dokuwiki_opt_license` (3)      | The content license to use. `dokuwiki_opt_license: "0"`     |



(1) `dokuwiki_local`: You can add additional parameters to the `local.php` configuration file, as seen here:
```Yaml
dokuwiki_local:
  - name: "['passcrypt']"
    value: 'bcrypt'
```

This will result in adding the following string to `/conf/local.php`:
```Yaml
$conf['passcrypt'] = 'bcrypt';
```

(2) `dokuwiki_users`: A list of users, containing the following name / value pairs:
```Yaml
- login: login
- hash: password hash
- name: full name
- email: email address
- groups: comma separated list of groups
```

Example:
```Yaml
dokuwiki_users:
- login: admin
  hash: "$2y$05$Nr3wFqH54gcdhxPK9easseLSVwLAnLTD2flYmQbAbCVIiiTU4mCjS"
  name: Administrator
  email: admin@admin
  groups: admin,user
```
This will result in adding the user admin to Dokuwiki, with the bcrypted password `admin`.


(3) `dokuwiki_opt_license`: The options for this are:
- cc-zero - [CC0 1.0 Universal](http://creativecommons.org/publicdomain/zero/1.0/)
- publicdomain - [Public Domain](http://creativecommons.org/licenses/publicdomain/)
- cc-by - [CC Attribution 4.0](International http://creativecommons.org/licenses/by/4.0/)
- cc-by-sa - [CC Attribution-Share Alike 4.0](International http://creativecommons.org/licenses/by-sa/4.0/)
- gnufdl - [GNU Free Documentation License 1.3](http://www.gnu.org/licenses/fdl-1.3.html)
- cc-by-nc - [CC Attribution-Noncommercial 4.0](International http://creativecommons.org/licenses/by-nc/4.0/)
- cc-by-nc-sa - [CC Attribution-Noncommercial-Share Alike 4.0](International http://creativecommons.org/licenses/by-nc-sa/4.0/)
- 0 - Do not show any license information



Dependencies
------------

None.


Example Playbook
----------------
```Yaml
- hosts: all
  become: yes
  become_method: sudo
  roles:
  - role: PeterMosmans.dokuwiki
  vars:
    dokuwiki_base: /var/www/html
    dokuwiki_configure_apache2: true
    dokuwiki_plugins:
      - name: tag
        src: https://github.com/dokufreaks/plugin-tag/tarball/master
      - name: pagelist
        src: https://github.com/dokufreaks/plugin-pagelist/tarball/master
    dokuwiki_plugins_remove:
      - name: authad
      - name: authldap
      - name: authmysql
      - name: authpdo
      - name: authpgsql
      - name: info
      - name: popularity
    dokuwiki_preconfigure: true
    dokuwiki_savedir: /var/www/html/data
    dokuwiki_template: bootstrap3
    dokuwiki_templates:
      - name: bootstrap3
        src: https://github.com/LotarProject/dokuwiki-template-bootstrap3/tarball/master
    dokuwiki_users:
      - login: admin
        hash: "$2y$05$Nr3wFqH54gcdhxPK9easseLSVwLAnLTD2flYmQbAbCVIiiTU4mCjS"
        name: Administrator
        email: admin@admin
        groups: admin,user
```

This example will install Dokuwiki to `/var/www/html`, and use `/var/www/html/data` as data directory.
It will install the plugins `tag` and `pagelist`, and remove the plugins `authad`, `authldap`, `authmysql`, `authpdo`, `authpgsql`, `info` and `popularity`.
It will install and use the `bootstrap3` theme, and grant the user `admin` with the password `admin` access to the wiki.
Moreover, it will configure and enable the Apache site.


License
-------

GPLv3


Author Information
------------------

Created by [Peter Mosmans](https://github.com/PeterMosmans). Suggestions, feedback and pull requests are always
welcome.

### Contributors

- [Jonas Heinrich](https://github.com/onny)
- [B Cacheira](https://github.com/cacheira)