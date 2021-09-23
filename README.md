# Ansible Role: Configure HTTP Proxy

[![Build Status](https://travis-ci.com/rgsystemes/ansible-configure-http-proxy.svg?branch=master)](https://travis-ci.com/rgsystemes/ansible-configure-http-proxy)

This role configures http_proxy for the apt package manager and other tools (currently git, npm, pip, pecl and docker-ce), that do not rely on the env. variable HTTP_PROXY.
The tools are being configured if detected amongst installed packages.

## Installation

Assuming your directory layout follows Ansible's recommendations :
    
    project/
      |---group_vars/
      |---host_vars/
      |---roles/
            |---my_custom_role_1
                  |---handlers/
                  |---files/
                  |---templates/
                  |---tasks/
                  |---vars/
            |---my_custom_role_2
                  |---handlers/
                  |---files/
                  |---templates/
                  |---tasks/
                  |---vars/
            |---requirements.yml 
      |---site.yml
      |---some-playbook.yml

Inside your `roles/requirements.yml`

```yml
- src: https://github.com/rgsystemes/ansible-configure-http-proxy
  name: ansible-configure-http-proxy
  version: master # you can also specify a tag instead
```

Then you install via the following command (from your project's directory) : 
```bash
~/project$: ansible-galaxy install -r roles/requirements.yml
```

## Requirements

- Ansible >= 2.10

## Role Variables

```yml
---
proxy_ip: 127.0.0.1
proxy_port: 8080
proxy_username: '' # if both username and password are set, the generated proxy_url variable will include them as follows : http://user:pass@host:port
proxy_password: ''
proxy_ignore: [] # Proxy whitelist : array of values such as ['localhost', '192.168.0.0/16']
proxy_url: "http://{{ proxy_ip }}:{{ proxy_port }}"
proxy_configure_apt: yes # set to no if you wish apt not to be configured by the role
proxy_configure_wget: yes # set to no if you wish wget not to be configured by the role
proxy_wgetrc_path: /etc/wgetrc
proxy_npm_users: # since proxy configuration is set in a per-user file (~/.npmrc), the task needs to run for a list of users running npm commands on the host
  - root
proxy_git_users: # same as npm but lies in ~/.gitconfig
  - root
proxy_docker_users: # same as npm but lies in ~/.docker/config.json. File permissions must be set as it uses the template module
  - username: root
    group: root
proxy_docker_update_systemd: yes # update docker's systemd unit with env variables
proxy_docker_restart_on_change: yes # allows docker daemon to be restarted

```

## Troubleshooting

The tasks related to Docker require to write files in /tmp although looping over a list of unprivileged users. This will raise an error with default settings. You need to set `allow_world_readable_tmpfiles = True` in your **ansible.cfg** or set it as an environment variable at the playbook level (see example below). 

## Example Playbook

    # configure-http-proxy.yml
    - hosts: all
      gather_facts: yes
      become: yes
      roles:
        - rgsystemes.ansible-configure-http-proxy

      environment:
        ALLOW_WORLD_READABLE_TMPFILES: true

      vars:
        proxy_ip: 10.0.0.1
        proxy_port: 3128
        proxy_npm_users:
          - root
          - web
        proxy_docker_users:
          - username: root
            group: root
          - username: web
            group: ansible

License
-------

MIT / BSD