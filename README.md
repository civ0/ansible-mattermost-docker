# ansible-role-mattermost-docker

Deploy [mattermost/docker](https://github.com/mattermost/docker) (team edition) with Ansible on an existing server.

- **Based on:** https://github.com/civ0/ansible-mattermost-docker
- **Mattermost version supported**: >= 7.2

Setup instructions are adapted from https://docs.mattermost.com/install/install-docker.html

# Table of contents

* [Use case for this role](#use-case-for-this-role)
* [Supported systems](#supported-systems)
	* [Requirements](#requirements)
* [Example playbooks](#example-playbooks)
	* [Installing Mattermost](#installing-mattermost)
	* [Upgrading Mattermost to a newer version](#upgrading-mattermost-to-a-newer-version)
* [Variables](#variables)

## Use case for this role
Use this role if you want to deploy Mattermost in docker on an existing server. It automatically configures a reverse proxy and HTTPS certificates.

## Supported systems
Currently only Debian systems are supported.

### Requirements
The following packages are must be installed
* `docker.io`
* `docker-compose`
* `git`

## Example playbooks
### Installing Mattermost
```
---
- name: Install Mattermost
  hosts: mattermost
  become: yes
  pre_tasks:
    - name: Install packages
      apt:
        name:
          - docker.io
          - docker-compose
          - git
  roles:
    - role: mattermost
      vars:
        mattermost_docker_install: yes
```

After the installation has finished, go to your freshly installed Mattermost in a browser and create the first user and team and also set the Site-URL. Changing the Site-URL requires a restart. An example playbook for restarting Mattermost is provided below.
```
---
- name: Restart Mattermost
  hosts: mattermost
  become: yes
  roles:
    - role: mattermost
      vars:
        mattermost_docker_restart: yes
```

### Upgrading Mattermost to a newer version
Uprades are done rebuilding the images from a new version of [mattermost/docker](https://github.com/mattermost/docker). The example playbook below creates a backup at `{{ mattermost_docker_postgres_database }}/backups` of you volumes and upgrades the Mattermost installation to `mattermost_docker_version`. The backups are not deleted automatically.
```
---
- name: Upgrade Mattermost
  hosts: mattermost
  become: true
  roles:
    - role: ansible-mattermost-docker
      vars:
        mattermost_docker_install: true
        mattermost_docker_upgrade: true
        mattermost_docker_version: 5.4.0
```

## Variables
The following variables are used by the role:
```
mattermost_docker_install_path: /opt/mattermost
mattermost_timezone: "UTC"
mattermost_docker_upgrade: false
mattermost_docker_install: false

# override these with vars from a vault
mm_postgres_user: mattermost
mm_postgres_pass: secure

# set an email for being contacted about
lets_encrypt_email: admin@example.com
```

