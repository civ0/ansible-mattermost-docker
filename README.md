# ansible-mattermost-docker
Deploy [mattermost/mattermost-docker](https://github.com/mattermost/mattermost-docker) with Ansible on an existing server

## Use case for this role
Use this role if you want to deploy Mattermost in docker on an existing server. The web container with the nginx reverse proxy is not used, you have to configure your own reverse proxy.

## Supported systems
Currently only Ubuntu is suppoerted.
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
    - role: ansible-mattermost-docker
      vars:
        mattermost_docker_install: yes
        mattermost_docker_version: 5.4.0
        mattermost_docker_update_volume_permissions: yes # set this to yes for Mattermost version greater than 4.9.x
      tags:
        - mattermost
```
After the installation has finished, go to your freshly installed Mattermost in a browser and create the first user and team and also set the Site-URL. Changing the Site-URL requires a restart. An example playbook for restarting Mattermost is provided below.
```
---
- name: Restart Mattermost
  hosts: mattermost
  become: yes
  roles:
    - role: ansible-mattermost-docker
      vars:
        mattermost_docker_restart: yes
```
### Upgrading Mattermost to a newer version
Uprades are done rebuilding the images from a new version of [mattermost/mattermost-docker](https://github.com/mattermost/mattermost-docker). The example playbook below creates a backup at `{{ mattermost_docker_postgres_database }}/backups` of you volumes and upgrades the Mattermost installation to `mattermost_docker_version`. The backups are not deleted automatically.
```
---
- name: Upgrade Mattermost
  hosts: mattermost
  become: yes
  roles:
    - role: ansible-mattermost-docker
      vars:
        mattermost_docker_install: yes
        mattermost_docker_upgrade: yes
        mattermost_docker_version: 5.4.0
        mattermost_docker_update_volume_permissions: yes # set this to yes for Mattermost version greater than 4.9.x
      tags:
        - mattermost
```

## Variables
The following variables are used by the role:
```
mattermost_docker_version: 5.4.0
mattermost_docker_upgrade: no
mattermost_docker_install_path: /opt/mattermost
mattermost_docker_team_edition: yes
mattermost_docker_update_volume_permissions: yes # this is needed for mattermost-docker > 4.9
mattermost_docker_uid: 2000
mattermost_docker_gid: 2000
mattermost_docker_http_port: 8000
mattermost_docker_https_port: 8443

# override these with vars from a vault
mattermost_docker_postgres_user: mattermost
mattermost_docker_postgres_password: secure
mattermost_docker_postgres_database: mattermost
```

