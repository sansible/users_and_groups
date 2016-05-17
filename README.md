# Users and Roles

Master: [![Build Status](https://travis-ci.org/sansible/users_and_groups.svg?branch=master)](https://travis-ci.org/sansible/users_and_groups)  
Develop: [![Build Status](https://travis-ci.org/sansible/users_and_groups.svg?branch=develop)](https://travis-ci.org/sansible/users_and_groups)

* [ansible.cfg](#ansible-cfg)
* [Installation and Dependencies](#installation-and-dependencies)
* [Tags](#tags)
* [Examples](#examples)

This roles manages OS users and groups.




## ansible.cfg

This role is designed to work with merge "hash_behaviour". Make sure your
ansible.cfg contains these settings

```INI
[defaults]
hash_behaviour = merge
```




## Installation and Dependencies

This role has no dependencies.

To install run `ansible-galaxy install sansible.users_and_groups` or add
this to your `roles.yml`

```YAML
- name: sansible.users_and_groups
  version: v1.0
```

and run `ansible-galaxy install -p ./roles -r roles.yml`




## Tags

This role uses three tags: **build**, **configure** and **maintain**

* `build` & `configure` - Ensures that specified groups and users are
  present.
* maintain - Ensures users on an already built and configured instance




## Examples

Simple example for creating two users and two groups.

```YAML
- name: Configure User Access
  hosts: sandbox

  roles:
    - name: sansible.users_and_groups
      users_and_groups:
        groups:
          - name: lorem
            system: yes
          - name: ipsum
        users:
          - name: lorem.ipsum
            groups:
              - ipsum
              - lorem
            ssh_key: ./lorem.ipsum.pub
          - name: dolor.ament
            groups:
              - ipsum
```

In most cases you would keep the list of users in external vars file or
group|host vars file.

```YAML
- name: Configure User Access
  hosts: sandbox

  vars_files:
    - "vars/sandbox/users.yml"

  roles:
    - name: sansible.users_and_groups
      users_and_groups:
        groups: "{{ base_image.os_groups }}"
        users: "{{ base_image.admins }}"

    - name: sansible.users_and_groups
      users_and_groups:
        users: "{{ developers }}"
```

Add selected group to sudoers

```YAML
- name: Configure User Access
  hosts: sandbox

  vars_files:
    - "vars/sandbox/users.yml"

  roles:
    - name: sansible.users_and_groups
      users_and_groups:
        groups: "{{ base_image.os_groups }}"
        users: "{{ base_image.admins }}"

    - name: sansible.users_and_groups
      users_and_groups:
        users: "{{ developers }}"

    - name: sansible.users_and_groups
      users_and_groups:
        sudoers:
          - name: wheel
            user: "%wheel"
            runas: "ALL=(ALL)"
            commands: "NOPASSWD: ALL"
```

Use whitelist groups option to allow users contextually.

Var file with users:

```YAML
---

# vars/users.yml

users_and_groups:
  groups:
    - name: admins
    - name: developer_group_alpha
    - name: developer_group_beta
  users:
    - name: admin.user
      group: admins
    - name: alpha.user
      group: alpha_develops
    - name: beta.user
      group: developer_group_beta
```

In a base image:

```YAML
---

# playbooks/base_image.yml

- name: Base Image
  hosts: "{{ hosts }}"

  vars_files:
    - vars/users.yml
    
  roles:
    - role: sansible.users_and_groups
      users_and_groups:
        whitelist_groups:
          - admins

    - role: base_image
```

In a service role:

```YAML
---

# playbooks/alpha_service.yml

- name: Alpha Service
  hosts: "{{ hosts }}"

  vars_files:
    - vars/users.yml
    
  roles:
    - role: sansible.users_and_groups
      users_and_groups:
        whitelist_groups:
          - admins
          - developer_group_alpha

    - role: alpha_service
```

