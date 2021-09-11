# Ansible Role for deploy Harbor

An Ansible Role that installs [Harbor](https://github.com/goharbor/harbor) on Linux.

## Requirements

 - docker
 - docker-compose

## Role Variables

For a complete list see `defaults/main.yaml`.

By default, the role uses the IP of the current host to set `harbor_hostname`. You can override it.


```yaml
harbor_version: 2.3.2

harbor_hostname: harbor.example.com

harbor_datadir: /data

harbor_ssl_self_signed_generate: false # If you have you own certificate and private key, else true - generate self-signed certificate

harbor_install_dir: /opt

harbor_ui_url_protocol: "https"

harbor_exposed_http_port: 80
harbor_exposed_https_port: 443

harbor_projects:
  - project_name: test
    is_public: "false"
    content_trust: "false"
    prevent_vul: "true"
    severity: "high"
    auto_scan: "true"

harbor_self_registration: "off"
harbor_users:
    - username: user1
      email: user1@example.com
      realname: User User
      role_name: developer
      role_id: 2
      has_admin_role: true
```

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Installing and configuring Harbor
  hosts: registry
  vars_files:
    - certificate.yml
  vars:
    harbor_version: 2.3.2
    harbor_hostname: "registry.example.com"
    harbor_datadir: "/data"
    harbor_ui_url_protocol: "https"
    harbor_ssl_self_signed_generate: false
    habor_metrics_enabled: true
  roles:
    - role: harbor
```

After the playbook runs, you should be able to navigate to your host on port 80/443 and see Harbor's UI. You can login with `admin/Harbor12345`. If you changed the exposed ports, remember to use them instead of 80/443.

## Managing state 

For convenience, this role includes tasks to stop, start and restart the registry using docker-compose.

Here's a playbook created specifically to restart the registry:

```yaml
---
- hosts: registry
  tasks:
    - name: Restarting Harbor
      include_role:
        name: harbor
        tasks_from: restart
```

Running the playbook above effectively restarts all components of Harbor.

`tasks_from` can be `restart`, `start` and `stop`.

If you are running the playbook again to ensure the list of users but you have already changed the default admin password, you can set the `harbor_admin_password` variable somewhere or simply pass it in the command-line with `-e "harbor_admin_password=mypass"`.
