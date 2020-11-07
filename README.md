<h1 align="center">Hetzner Robot Server Boostrap Ansible Role</h1>
<br />

<div align="center">
  <a href="https://travis-ci.com/mariancraciun1983/ansible-hetzner-dev-bootstrap">
    <img src="https://travis-ci.com/mariancraciun1983/ansible-hetzner-dev-bootstrap.svg?branch=master" alt="Build Status" />
  </a>
  <a href="https://galaxy.ansible.com/mariancraciun1983/hetzner_dev_boostrap">
    <img src="https://img.shields.io/ansible/role/51683" alt="Ansible Galaxy" />
  </a>
  <a href="https://galaxy.ansible.com/mariancraciun1983/hetzner_dev_boostrap">
    <img src="https://img.shields.io/ansible/quality/51683" alt="Ansible Quality Score" />
  </a>
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="MIT License" />
  </a>
</div>
<br />

Ansible role to bootstrap the installed system on a [Hetzner dedicated servers](https://www.hetzner.com/dedicated-rootserver) with [Storage Box](https://www.hetzner.com/storage/storage-box), [vSwitch](https://docs.hetzner.com/robot/dedicated-server/network/vswitch/) and [Failover](https://docs.hetzner.com/robot/dedicated-server/ip/failover/) support.


## Introduction

This role is useful during my lessons specifically for practicing on the Hetzner servers. There are also 2 other useful roles: 
[hetzner_install_image](https://galaxy.ansible.com/mariancraciun1983/hetzner_install_image) and [install_python](https://galaxy.ansible.com/mariancraciun1983/install_python) .

### Ansible
This role was tested against Ansible version 2.8 2.9 2.10 .
The supported platforms are
  - Debian
    - buster
    - stretch
  - Ubuntu
    - focal
    - bionic
    - xenial
  - Centos
    - 8

## Variables
Vswitch configuration example
```yaml
group_vars:
  all:
    vswitch: true # enable vswitch networking
    vswitch_id: 14567 # vwitch id
    robotws:
      user: hetzuser # webservice user
      password: hetzpass # webservice pass

host_vars:
  target1:
    vswitch_ip: 10.0.0.11
  target2:
    vswitch_ip: 10.0.0.12
```

By default the ansible user will be created
```yaml
group_vars:
  all:
    admin_user:
      name: ansible
      group: wheel
      authorized_key_file: "{{ lookup('env','HOME') + '/.ssh/id_rsa.pub' }}"
      sudo: true
```

If you have a storagebox, mount it with
```yaml
group_vars:
  all:
    davfs2_mounts:
      - name: u12345.your-storagebox.de
        fstype: davfs
        src: https://u12345.your-storagebox.de
        path: /mnt/storagebox
        user: u12345
        password: u12345password
```

## Testing

Molecule with vagrant are being used. Since this project will configure the ssh daemon and networking, docker can't be supported.

Requirements for testing:
```bash
# install qemu/libvirt and bridge-utils needed for virtualization
sudo apt-get update && sudo apt-get install -y bridge-utils dnsmasq-base ebtables libvirt-bin libvirt-dev qemu-kvm qemu-utils ruby-dev

# install vagrant 2.2.10
sudo wget -nv https://releases.hashicorp.com/vagrant/2.2.10/vagrant_2.2.10_x86_64.deb
sudo dpkg -i vagrant_2.2.10_x86_64.deb

# install vagrant libvirt plugin
sudo vagrant plugin install vagrant-libvirt
```

Running the tests:
```bash
# linting will first also fetch the requirements
pipenv run molecule lint

sudo -E pipenv run molecule create

sudo -E pipenv run molecule converge

sudo -E pipenv run molecule destroy
```