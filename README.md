# Ansible Role: Sudoers

[![Molecule](https://github.com/truestory1/ansible-role-sudoers/actions/workflows/molecule.yml/badge.svg)](https://github.com/truestory1/ansible-role-sudoers/actions/workflows/molecule.yml)

## Description

This Ansible role provides a straightforward way to manage and configure the sudoers file on your target hosts. It allows you to define user- and group-based sudo privileges, ensuring consistent and secure management of sudo rights across your infrastructure.

## Features

- **Custom Sudoers Configuration:** Easily manage and customize sudoers entries.
- **User and Group Management:** Define sudo rules for both users and groups.
- **Flexible Commands:** Specify allowed commands and control password requirements.
- **Production Ready:** Designed for deployment in production environments.
- **Tested with Molecule:** Includes basic Molecule scenarios for testing.

## Requirements

- **Ansible:** Version 2.9 or later.
- **Python:** Version 3.x on managed nodes.
- **Dependencies:**
  - `community.general` collection (install via `ansible-galaxy collection install community.general`).

## Role Variables

This role uses the `sudoers` variable to define one or more sudoers entries. Each entry supports the following keys:

- **`user`** (string, optional): The username for which to configure sudo privileges.
- **`group`** (string, optional): The group name for which to configure sudo privileges.
- **`runas`** (string, optional): The user that commands should run as (e.g., `postgres`).
- **`commands`** (list, optional): A list of allowed commands. If omitted, all commands are allowed.
- **`nopassword`** (boolean, optional): Set to `false` if a password prompt should be required. Defaults to `true` if not specified.

### Allowed Commands

The `commands` parameter specifies which system commands are permitted when the sudoers entry is invoked. Here are some key points about using the `commands` parameter:

- **List Format:** Provide the commands as a YAML list.
- **YAML Anchors:** You can use YAML anchors and aliases to reuse command lists across multiple entries.
- **Default Behavior:** If no commands list is provided, the role will allow all commands by default.

## Example Playbook

Below is an example playbook that uses this role. It demonstrates how to define command lists and various sudoers entries for both users and groups:

```yaml
---
- name: Converge
  hosts: all
  gather_facts: false
  vars:
    commands_test01: &commands_test01
      - /bin/id
      - /bin/ls
    commands_test02: &commands_test02
      - /bin/id
      - /bin/whoami
    sudoers:
      - group: test
        runas: postgres
        commands: *commands_test01
      - user: test2
        commands: *commands_test01
      - user: test3
      - user: test5
        nopassword: false
        commands: *commands_test01
      - user: test6
        commands: *commands_test02
  tasks:
    - name: Start the role
      ansible.builtin.include_role:
        name: ansible-role-sudoers
```

