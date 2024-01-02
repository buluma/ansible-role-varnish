# Ansible role [varnish](https://galaxy.ansible.com/ui/standalone/roles/buluma/varnish/documentation)

Varnish for Linux.

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-varnish/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-varnish/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-varnish.svg)](https://github.com/buluma/ansible-role-varnish/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-varnish.svg)](https://github.com/buluma/ansible-role-varnish/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-varnish.svg)](https://github.com/buluma/ansible-role-varnish/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/varnish)](https://galaxy.ansible.com/ui/standalone/roles/buluma/varnish/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-varnish/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true

  roles:
    - role: buluma.varnish
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-varnish/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: buluma.bootstrap
    - role: buluma.systemd
    - role: buluma.httpd

  tasks:
    - name: update apt cache.
      apt: update_cache=true cache_valid_time=600
      when: ansible_os_family == 'Debian'

    - name: ensure build dependencies are installed (RedHat 7+).
      ansible.builtin.yum:
        name:
          - logrotate
          - systemd-sysv
        state: present
      when:
        - ansible_os_family == 'RedHat'
        - ansible_distribution_major_version >= '7'

    - name: ensure build dependencies are installed (RedHat < 7).
      ansible.builtin.yum:
        name: logrotate
        state: present
      when:
        - ansible_os_family == 'RedHat'
        - ansible_distribution_major_version < '7'

    - name: ensure curl is installed.
      package: name=curl state=present
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-varnish/blob/master/defaults/main.yml):

```yaml
---
varnish_package_name: "varnish"
varnish_version: "7.1"

varnish_use_default_vcl: true
varnish_default_vcl_template_path: default.vcl.j2

varnish_default_backend_host: "127.0.0.1"
varnish_default_backend_port: "8080"

varnish_listen_address: ""
varnish_listen_port: "80"
varnish_secret: "14bac2e6-1e34-4770-8078-974373b76c90"
varnish_config_path: /etc/varnish
varnish_limit_nofile: 131072

varnish_admin_listen_host: "127.0.0.1"
varnish_admin_listen_port: "6082"

varnish_storage: "file,/var/lib/varnish/varnish_storage.bin,256M"
varnish_pidfile: /run/varnishd.pid

varnishd_extra_options: ""

varnish_enabled_services:
  - varnish

# Make sure Packagecloud repo is used on RHEL/CentOS.
varnish_packagecloud_repo_yum_repository_priority: "1"

# Only used on RedHat / CentOS.
varnish_yum_repo_baseurl: https://packagecloud.io/varnishcache/{{ varnish_packagecloud_repo }}/el/{{ ansible_distribution_major_version|int }}/$basearch

# Only used on Debian / Ubuntu.
varnish_apt_repo: deb https://packagecloud.io/varnishcache/{{ varnish_packagecloud_repo }}/{{ ansible_distribution | lower }}/ {{ ansible_distribution_release }} main

# Optionally define additional backends.
# varnish_backends:
#   apache:
#     host: 10.0.2.2
#     port: 80
#   nodejs:
#     host: 10.0.2.3
#     port: 80

# Optionally define vhosts pointed at different backends.
# varnish_vhosts:
#   example.com:
#     backend: apache
#   nodejs.example.com:
#     backend: nodejs
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-varnish/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.systemd](https://galaxy.ansible.com/buluma/systemd)|[![Ansible Molecule](https://github.com/buluma/ansible-role-systemd/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-systemd/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-systemd.svg)](https://github.com/shadowwalker/ansible-role-systemd)|
|[buluma.core_dependencies](https://galaxy.ansible.com/buluma/core_dependencies)|[![Ansible Molecule](https://github.com/buluma/ansible-role-core_dependencies/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-core_dependencies/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-core_dependencies.svg)](https://github.com/shadowwalker/ansible-role-core_dependencies)|
|[buluma.httpd](https://galaxy.ansible.com/buluma/httpd)|[![Ansible Molecule](https://github.com/buluma/ansible-role-httpd/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-httpd/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-httpd.svg)](https://github.com/shadowwalker/ansible-role-httpd)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-varnish/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|focal, bionic|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|bullseye|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-varnish/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-varnish/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-varnish/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)

