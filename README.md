# logrotate

Install and configure logrotate on your system.

|Travis|GitHub|Quality|Downloads|
|------|------|-------|---------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-logrotate.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-logrotate)|[![github](https://github.com/robertdebock/ansible-role-logrotate/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-logrotate/actions)|[![quality](https://img.shields.io/ansible/quality/39060)](https://galaxy.ansible.com/robertdebock/logrotate)|[![downloads](https://img.shields.io/ansible/role/d/39060)](https://galaxy.ansible.com/robertdebock/logrotate)|

## Example Playbook

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    logrotate_frequency: daily
    logrotate_keep: 7
    logrotate_compress: yes
    logrotate_entries:
      - name: example
        path: "/var/log/example/*.log"
      - name: example-frequency
        path: "/var/log/example-frequency/*.log"
        frequency: weekly
      - name: example-keep
        path: "/var/log/example-keep/*.log"
        keep: 14
      - name: example-compress-yes
        path: "/var/log/example-compress/*.log"
        compress: yes
      - name: example-compress-no
        path: "/var/log/example-compress/*.log"
        compress: no

  roles:
    - role: robertdebock.logrotate
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.cron
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    - name: check if connection still works
      ping:
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## Role Variables

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for logrotate

# How often to rotate logs, either daily, weekly or monthly.
logrotate_frequency: weekly

# How many files to keep.
logrotate_keep: 4

# Should rotated logs be compressed??
logrotate_compress: yes
```

## Requirements

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.cron

```

## Context

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/logrotate.png "Dependency")

## Compatibility

This role has been tested on these [container images](https://hub.docker.com/):

|container|tags|
|---------|----|
|alpine|all|
|amazon|2018.03|
|el|7, 8|
|debian|buster, bullseye|
|fedora|31|
|opensuse|all|
|ubuntu|focal, bionic, xenial|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.



## Testing

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-logrotate) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-logrotate/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## License

Apache-2.0


## Author Information

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
