# Ansible

**What is Ansible?**

> Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code.

---
**inventory**

Contains all system addresses (can be hostnames, IPs). Within inventory we can use groups for organising systems. Can be a static file or be provided on the CLI or even be dynamically generated (=inventory will talk with some remote API to fetch the information). You provide the inventory file on the command line with flag `-i <inventory_file>`. Although you can store variables in the main inventory file, storing separate host and group variables files may help you organize your variable values more easily: `group_vars` are a convenient way to apply variables to multiple hosts at once. It is an Ansible-specific folder as part of the repository structure. If your inventory file at `/etc/ansible/hosts` contains a host named "foosball" that belongs to two groups, "raleigh" and "webservers", that host will use variables in YAML files at the following locations:
```
/etc/ansible/group_vars/raleigh
/etc/ansible/group_vars/webservers
/etc/ansible/host_vars/foosball
```

`host_vars` is a similar folder to `group_vars` in the repository structure. It contains data models that apply to individual hosts.

---
**playbooks, plays, tasks, modules**
a repeatable, re-usable, simple configuration management that can:
* declare configurations
* orchestrate steps of any manual ordered process, on multiple sets of machines, in a defined order
* launch tasks synchronously or asynchronously

A tip: a playbook should look after a single solution - do one specific thing.

A playbook consists of one or more plays. A play is a collection of tasks. And a task executes a module (list module, service module, etc. There are many builtin modules in Ansible). Example of a play in a playbook with two tasks: `user` and `package` are modules. This play targets hosts named `webservers` which are defined in the inventory file (`all` will apply it to all hosts):

```
- name: Configure web servers
  hosts: webservers
  become: yes
  tasks:
    - name: Create a non root user
      user:
        name: michaelc
        state: present
    - name: Install nginx
      package:
        name: nginx
        state: present
```

---
**variables and groups**

You can create variables with standard YAML syntax, including lists and dictionaries. You can define these variables in your playbooks, in your inventory, in re-usable files or roles, or at the command line. To reference a variable, use Jinja2 syntax to reference it. Jinja2 variables use double curly braces. For example, the expression `My amp goes to {{ max_amp_value }}` demonstrates the most basic form of variable substitution. You can use Jinja2 syntax in playbooks. There are two default groups: `all` and `ungrouped`. The `all` group contains every host. The `ungrouped` group contains all hosts that don’t have another group aside from all. Every host will always belong to at least 2 groups (`all` and `ungrouped` or `all` and some other group). Though `all` and `ungrouped` are always present, they can be implicit and not appear in group listings like `group_names`:
```
all:
  hosts:
    main.example.com:
  children:
    webservers:
      hosts:
        foo.example.com:
        bar.example.com:
    dbservers:
      hosts:
      one.example.com:
      two.example.com:
      three.example.com:
```

---
**roles**

Repeatable units of code (DRY). With roles we can wrap multiple playbooks, files and wrap them in a fashion that are sharable and repeatable. You can use roles in a single playbook multiple times, and feed it different types of information each time. These roles can be put in Ansible Galaxy. Galaxy provides pre-packaged units of work known to Ansible as roles and collections. An Ansible role has a defined directory structure with eight main standard directories. You must include at least one of these directories in each role. You can omit any directories the role does not use:

```
# playbook
site.yml
webservers.yml
fooservers.yml
roles/
    common/
        tasks/
        handlers/
        library/
        files/
        templates/
        vars/
        defaults/
        meta/
    webservers/
        tasks/
        defaults/
        meta/
```

By default Ansible will look in each directory within a role for a `main.yml` file for relevant content (also `main.yaml` and `main`):
* `tasks/main.yml` - the main list of tasks that the role executes.
* `handlers/main.yml` - handlers, which may be used within or outside this role.
* `library/my_module.py` - modules, which may be used within this role (see Embedding modules and plugins in roles for more information).
* `defaults/main.yml` - default variables for the role (see Using Variables for more information). These variables have the lowest priority of any variables available, and can be easily overridden by any other variable, including inventory variables.
* `vars/main.yml` - other variables for the role (see Using Variables for more information).
* `files/main.yml` - files that the role deploys.
* `templates/main.yml` - templates that the role deploys.
* `meta/main.yml` - metadata for the role, including role dependencies.

An example of how to use roles in your playbook:
```
- hosts: webservers
  roles:
    - common
    - role: foo_app_instance
      vars:
        dir: '/opt/a'
        app_port: 5000
      tags: typeA
    - role: foo_app_instance
      vars:
        dir: '/opt/b'
        app_port: 5001
      tags: typeB
```

---
**Tags**

You can label your tasks in a playbook with tags. These tags can be used to:
* skip certain tasks 
* or only execute certain tasks
* great for speeding up development times during experimentation
* integrate with CI/CD pipelines so that each pipeline runs only a specific part of the playbook 

---
**Ansible Vault**

Ansible Vault encrypts variables and files so you can protect sensitive content such as passwords or keys rather than leaving it visible as plaintext in playbooks or roles. To see an actual example, just checkout these two videos:

[Ansible Vault - Introduction | How Ansible Vault🔒works | Learn Ansible Vault basic commands](https://youtu.be/L8GzW4sopug)
[Ansible vault with realtime example | Using Ansible vault for git](https://youtu.be/XNr3BbCtSmA)
 
---
**Ansible installation**

For Ansible installation, it is better to follow Ansible’s own documentation. The preferred way on Mac is with pip. So, just install a python version above Ansible’s min requirement (currently 3.8) with pyenv, and in the virtual environment, do `pip install ansible`.


---
**references**
1. [Ansible playlist by Michael Crilly](https://www.youtube.com/playlist?list=PL0yQYCnvTmOv7ctKBb66YQx11NrQPWSrx)
2. [Ansible documentation](https://docs.ansible.com/ansible/latest/getting_started/index.html)