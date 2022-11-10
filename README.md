# Ansible Role: Configure HTTP Proxy

This role configures http_proxy for the apt package manager and other tools (currently wget, curl, git, npm, pip, pecl and docker-ce), that do not rely on the env. variable HTTP_PROXY.
The tools are being configured if detected amongst installed packages.

Tested with :
  - Debian 10.x ✔️
  - Debian 11.x ✔️
  - Ubuntu 10.04.x ✔️
  - Ubuntu 22.04.x ✔️ 

## Requirements

- ansible-core >=2.13

## Role Variables

```yml
---
# General proxy settings
proxy_ip: 127.0.0.1
proxy_port: 8080
proxy_username: ''
proxy_password: ''
proxy_ignore: []
proxy_url: "http://{{ proxy_ip }}:{{ proxy_port }}"
# Setup apt
proxy_configure_apt: yes
# Setup wget in /etc/wgetrc
proxy_configure_wget: yes
proxy_wgetrc_path: /etc/wgetrc
# Setup curl in $HOME/.curlrc
# https://everything.curl.dev/cmdline/configfile#default-config-file
proxy_configure_curl: yes
# User list for ~/.npmrc file
proxy_npm_users:
  - root
# User list for pecl config-set cmd
proxy_pecl_users:
  - root
# User list for ~/.gitconfig
proxy_git_users:
  - root
# Dockerd settings
proxy_configure_docker: yes
proxy_docker_restart_on_change: yes

```

## Example Playbook

    # configure-http-proxy.yml
    - hosts: all
      gather_facts: yes
      become: yes
      roles:
        - name: rgsystemes.ansible-configure-http-proxy

      vars:
        proxy_ip: 10.0.0.1
        proxy_port: 3128
        proxy_npm_users:
          - root
          - web

License
-------

MIT / BSD
