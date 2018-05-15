# CGC cloud setup

This repository contains scripts and data used in managing the configuration of
the CGC cloud servers.

## Some NOTES by Ts2

1. When docker has sth wrong with network and couldn't start,try these command
        sudo rm -rf /var/lib/docker/network
        sudo systemctl start docker
2. First things first,create namespace manually.
        #copy: src=namespace.yml dest=/etc/kubernetes/kube-system-ns.yml
        kubectl create -f /etc/kubernetes/kube-system-ns.yml
3. Install cpufreq in all hosts.
        sudo apt-get install indicator-cpufreq

## Using this repository

1. Get [Ansible](http://www.ansibleworks.com/)
   (`pip install -r requirements.txt`)

2. Clone this repository and VPN access keys (ask @Yan).

3. Build or download the VM image in `roles/crs/vm/files/cgc-vm.qcow2` (see [tester-qemu-docker](kubernetes/tester-qemu-docker/)).

4. Run `ansible-playbook`

## Playbooks

* Setup

    Install and run Kubernetes on CGC nodes:

        ansible-playbook -i inventory/mechaphish/hosts setup.yml -b

    This will setup users, install dependencies and spawn a new Kubernetes instance.

* Teardown

    Use `teardown.yml` playbook to stop Kubernetes and reset nodes to their original state:

        ansible-playbook -i inventory/mechaphish/hosts teardown.yml -b

* Virtual Competition

        ansible-playbook -i inventory/mechaphish/hosts setup-virtualcompetition.yml -b

* Registry

        ansible-playbook -i inventory/mechaphish/hosts setup-registry.yml -b

## Run commands

You can also use the `ansible` command directly to run tasks on remote hosts:

    ansible all -i inventory/mechaphish/hosts -a "uptime"

Adding -vvvv (or --verbose) will get you lots of useful debug info.

## Repository structure

### `setup.yml`, `teardown.yml`, etc.

Top-level ansible playbooks.

### `roles/`

Application-specific configuration.

### `inventory/`

Host or host-group specific configuration.
CGC nodes are grouped in different sets. The main inventory lives at
`inventory/NAME/hosts` and host and group variables files live in
`inventory/NAME/{host,group}_vars`.

### `kubernetes/`

Dockerfile and replication controller settings for Kubernetes images.

### `tools/`

* `crs`: handy script to start/stop crs
* `powercontrol.py`: poorman IPMI
* `virtualcompetition`: start/stop virtual-competition
* `update-vm-image`: install latest components into DECREE VM image.
   Requires:
   ```
   pip install paramiko stopit
   apt-get --no-install-recommends install qemu-kvm
   adduser gitlab-runner kvm
   ```

### `sshp/`

List of hosts to use with `sshp`
(requires [sshp](https://www.npmjs.com/package/sshp)).

```
sshp -f sshp/ips.txt uptime
```

## CGC cloud servers

[Google Spreadsheet](https://docs.google.com/spreadsheets/d/1merG8nu291re7AyIhTZdZP4NNU7wPd4RcnNRLsHsSwI/edit?usp=sharing)
