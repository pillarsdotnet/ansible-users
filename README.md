users
=====

Manages os users, groups, and authorized_keys for ssh logins and sudoers capability.

Requirements
------------

* Ansible 2.4 or later.
* Debian or RedHat Linux.

Role Variables
--------------

- Some [defaults](defaults/main.yml) should be overridden in the main playbook `group_vars`.

- User lists are passed through [`flatten`](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#list-filters) and [`unique`](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#set-theory-filters) filters to accommodate lists of lists. For example:

  ```yaml
  site_admins:
  - 'susan'
  - 'tom'
  site_users:
  - 'john'
  - 'marjorie'
  - 'susan'
  users_add:
  - '{{ site_admins }}'
  - '{{ site_users }}'
  ```

- [`users_add`](defaults/main.yml#L2) - List of usernames to be added.  
  When [adding users](tasks/add.yml), supplementary groups will [also be added](tasks/main.yml#L22).

- [`users_disable`](defaults/main.yml#L47) - List of users to be disabled.  
  Disabled users will not be [added](tasks/main.yml#L11).  
  Disabled users will retain their /etc/passwd entry.  
  Home directories of disabled users will be [chowned to `root`](tasks/disable.yml#L15), effectively preventing ssh access.  
  Disabled user ssh keys will be [excluded from `authorized_keys` files](tasks/disable.yml#L29).  

- [`users_remove`](defaults/main.yml#L28) - List of users to be removed.  
  Removed users will not be [disabled](tasks/main.yml#L7) or [added](tasks/main.yml#L11).  
  Removed user homedirs will be [deleted](tasks/remove.yml#L14), unless previously [chowned](tasks/disable.yml#L15).  
  Removed user accounts will be [deleted](tasks/remove.yml#L26).  
  Removed user ssh keys will be [excluded from `authorized_keys` files](tasks/remove.yml#L34).  
  [*Should other removed-user-owned files be deleted?*](To-Do.md#L4)

- [`users_sudoers_group`](defaults/main.yml#L38) - Group whose members should be [granted passwordless sudo access](tasks/main.yml#L32).

- [`user_list`](defaults/main.yml#L32) - Master list of all users.

- [`user_defaults`](defaults/main.yml#L7) - Default attributes applied to every user unless overridden.

Dependencies
------------

* Includes the [`sshd`](https://github.com/willshersystems/ansible-sshd) role to [ensure that authorized users have ssh login access](tasks/main.yml#L46).
* Assumes that `/etc/sudoers.d` includes have been enabled.

Example Playbook
----------------

```
  hosts:all
  roles:
    - 'ansible-users'
  vars:
    users_add:
      - 'jdoe'
      - 'rroe'
      - 'centos'
      - 'debian'
      - 'redhat'
      - 'ubuntu'
	users_default_groups:
	  - 'users'
    users_delete:
      - 'ec2-user'  # Delete account and homedir.
	users_disable:
	  - 'acontractor' # Disable account and chown homedir to root.
    users_list:
	  acontractor:
	    ssh_key: 'ssh-rsa BLAH able.contractor@example.com'
	  jdoe:
        groups: [ admin, users ]  # Jane is an admin
	    ssh_key: 'ssh-dsa BLAH jane.doe@example.com'
	  rroe:
	    ssh_key: 'ssh-rsa BLAH richard.roe@example.com'
	users_ssh_group: 'users'  # All "users" group members get ssh access.
	users_sudoers_group: 'admin'  # All "admin" group members get full sudo.
```

Author Information
------------------

[Robert August Vincent II](https://github.com/pillarsdotnet)  
*(pronounced "Bob" or "Bob-Vee")* 
