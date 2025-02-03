# Ansible Role: ganto.copr_reposync

[![CI](https://github.com/ganto/ansible-copr_reposync/actions/workflows/ci.yml/badge.svg)](https://github.com/ganto/ansible-copr_reposync/actions/workflows/ci.yml)
[![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-ganto.copr__reposync-blue.svg?style=flat&logo=ansible)](https://galaxy.ansible.com/ganto/copr_reposync)

Setup a local package mirror of a [COPR](https://copr.fedorainfracloud.org/coprs/) repository which is able to provide packages that will be deleted by COPR over time. It can then be served to other machines with Web server.

A synchronization job will be created as a 'oneshot' systemd service which can be executed through a (optional) systemd timer.


## Requirements

This role must run on a Linux host that uses systemd and has the `dnf-utils` and `createrepo_c` packages available which is typically Fedora or CentOS 8.


## Getting Started

1. Download this role via:

   ```
   $ ansible-galaxy install ganto.copr_reposync
   ```

2. Create a minimal playbook (e.g. called `reposync.yml`) to run this role:

   ```YAML
   - name: Setup COPR repository mirror
     hosts: localhost
     become: True
     gather_facts: False

     vars_prompt:
       - name: 'copr_reposync_name'
         prompt: 'Enter COPR repository name (e.g. ganto/jo)'
         private: no

     roles:
       - name: ganto.copr_reposync
   ```

3. Run this role (for example against the [ganto/jo](https://copr.fedorainfracloud.org/coprs/ganto/jo/) repository):

   ```
   $ ansible-playbook reposync.yml
   Enter COPR repository name (e.g. ganto/jo): ganto/jo

   PLAY [Setup COPR repository mirror] ************************
   ...
   ```

After playbook execution completed there will be a directory `/var/www/html/ganto/jo` that includes the package repositories for the most common distributions and releases e.g. `epel-7-x86_64` with all RPMs that are part of this repository including newly generated repository metadata so that this folder than be easily used as a yum/dnf repository by itself.

There is also a systemd timer that will regularly synchronize new package builds from COPR and regenerate the repository metadata.

```
$ sudo systemctl list-timers
NEXT                         LEFT          LAST                         PASSED      UNIT                               ACTIVATES
Sun 2020-02-23 06:00:00 UTC  5h 31min left Sun 2020-02-23 00:00:07 UTC  28min ago   reposync@jo-epel-7-x86_64.timer    reposync@jo-epel-7-x86_64.service
Sun 2020-02-23 06:00:00 UTC  5h 31min left Sun 2020-02-23 00:00:07 UTC  28min ago   reposync@jo-epel-8-x86_64.timer    reposync@jo-epel-8-x86_64.service
Sun 2020-02-23 06:00:00 UTC  5h 31min left Sun 2020-02-23 00:00:07 UTC  28min ago   reposync@jo-fedora-30-x86_64.timer reposync@jo-fedora-30-x86_64.service
Sun 2020-02-23 06:00:00 UTC  5h 31min left Sun 2020-02-23 00:00:07 UTC  28min ago   reposync@jo-fedora-31-x86_64.timer reposync@jo-fedora-31-x86_64.service
```


**Serving the COPR repository mirror**

1. Install and start a Web server. E.g.:

   ```
   $ sudo dnf install httpd
   $ sudo systemctl enable --run httpd
   ```

2. Download the repository file for your distribution. E.g.:

   ```
   $ sudo wget -O /etc/yum.repos.d/_copr-mirror_ganto-jo.repo http://localhost/ganto/jo/ganto-jo-fedora.repo
   ```

3. Now you can install every package version from the synchronized repository mirror:

   ```
   $ sudo dnf install jo-1.3
   ```


## Development

### Testing

There is a [Molecule](https://molecule.readthedocs.io/) test profile that can be used to verify the basic functionality of the role. The default scenario is using the [podman](https://podman.io/) container driver. If you prefer [docker](https://www.docker.com/) you can select the corresponding scenario with the `-s docker` molecule arguments.

1. Ensure you have the necessary dependencies installed (e.g. in a Python [venv](https://docs.python.org/3/tutorial/venv.html)):
```
pip install -r molecule/podman/requirements.txt         # for podman
# or
pip install -r molecule/docker/requirements.txt         # for docker
```
2. Run the test suite. When using docker, then you need to add the `-s docker` option. The other options in brackets are optional but useful if you need to troubleshoot issues:
```
molecule [-vvv] test [--destroy never][-s docker]
```
3. If you used `--destroy never` the container will remain after the test run and can be inspected interactively via:
```
podman exec -it <container-id> /bin/sh                  # for podman
# or
docker exec -it <container-id> /bin/sh                  # for docker
```
4. Once you're done with inspecting the instance it has to be deleted before a new test run can be started:
```
molecule destroy [-s docker]
```


## License

[Apache-2.0](https://spdx.org/licenses/Apache-2.0.html)


## Author Information

[Changelog](CHANGELOG.md)

The [ganto.copr_reposync](https://galaxy.ansible.com/ganto/copr_reposync) role was written and is maintained by:
- [Reto Gantenbein](https://linuxmonk.ch/) | [e-mail](mailto:reto.gantenbein@linuxmonk.ch) | [GitHub](https://github.com/ganto)
