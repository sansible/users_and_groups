# Users and Roles

Master: ![Build Status](https://travis-ci.org/ansible-city/users_and_groups.svg?branch=master)  
Develop: ![Build Status](https://travis-ci.org/ansible-city/users_and_groups.svg?branch=develop)

* [ansible.cfg](#ansible-cfg)
* [Dependencies](#dependencies)
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




## Dependencies

This role haas no dependencies.




## Tags

This role uses two tags: **build** and **configure**

* `build` - Installs Go CD server and all it's dependencies.
* `configure` - Configure and ensures that the service is running.




## Examples

Simple example for creating two users and two groups.

```YAML
- name: Install GO CD Server
  hosts: sandbox

  roles:
    - name: ansible-city.users_and_groups
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
- name: Install GO CD Server
  hosts: sandbox

  vars_files:
    - "vars/sandbox/users.yml"

  roles:
    - name: ansible-city.users_and_groups
      users_and_groups:
        groups: "{{ base_image.os_groups }}"
        users: "{{ base_image.admins }}"

    - name: ansible-city.users_and_groups
      users_and_groups:
        users: "{{ developers }}"
```
