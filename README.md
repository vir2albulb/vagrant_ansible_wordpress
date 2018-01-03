# Wordpress deployment using VirtualBox, Vagrant and Ansible

Playbook is builded by default on Centos7

Playbook is created to deploy Wordpress server based on Nginx, PHP and MariaDB
servers. It allows to create very easily environment using json-like file.
VirtualBox was used to build VMs as VBox allows usage of concurrent builds
in [Vagrantfile](../master/Vagrantfile)

### Config

##### config.json

All parts of playbook uses single one config file:

[config.json](../master/files/config.json) format for single instance deployment
(both database and webserver installed on one VM)

```json
{
  "instances": [
    {
      "hostname": "wordpressServer",
      "ip_address": "192.168.33.10",
      "playbooks": [ "database", "webserver" ]
    }
  ]
}
```

[config_2vms.json](../master/files/config_2vms.json) format for two instances
deployment (database and webserver installed on two independent VMs)

```json
{
  "instances": [
    {
      "hostname": "wordpressServer",
      "ip_address": "192.168.33.10",
      "playbooks": [ "webserver" ]
    },
    {
      "hostname": "databaseServer",
      "ip_address": "192.168.33.11",
      "playbooks": [ "database" ]
    }
  ]
}
```

`wordpressServer` instance serves Wordpress content using Nginx webserver.

### Requirements

#### Install VirtualBox, Vagrant and Ansible

[VirtualBox](https://www.virtualbox.org/manual/ch02.html)

[Vagrant](https://www.vagrantup.com/intro/getting-started/install.html)

[Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)

#### Download/clone repository

Download Bitbucket repository or clone it using:

```shell
# using HTTPS
$ git clone https://bitbucket.org/vir2albulb/vagrant_ansible_wordpress.git
```

#### Ansible-vault password

Place ansible vault password under home directory in file `.vault_pass`. Vault
password is used to encrypt/decrypt
[group_vars/all](../master/provisioning/group_vars/all).

### Usage

#### Choose [config.json](../master/files/config.json)

If you want to use config file for 2 VMs just rename
[config_2vms.json](../master/files/config_2vms.json) to `config.json`.
[Vagrantfile](../master/Vagrantfile) uses `config.json` as main configuration
file.

#### Create deployment using vagrant

```shell
$ vagrant up
```

Use web server IP address `192.168.33.10` to see result in web browser.

#### Changes in playbooks

If you want to apply some changes in playbooks:

```shell
# Destroy and create new infrastructure
$ vagrant destroy -f
$ vagrant up

## OR ##
# Provision changes using vagrant
$ vagrant provision
```

The reason for usage `vagrant provision` rather then `ansible-playbook` is
because [Vagrantfile](../master/Vagrantfile) passes IP addresses of webserver
and database to playbook using extra arguments.

## License
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
