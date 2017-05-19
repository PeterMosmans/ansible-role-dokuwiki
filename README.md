Ansible Role: Dokuwiki
=========

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki.svg)](https://travis-ci.org/PeterMosmans/ansible-role-dokuwiki)


This role installs or upgrades Dokuwiki on a server.


Requirements
------------

A webserver having PHP installed, e.g. by using PeterMosmans.apache2


Role Variables
--------------

Available variables are listed below, along with default values.
```
dokuwiki_url: https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
dokuwiki_base: /var/www/html
dokuwiki_preconfigure: true
dokuwiki_title: "default Dokuwiki site"
dokuwiki_savedir: /var/www/html/data
dokuwiki_group: www-data
dokuwiki_user: root
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
