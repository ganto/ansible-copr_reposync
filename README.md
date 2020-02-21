Ansible Role: ganto.copr-reposync
=================================

Setup a local package mirror of a [COPR](https://copr.fedorainfracloud.org/coprs/)
repository which is able to provide packages that will be deleted by COPR over
time. It can then be served to other machines with Web server.

Requirements
------------

This role must run on a Linux host that has the `dnf-plugins-core` and
`createrepo_c` packages available which is typically Fedora or CentOS 8.


License
-------

[GPLv3](https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29)

Author Information
------------------

The `ganto.copr-reposync` role was written by Reto Gantenbein | [e-mail](mailto:reto.gantenbein@linuxmonk.ch) | [GitHub](https://github.com/ganto)
