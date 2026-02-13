# [Ansible role logrotate](#ansible-role-logrotate)

Install and configure logrotate on your system.

|GitHub|GitLab|Downloads|Version|
|------|------|---------|-------|
|[![github](https://github.com/robertdebock/ansible-role-logrotate/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-logrotate/actions)|[![gitlab](https://gitlab.com/robertdebock-iac/ansible-role-logrotate/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-logrotate)|[![downloads](https://img.shields.io/ansible/role/d/robertdebock/logrotate)](https://galaxy.ansible.com/robertdebock/logrotate)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-logrotate.svg)](https://github.com/robertdebock/ansible-role-logrotate/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/robertdebock/ansible-role-logrotate/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true

  vars:
    logrotate_frequency: daily
    logrotate_keep: 7
    logrotate_compress: true
    logrotate_create: false
    logrotate_entries:
      - name: example
        path: "/var/log/example/*.log"
      - name: example-frequency
        path: "/var/log/example-frequency/*.log"
        frequency: weekly
      - name: example-keep
        path: "/var/log/example-keep/*.log"
        keep: 14
      - name: example-compress
        path: "/var/log/example-compress/*.log"
        compress: true
      - name: example-copylog
        path: "/var/log/example-copylog/*.log"
        copylog: true
      - name: example-copytruncate
        path: "/var/log/example-copytruncate/*.log"
        copytruncate: true
      - name: example-delaycompress
        path: "/var/log/example-delaycompress/*.log"
        delaycompress: true
      - name: example-script
        path: "/var/log/example-script/*.log"
        postrotate: "true"  ## Normally this command would be `killall -HUP some_process_name`
      - name: btmp
        path: /var/log/btmp
        missingok: true
        frequency: monthly
        create: true
        create_mode: "0660"
        create_user: root
        create_group: utmp
        dateext: true
        dateformat: "-%Y-%m-%d"
        keep: 1
      - name: wtmp
        path: /var/log/wtmp
        missingok: true
        frequency: monthly
        create: true
        create_mode: "0664"
        create_user: root
        create_group: utmp
        minsize: 1M
        maxsize: 128M
        dateext: true
        dateformat: "-%Y%m%d"
        keep: 1
      - name: example-su
        path: "/var/log/example-su/*.log"
        su: root root
        keep: 4
        frequency: weekly
      - name: dnf
        path: /var/log/hawkey.log
        missingok: true
        notifempty: true
        keep: 4
        frequency: weekly
        create: true
      - name: example-sharedscripts
        path: "/var/log/example-sharedscripts/*.log"
        sharedscripts: true
      - name: example-dateyesterday
        state: present
        path: "/var/log/example-dateyesterday/*.log"
        dateyesterday: true
      - name: example-prerotate
        path: "/var/log/example-prerotate/*.log"
        prerotate: touch /tmp/logrotate-prerotate
      - name: example-postrotate-list
        path: "/var/log/example-postrotate-list/*.log"
        postrotate:
          - touch /tmp/logrotate-postrotate-1
          - touch /tmp/logrotate-postrotate-2
      - name: example-prerotate-list
        path: "/var/log/example-prerotate-list/*.log"
        prerotate:
          - touch /tmp/logrotate-prerotate-list-1
          - touch /tmp/logrotate-prerotate-list-2
      - name: example-paths
        paths:
          - "/var/log/example-paths/a.log"
          - "/var/log/example-paths/b.log"
        frequency: weekly
        keep: 4
      - name: example-absent
        state: absent
        # Negative numbers work on some distributions: `error: example-negative:10 bad rotation count '-1'\`
        # - name: example-negative
        #   path: "/var/log/example-keep-negative/*.log"
        #   keep: -1

  roles:
    - role: robertdebock.logrotate
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/robertdebock/ansible-role-logrotate/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: false

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.cron

  post_tasks:
    - name: Create log directory
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
      loop:
        - /var/log/example
        - /var/log/example-frequency
        - /var/log/example-keep
        - /var/log/example-compress
        - /var/log/example-copylog
        - /var/log/example-copytruncate
        - /var/log/example-delaycompress
        - /var/log/example-script
        - /var/log/example-sharedscripts
        - /var/log/example-dateyesterday
        - /var/log/example-prerotate
        - /var/log/example-postrotate-list
        - /var/log/example-prerotate-list
        - /var/log/example-su
        - /var/log/example-paths

    - name: Create log file
      ansible.builtin.copy:
        dest: "{{ item }}"
        content: "example"
        mode: "0644"
      loop:
        - /var/log/example/app.log
        - /var/log/example-frequency/app.log
        - /var/log/example-keep/app.log
        - /var/log/example-compress/app.log
        - /var/log/example-copylog/app.log
        - /var/log/example-copytruncate/app.log
        - /var/log/example-delaycompress/app.log
        - /var/log/example-script/app.log
        - /var/log/example-sharedscripts/app.log
        - /var/log/example-dateyesterday/app.log
        - /var/log/example-prerotate/app.log
        - /var/log/example-postrotate-list/app.log
        - /var/log/example-prerotate-list/app.log
        - /var/log/example-su/app.log
        - /var/log/example-paths/a.log
        - /var/log/example-paths/b.log
        - /var/log/btmp
        - /var/log/wtmp
        - /var/log/hawkey.log
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/robertdebock/ansible-role-logrotate/blob/master/defaults/main.yml):

```yaml
---
# defaults file for logrotate

# How often to rotate logs, either hourly, daily, weekly or monthly.
logrotate_frequency: weekly

# How many files to keep.
logrotate_keep: 4

# Should rotated logs be compressed??
logrotate_compress: true

# Use date extension on log file names
logrotate_dateext: false

# Enable the global `create` directive in /etc/logrotate.conf.
logrotate_create: true

# User/Group for rotated log files (Loaded by OS-Specific vars if found, or and can be set manually)
logrotate_user: "{{ _logrotate_user[ansible_facts['distribution']] | default(_logrotate_user['default']) }}"
logrotate_group: "{{ _logrotate_group[ansible_facts['distribution']] | default(_logrotate_group['default']) }}"

# Set the state of the service
logrotate_service_state: "started"
logrotate_service_enabled: true
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/robertdebock/ansible-role-logrotate/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions)|[![Build Status GitLab](https://gitlab.com/robertdebock-iac/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-bootstrap)|
|[robertdebock.cron](https://galaxy.ansible.com/robertdebock/cron)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-cron/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-cron/actions)|[![Build Status GitLab](https://gitlab.com/robertdebock-iac/ansible-role-cron/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-cron)|

## [Context](#context)

This role is part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/ansible-role-logrotate/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|[Alpine](https://hub.docker.com/r/robertdebock/alpine)|all|
|[EL](https://hub.docker.com/r/robertdebock/enterpriselinux)|all|
|[Debian](https://hub.docker.com/r/robertdebock/debian)|all|
|[Fedora](https://hub.docker.com/r/robertdebock/fedora)|all|
|[Ubuntu](https://hub.docker.com/r/robertdebock/ubuntu)|all|

The minimum version of Ansible required is 2.12, tests have been done on:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them on [GitHub](https://github.com/robertdebock/ansible-role-logrotate/issues).

## [License](#license)

[Apache-2.0](https://github.com/robertdebock/ansible-role-logrotate/blob/master/LICENSE).

## [Author Information](#author-information)

[robertdebock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
