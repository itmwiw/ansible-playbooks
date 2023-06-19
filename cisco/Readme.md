## Prerequisites

### Ip address 

Unlike routers, it is not possible to give a switch an ip adress through interface. You can only do it through Vlan:
```Bash
enable
configure terminal
interface vlan 1
ip address <adress_ip> <Network_mask>
no shutdown
exit
#ip default gateway <gateway_adresse_ip>
exit
copy running-config startup-config
```Bash

### SSH

Here's an example script to use for SSH's configuration:
```Bash
configure terminal
# Authentification
aaa new-model
aaa authentication login default local
aaa authorization exec default local
username admin secret admin
# SSH
hostname <hostname>
ip domain-name <Domain>
crypto key generate rsa general-keys modulus 1024
ip ssh version 2
ip ssh logging events
ip ssh time-out 60
ip ssh authentication-retries 3
line vty 0 15
transport input ssh
exit
# Password for the enable command
enable secret <enablepassword>
exit
copy running-config startup-config
```

## Run the playbook

- Install dependencies:

```Bash
pip install ansible-pylibssh
ansible-galaxy collection install cisco.ios
pip install hvac
```

- Add Floating-IPs configuration: 
```Bash
ansible-playbook floating-ip.yml -i hosts.ini
```
