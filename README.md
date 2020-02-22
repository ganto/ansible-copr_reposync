Ansible Role: ganto.copr-reposync
=================================

Setup a local package mirror of a [COPR](https://copr.fedorainfracloud.org/coprs/)
repository which is able to provide packages that will be deleted by COPR
over time. It can then be served to other machines with Web server.

A synchronization job will be created as a 'oneshot' systemd service which
can be executed through a (optional) systemd timer.


Requirements
------------

This role must run on a Linux host that uses systemd and has the `dnf-plugins-core`
and `createrepo_c` packages available which is typically Fedora or CentOS 8.


Getting Started
---------------

1. Download this role via:

        $ ansible-galaxy install ganto.copr-reposync


2. Create a minimal playbook (e.g. called `reposync.yml`) to run this role:

        - name: Setup COPR repository mirror
          hosts: localhost
          become: True
          gather_facts: False
          
          vars_prompt:
            - name: 'copr_reposync_name'
              prompt: 'Enter COPR repository name (e.g. ganto/jo)'
              private: no
          
          roles:
            - name: ganto.copr-reposync

3. Run this role (for example against the [ganto/jo](https://copr.fedorainfracloud.org/coprs/ganto/jo/)
   repository):

        $ ansible-playbook reposync.yml
        Enter COPR repository name (e.g. ganto/jo): ganto/jo
        
        PLAY [Setup COPR repository mirror] ************************
        ...

After playbook execution completed there will be a directory `/var/www/html/ganto/jo`
that includes the package repositories for the most common distributions and
releases e.g. `epel-7-x86_64` with all RPMs that are part of this repository
including newly generated repository metadata so that this folder than be
easily used as a yum/dnf repository by itself.

There is also a systemd timer that will regularly synchronize new package builds
from COPR and regenerate the repository metadata.


License
-------

[GPLv3](https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29)

Author Information
------------------

The `ganto.copr-reposync` role was written by Reto Gantenbein | [e-mail](mailto:reto.gantenbein@linuxmonk.ch) | [GitHub](https://github.com/ganto)
