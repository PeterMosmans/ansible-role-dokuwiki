Ansible Role: Dokuwiki
=========

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki.svg)](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki)


This role installs or upgrades Dokuwiki on a server.


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


**dokuwiki_preconfigure**: When true, apply configuration template to preconfigure Dokuwiki. If not specified or false, Dokuwiki will be 'unconfigured'.
```
dokuwiki_preconfigure: true
```


**dokuwiki_savedir**: The directory where all files (content) will be stored.
```
dokuwiki_savedir: /var/www/html/data
```


**dokuwiki_templatess**: A list of name / source pairs, with templates to automatically install. The sources need to point to tar or .tgz sources (e.g.). Example:
```
 - name: bootstrap3
   src: https://github.com/LotarProject/dokuwiki-template-bootstrap3/tarball/master
```


**dokuwiki_user**: The user owning the Dokuwiki files.
```
dokuwiki_user: root
```

The following variables will be used in the configuration template, and therefore will only be applied if `dokuwiki_preconfigure` is set to `true`.
```
dokuwiki_title
```

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
    - dokuwiki_base: /var/www/html
    - dokuwiki_savedir: /var/www/html/data
```
This example will install Dokuwiki to `/var/www/html`, and use `/var/www/html/data` as data directory.


License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans.
