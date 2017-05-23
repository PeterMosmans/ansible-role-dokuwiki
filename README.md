Ansible Role: Dokuwiki
=========

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki.svg)](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki)


This role installs, configures, hardens and/or upgrades Dokuwiki on a server. The main focus is on preconfiguring Dokuwiki instances in a repeatable and secure fashion.
It does not install a webserver by itself.


Requirements
------------

A webserver having PHP installed, e.g. by using PeterMosmans.apache2


Role Variables
--------------

Available variables are listed below, along with default values. The default values are specified in `default/main.yml`.

**dokuwiki_url**: The URL where the (latest) version of Dokuwiki can be found. By default, it uses the official Dokuwiki source.
```
dokuwiki_url: https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
```


**dokuwiki_base**: The local path where Dokuwiki will be installed.
```
dokuwiki_base: /var/www/html
```


**dokuwiki_group**: The group owning the Dokuwiki files.
```
dokuwiki_group: www-data
```


**dokuwiki_plugins**: A list of name / source pairs, with plugins to automatically install. The sources need to point to tar or .tgz sources (e.g.). Example:
```
 - name: pagelist
   src: https://github.com/dokufreaks/plugin-pagelist/tarball/master
```

**dokuwiki_plugins_remove**: A list of plugins to automatically remove upon installation or upgrade.
Example:
```
dokuwiki_plugins_remove:
  - name: authad
  - name: authldap
  - name: authmysql
  - name: authpdo
  - name: authpgsql
  - name: info
  - name: popularity
```


**dokuwiki_preconfigure**: When true, apply configuration templates to preconfigure Dokuwiki. If not specified or false, Dokuwiki will be 'unconfigured'. See below in the preconfigure chapter which variables can be used in the configuration templates.
The following configuration files are templated:
- `/conf/acl.auth.php`
- `/conf/local.php`
- `/conf/plugins.local.php`
- `/conf/users.auth.php`

Example:
```
dokuwiki_preconfigure: true
```


**dokuwiki_savedir**: The directory where all files (content) will be stored.
```
dokuwiki_savedir: /var/www/html/data
```


**dokuwiki_templatess**: A list of name / source pairs, with templates to automatically install. The sources need to point to tar or .tgz sources (e.g.). Example:
```
dokuwiki_templates:
 - name: bootstrap3
   src: https://github.com/LotarProject/dokuwiki-template-bootstrap3/tarball/master
```


**dokuwiki_user**: The user owning the Dokuwiki files.
```
dokuwiki_user: root
```


## Preconfigure
The following variables will be used in the configuration templates (`local.php.j2`, `users.auth.php.j2`), and therefore will only be applied if `dokuwiki_preconfigure` is set to `true`.


**dokuwiki_title** The Dokuwiki title


**dokuwiki_local**: A list of name / value configuration pairs to be added to the `local.php` configuration file.
Example:
```
dokuwiki_local:
 - name: ['mytemplate']
   value: 'myvalue'
```
This will result in adding the following string to `/conf/local.php`:
```
$conf['mytemplate'] = 'myvalue';
```


**dokuwiki_users**: A list of users, containing the following name / value pairs:
```
- login: login
- hash: password hash
- name: full name
- email: email address
- groups: comma separated list of groups
```

Example:
```
dokuwiki_users:
- login: admin
  hash: "$2y$05$Nr3wFqH54gcdhxPK9easseLSVwLAnLTD2flYmQbAbCVIiiTU4mCjS"
  name: Administrator
  email: admin@admin
  groups: admin,user
```

This will result in adding the user admin to Dokuwiki, with the bcrypted password `admin`.

Dependencies
------------

None.


Example Playbook
----------------
```
- hosts: all
  become: yes
  become_method: sudo
  roles:
  - role: PeterMosmans.dokuwiki
  vars:
    dokuwiki_base: /var/www/html
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


License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans. Suggestions, feedback and pull requests are always welcome.
