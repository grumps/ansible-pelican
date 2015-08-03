Role Name
=========

This role installs pelican in a virtual environment, and generates html into a web root directory. The role doesn’t install a webserver, and expects there to be another role to manage the webserver. The role expectes the pelican application to be in a git repository such as Github or Bitbucket. We generate HTML output using publishconf.py.

Requirements
------------

This role expect keys for deployment to be utilized via an [ssh agent forwarded][1] or already existing for the `remote_user`.  This role requires `publishconf.py` to generate the HTML from Pelican. The role *does not* utilize a template to generate the file so it must live in the repository. Additionally the role requires `requirments.txt` to live in the root directory and for it to include Pelican and it's dependencies because they are install via pip.

Role Variables
--------------

A pelicanapp dictionary app must exist for each site as follows and do not have defaults. _note this will deploy multiple sites but they currently all need to use the same git version..._
```
pelicanapp:
    - {
        name: 'ofmax.li',
        repo: 'git@github.com:grumps/ofmax.li.git',
       }
build_user: 'buildmaster'
```
* `pelicanapp.name` should be the name of the site, this will be used for directory names.
* `pelicanapp.repo` is uri for the github repo.
* `build_user` this is the name of linux user, used to generate the HTML. This user will be given write permssions for the `web_root/pelicanapp.name` directory.

"Role" level default variables:
* `build_path_prefix: '/home'` This should probably not be changed, instead if you wish to not use the `build_user` home directory, then override `build_path`
* `build_path: "{{ build_path_prefix }}/{{ build_user }}"` This is where the pelican app will be installed. It will be installed in `build_path/pelicanapp.name/application`
* `web_root: "/var/www"` This dir will be concatenated with `pelicanapp.name` HTML will live here.
* `git_version: "master"` This is the version that's checked out.

_note `vars/main.yml`_ contains `application` but is set to `pelicanapp`. Dont' mess with that :-)

Dependencies
------------

You should have a role to handle the webserver.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```
# static_site.yml
- hosts: static_site
  sudo: True
  roles:
    - ansible-pelican
```

```
# group_vars/static_site.yml
pelicanapp:
    - {
        name: 'ofmax.li',
        repo: 'git@github.com:grumps/ofmax.li.git',
       }
build_user: 'zul'
```
License
-------

GPLv3

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).

[1]: https://developer.github.com/guides/using-ssh-agent-forwarding/