== Prerequisites
=== Opnsense
- The Opnsense firewalls must be accessible via the ip adress or hostname in the hosts.ini file.
- Ssh must be enabled on both firewalls: Navigate to System > Settings > Administration and under Secure Shell section, check Enable Secure Shell. To login as root, check Permit root user login.
- Add the ssh public keys: system > acess > user > authorized keys.
- Change the login shell to tcsh: system > acess > user >  Login shell = tcsh

=== Cisco switch
==== Ip address 
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
==== SSH
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

== Run the playbook

- Install dependencies and collections:
sudo apt install python3-lxml
pip install ansible-pylibssh
ansible-galaxy collection install cisco.ios


- Add floating Ips configuration:
ansible-playbook add-floating-ip.yml -i hosts.ini

== Draft
-- configure ip --
# It is not possible to give a switch an ip adress through interface. You can only do it through Vlan.
enable
configure terminal
interface vlan 1
ip address 192.168.1.110 255.255.255.0
no shutdown
exit
#ip default gateway <adresse_ip_passerelle>
exit
copy running-config startup-config

-- ssh --
configure terminal
# Configuration de l'authentification et ajout d'un compte administrateur
aaa new-model
aaa authentication login default local
aaa authorization exec default local
username admin secret admin
# Activation et Configuration SSH
hostname myswitch
ip domain-name advatys.com
crypto key generate rsa general-keys modulus 1024
ip ssh version 2
ip ssh logging events
ip ssh time-out 60
ip ssh authentication-retries 3
# Désactivation de telnet pour l'accès au switch
line vty 0 15
transport input ssh
exit
# Activation du mot de passe pour la commande enable
enable secret enablepassword
exit
copy running-config startup-config

# Non testé à cause de la version trop ancienne du switch de test

Optionally, you can configure the router to disable SSH password authentication:
no ip ssh server authenticate user password
no ip ssh server authenticate user keyboard


ip ssh pubkey-chain # doesn't work in the testing switch
username admin
key-string
AAAAC3NzaC1lZDI1NTE5AAAAIC/dWjs+ZIdD3glfevXkLs9dqnp/i7xGi5kXytrbHzZb  
eIR1lyAnDJIsYbTbcdm+n5KiQnCt2561MpN4yOFpajFNM/dqH7/jYaqaicHCSV2F       
RGauEp7FzN/uXxsX7mii6qOuxovl9OflLpXcvH5QH6551ycmL8nIv8UCY8uayiGI        
INsC0LyKEctWDW6qWp43T7rhcP0y4JoMraTCZLIPNE0Bo0bHgnGLg6fEvJmyB3sX       
H+7BaxHdYKg2OcIgVqYzclWhDwxj32kqd1BCq089iBMrb4QppDU2eM/t22iK29mn      
eqOGTiCkxB80ix+KULT9okmqkj3TbhCpunTfuPCCRNrjqndBsw==
exit
exit
exit

# Autres
-- enable gui -- 
configure terminal
ip http server
ip http secure-server
no ip http server
no ip http secure-server
username thaddouchi privilege 15 password 0 thaddouchi


-- Netplan Vlans --

network:
    ethernets:
        ens3:
            dhcp4: no
            match:
                macaddress: 0c:9b:79:20:00:00
            set-name: ens3
    vlans:
        vlan11:
            id: 11
            link: ens3
            addresses: [192.168.11.224/24]
            #gateway4: 192.168.10.1
            nameservers:
                addresses: [8.8.8.8, 8.8.4.4]
        vlan12:
            id: 12
            link: ens3
            addresses: [192.168.12.224/24]
            #gateway4: 192.168.10.1
            nameservers:
                addresses: [8.8.8.8, 8.8.4.4]
    version: 2
