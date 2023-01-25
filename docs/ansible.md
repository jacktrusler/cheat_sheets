# Ansible
![Ansible](./svgs/ansible.svg "Ansible")
*Redhat's solution for automating server tasks.* 

Ansible uses .yaml (YAML ain't markup language) to define what they call a *playbook* which is a series
of commands that are run in order based on how the script is defined. 

## Setup
Ansible is most often used on a host that configures other machines, the machine where all the ansible functions 
are defined and where you use the ansible CLI tools is called the **Control node**, the devices you are managing are 
called **managed nodes**

`/etc/ansible` directory holds the configuration in `ansible.cfg` file, the hosts that you wish to 
connect to over ssh in the `hosts` file, and the inventory of servers and their tags in the `inventory.yaml` file. 

examples: 

- hosts file
```
[myservers]
root@jacktrusler.com
root@jsonbateman.com

[redhat]
root@jacktrusler.com
```

    ansible all --list-hosts
    ansible all -m ping

- inventory.yaml file
```yaml
myservers:
  hosts:
    root@jsonbateman:
      ansible_host: root@jsonbateman.com
    root@jacktrusler:
      ansible_host: root@jacktrusler.com
redhat:
  hosts:
    root@jsonbateman:
      ansible_host: root@jsonbateman.com
```

    ansible myservers -m ping -i inventory.yaml

after that you can define a playbook anywhere and ansible will look for the configured files in the 
`etc/ansible` directory. It should also look in the ~/.ansible directory but I haven't tried that yet. 

example playbook: 

```yaml
- name: Ping Servers
  hosts: myservers
  tasks:
    - name: Ping hosts
      ansible.builtin.ping:
- name: Update servers
  hosts: redhat
  tasks:
    - name: Upgrade all packages
      ansible.builtin.dnf:
        name: "*"
        state: latest
```

[here's some builtin dnf functions](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/dnf_module.html)

## Running Locally
[ansible docs are pretty straightforward on how to run local scripts](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_delegation.html#local-playbooks)
