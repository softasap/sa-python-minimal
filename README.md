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
    raw: sudo bash -c "test -e /usr/bin/python || (apt -qqy update && apt install -qy python-minimal)"
```

Goal is to skip any sophisticated tasks ansible uses python for.

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


```YAML
  roles:
    - {
        role: "sa-python-minimal"
      }
```





