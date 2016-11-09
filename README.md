sa-prudentia
============

[![Build Status](https://travis-ci.org/softasap/sa-prudentia.svg?branch=master)](https://travis-ci.org/softasap/sa-prudentia)


Example of usage (all parameters are optional)

Simple
```YAML
  roles:
    - {
        role: "sa-prudentia"
      }
```

Advanced:

```YAML
  roles:
    - {
        role: "sa-prudentia"
      }
```



https://pypi.python.org/pypi/prudentia


Prudentia is a Continuous Deployment toolkit written in Python.

Mission

Prudentia’s mission is to help you to get production (or any other environment) ready in minutes instead of days, by streamlining all the actions needed to provision your architectural components.

Features

Prudentia uses Ansible as its main automation system, so it easily understands Ansible playbooks. A playbook is one of the components needed to define a Prudentia Box.

Prudentia currently offers:

a CLI (supporting auto-completion) used to interactively define Boxes and run operations on them
Here-Document format to script Prudentia environments
provisioning of an existing server that can be accessed trough SSH
management of the lifecycle of a Box that has been created through Prudentia
creating Boxes using one of these providers:
Vagrant
DigitalOcean
local
ssh
Currently, all features work with Python 2.6+ and 3.2+.

Prerequisites

You need at minimum:

Python 2.6 and pip
To install on a Linux distribution you need:

libffi-dev
libssl-dev
python-dev
Installation

To install Prudentia:

```
$ pip install prudentia
```

It may be necessary to have root privileges, in which case:

```
$ sudo pip install prudentia
```

To uninstall:

```
$ pip uninstall prudentia
```

Box operations

A Simple provider (e.g. Local provider or SSH provider) have the following operations available:

```
register: adds a new box definition to the registry
unregister: removes a box from the registry
reconfigure: changes the definition of an existing box
list: lists all boxes in the registry
set: defines or override an Ansible extra variable
unset: removes an Ansible extra variable
vars: loads Ansible extra variables from an external .yml or .json file (overriding existing ones)
envset: sets the value of an environment variable
provision: runs tasks defined in the playbook associated with a box
decrypt: sets the password used to decrypt Ansible vault files
verbose: sets Ansible verbosity, using a value between 0 and 4
facts: shows useful information about the box and accepts optional parameter to filter properties
```

A Factory provider (e.g. Vagrant provider or DigitalOcean provider) extend simple provider and adds the ability to change the box life cycle:

```
create: instantiate a new instance based of the box definition
restart: reloads the instance
stop: shuts down the instance
destroy: kill the instance
phoenix: shortcut for stop -> destroy -> create -> start -> provision (citing phoenix server Martin Fowler’s article)
status: returns the status of the instance
```

Usage

CLI
We’ll show a usage example of the SSH provider bundled with Prudentia.

Make sure you have a server that you can ssh into.

```
$ prudentia ssh
```

Check what the Ssh provider can do using tab completion:

```
(Prudentia > Ssh)
decrypt      EOF          help         list         provision    reconfigure  register     set          unregister   unset        vars
```

Let’s start registering a new box:

```
(Prudentia > Ssh) register
```

Specify the playbook path:
Now Prudentia is asking for a playbook path, and this is actually an Ansible playbook.

You can use one of the samples that you can find in the examples/boxes directory. For instance, the tasks.yml that will run some Ansible tasks that we’ve defined (those tasks are not that meaningful, but they are used as a sanity check in our tests).

So let’s continue using the tasks.yml:

```
(Prudentia > Ssh) register
Specify the playbook path: /path/to/prudentia/examples/boxes/tasks.yml
Specify the box name [default: tasks-host]:
Specify the instance address or inventory: ip.of.your.server
Specify the remote user [default: _your_user_]:
Specify the password for the remote user [default: ssh key]:
```


Box example -> (/path/to/prudentia/examples/boxes/tasks.yml, tasks-host, ip.of.your.server, _your_user_) added.
You will notice that, for some questions, Prudentia gives suggested answer within [ ]. For instance, the suggested Box name is tasks-host. If you like the suggestion, just press enter to choose it.

So far we’ve registered a Prudentia Box that can be used to play around. If you want to check the definition again:

```
(Prudentia > Ssh) list
example -> (/path/to/prudentia/examples/boxes/tasks.yml, tasks-host, ip.of.your.server, _your_user_)
```
Now that we have double-checked that our Box has been registered, we can provision it:

```
(Prudentia > Ssh) provision example

PLAY [tasks-host] ***************************************************************

GATHERING FACTS ***************************************************************
ok: [tasks-host]

TASK: [Uname] *****************************************************************
changed: [tasks-host] => {"changed": true, "cmd": ["uname", "-a"], "delta": "0:00:00.005527", "end": "2015-01-01 19:13:58.633534", "rc": 0, "start": "2015-01-01 19:13:58.628007", "stderr": "", "stdout": "Darwin tiziano-air 12.5.0 Darwin Kernel Version 12.5.0: Sun Sep 29 13:33:47 PDT 2013; root:xnu-2050.48.12~1/RELEASE_X86_64 x86_64", "warnings": []}

TASK: [Shuffle] ***************************************************************
ok: [tasks-host] => (item=2) => {
    "item": 2,
    "msg": "2"
}
ok: [tasks-host] => (item=4) => {
    "item": 4,
    "msg": "4"
}
ok: [tasks-host] => (item=1) => {
    "item": 1,
    "msg": "1"
}
ok: [tasks-host] => (item=5) => {
    "item": 5,
    "msg": "5"
}
ok: [tasks-host] => (item=3) => {
    "item": 3,
    "msg": "3"
}

TASK: [No operation] **********************************************************
ok: [tasks-host] => {
    "msg": "Task noop executed."
}

PLAY RECAP ********************************************************************
tasks-host                  : ok=4    changed=1    unreachable=0    failed=0

Play run took 0 minutes
```

Now Prudentia has done the reasonable uninteresting uname, shuffling a list of ints and noop tasks on the remote machine.
