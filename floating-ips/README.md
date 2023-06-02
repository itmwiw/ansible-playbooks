## Prerequisites

- The Opnsense firewalls must be accessible via the ip adress or hostname in the hosts.ini file.
- Ssh must be enabled on both firewalls: Navigate to System > Settings > Administration and under Secure Shell section, check Enable Secure Shell. To login as root, check Permit root user login.
- Add the ssh public keys: system > acess > user > authorized keys.
- Change the login shell to tcsh: system > acess > user >  Login shell = tcsh

## Run the playbook

- Install dependencies:
sudo apt install python3-lxml

- Run the paybook:
ansible-playbook initial-config.yml -i hosts.ini