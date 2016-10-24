sa-python-minimal
=================

[![Build Status](https://travis-ci.org/softasap/sa-python-minimal.svg?branch=master)](https://travis-ci.org/softasap/sa-python-minimal)


This is temporary role, caused by absense of python in default empty xenial 16.04 image, in particular on AWS or Digital Ocean.
This makes provision to fail with error like

```
mux_client_read_packet: read header failed: Broken pipe
debug2: Received exit status from master 0
Shared connection toclosed.
, "module_stdout": "/bin/sh: 1: /usr/bin/python: not found\r\n", "msg": "MODULE FAILURE"}
```

In this case either use this role, or just add following fragment to

```YAML

 pre_tasks:

  - name: ANSIBLE PYTHON | install python 2
    raw: test -e /usr/bin/python || (apt -qqy update && apt install -qy python-minimal)
    become: yes
```

Goal is to skip any sophisticated tasks ansible uses python for. (For the same reason you also need to set `gather_facts: False`)

In order to gather facts, when py2 is installed

At the same moment, Xenial comes with python, located in /usr/bin/python3.
Potentially, you can modify ansible to use python3.

Everything up to you


Example of usage (all parameters are optional)

Simple

```YAML
  roles:
    - {
        role: "sa-python-minimal"
      }
```


Advanced:

via role


```YAML
---
- hosts: bootstrapped_box
  gather_facts: False

  vars:
    - root_dir: "{{ playbook_dir }}"

    - galaxy_deploy_user: galaxy
      galaxy_deploy_authorized_keys:
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav1.pub"  # vyacheslav 1
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav2.pub"  # vyacheslav 2
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav3.pub"  # vyacheslav 3
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav4.pub"  # vyacheslav 4

  vars_files:
    - "project_common_vars.yml"


  pre_tasks:
    - debug: msg="Pre tasks section"

  roles:
    - {
        role: "sa-python-minimal",
      }      
    - {
        role: "sa-box-bootstrap",
        deploy_user: "{{deploy_user}}",
        deploy_user_key: "{{playbook_dir}}/files/ssh_keys/deploy_rsa",
        deploy_user_pub_key: "{{playbook_dir}}/files/ssh_keys/deploy_rsa.pub",
        deploy_user_authorized_keys: "{{deploy_authorized_keys}}",

        option_copy_initial_authorized_keys: true,
        option_enforce_ssh_keys_login: true,
        option_file2ban: false,
        option_ufw: true,
        option_monit: false
      }



  tasks:

    - debug: msg="Tasks section"

```


embedded:



```YAML
---
- hosts: bootstrapped_box
  gather_facts: False

  vars:
    - root_dir: "{{ playbook_dir }}"

    - galaxy_deploy_user: galaxy
      galaxy_deploy_authorized_keys:
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav1.pub"  # vyacheslav 1
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav2.pub"  # vyacheslav 2
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav3.pub"  # vyacheslav 3
        - "{{playbook_dir}}/files/ssh_keys/vyacheslav4.pub"  # vyacheslav 4

  vars_files:
    - "project_common_vars.yml"


  pre_tasks:
    - debug: msg="Pre tasks section"

    - name: ANSIBLE PYTHON | install python 2
      raw: test -e /usr/bin/python || (apt -qqy update && apt install -qy python-minimal)
      become: yes

    - name: gather facts
      setup:


  roles:
    - {
        role: "sa-box-bootstrap",
        deploy_user: "{{deploy_user}}",
        deploy_user_key: "{{playbook_dir}}/files/ssh_keys/deploy_rsa",
        deploy_user_pub_key: "{{playbook_dir}}/files/ssh_keys/deploy_rsa.pub",
        deploy_user_authorized_keys: "{{deploy_authorized_keys}}",

        option_copy_initial_authorized_keys: true,
        option_enforce_ssh_keys_login: true,
        option_file2ban: false,
        option_ufw: true,
        option_monit: false
      }



  tasks:

    - debug: msg="Tasks section"

```
