Ansible Role make_vm_template_unique
=========

[![Galaxy](https://img.shields.io/badge/galaxy-UdelaRInterior.make__vm__template__unique-blue.svg)](https://galaxy.ansible.com/udelarinterior/make_vm_template_unique)

This role allows to convert a VM created from a template into a unique VM (destined for production) with its own IP addresses, users->passwords, hostname and SSH access.

A utility for continuous flow with Ansible, from host creation to production-ready service.


Requirements
------------

A VM running on a known address and with superuser access by password is required.

Typically, that VM will be created from a template, and the required data will be that used when the template was created.

Role Variables
--------------

```yaml
# Timeout to verify if the unique VM is already configured. (Lower values
# can speed up the execution of the role, but also generate false positives)
vm_unique_connection_test_timeout: 15


########################
### Template VM data ###
########################

# Data needed to make an ssh-copy-id to the VM template
vm_template_host: template.mydomain.com
vm_template_login_user: root
vm_template_login_pass: my_super_strong_pass


######################
### Unique VM data ###
######################

# Local public key to copy to unique VM for later access without password
vm_unique_ssh_identity_file: ~/.ssh/id_rsa.pub

# System hostname
vm_unique_hostname: "{{ inventory_hostname.split('.')[0] }}"

# Collection of user->password to which it's recommended to change the default password for security
vm_unique_passwords_to_change:
  - username: root
    password: "{{ 'unique_and_strong_pass' | password_hash('sha512', 'my_custom_salt') }}"

# Path to SSH server configuration file. If kept empty, the configuration task will be skipped
vm_unique_ssh_config_path: '' # e.g. '/etc/ssh/sshd_config'
# Configure the SSH login type allowed for root
vm_unique_ssh_permit_root_login: prohibit-password  # prohibit-password - no - yes

# Source template and destination configuration file for network interfaces. Typical example
# for Debian-based distributions. Look at the "templates" folder for more examples.
vm_unique_net_config_template: etc/network/interfaces.j2
vm_unique_net_config_path: /etc/network/interfaces
# To avoid problems (IP conflicts), this list is empty by default, causing the related task to be skipped
vm_unique_net_interfaces: []
  # - name: eth0
  #   ip4: 192.168.33.10
  #   gw4: 192.168.33.1
  #   netmask4: 24
  #   ip6: 200:db8::10
  #   gw6: 200:db8::1
  #   netmask6: 64

  # - name: eth1
  #   ip6: 200:db9::10
  #   gw6: 200:db9::1
  #   netmask6: 64
```

Dependencies
------------

No dependencies.

Example Playbook
----------------

```yaml
  - hosts: vm_from_template
    roles:
        - role: udelarinterior.make_vm_template_unique
          # Data needed to make an ssh-copy-id to the VM template
          vm_template_host: template.mydomain.com
          vm_template_login_user: root
          vm_template_login_pass: my_super_strong_pass
          # System hostname
          vm_unique_hostname: "{{ inventory_hostname.split('.')[0] }}"
          # Collection of user->password to which it's recommended to change the default password for security
          vm_unique_passwords_to_change:
            - username: root
              password: "{{ 'unique_and_strong_pass' | password_hash('sha512', 'my_custom_salt') }}"
          # Unique VM network interface configuration
          vm_unique_net_interfaces:
            - name: eth0
              ip4: 192.168.33.10
              gw4: 192.168.33.1
              netmask4: 24
              ip6: 200:db8::10
              gw6: 200:db8::1
              netmask6: 64
            - name: eth1
              ip6: 200:db9::10
              gw6: 200:db9::1
              netmask6: 64
```

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior.

Licenced under GPL-v3

Author Information
------------------

[@santiagomr](https://github.com/santiagomr)
