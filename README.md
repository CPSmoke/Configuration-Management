# Configuration-Management
https://roadmap.sh/projects/configuration-management
Installing Ansible on Ubuntu:
Update the package list:
Open a terminal and run the command to update the list of available packages:

bash
```sudo apt update```
Install the necessary dependencies:
Make sure that you have the necessary dependencies installed:

bash
```sudo apt install software-properties-common```
Add a PPA for Ansible:
Ansible can be installed from a PPA (Personal Package Archive). Add the PPA using the following command:

bash
```sudo add-apt-repository ppa:ansible/ansible```
Update the package list again:
After adding a new PPA, you need to update the package list:

bash
```sudo apt update```
Install Ansible:
You can now install Ansible by running the command:

bash
```sudo apt install ansible```
Check the Ansible version:
After installation, check that Ansible is installed correctly and review its version:

bash
```ansible --version```

Configuring SSH On the local computer:
Generating a key:

bash
```ssh-keygen -t rsa -b 4096```

Add public keys from the local server to the remote one:
For Linux:

bash
```ssh-copy-id -i ~/.ssh/id_rsa.pub user@server-ip```

For Windows:

  ```type C:\Users\username\.ssh\id_rsa_test\id_rsa1.pub | ssh user@19.XXX.XXX.XXX "mkdir -p ~/.ssh && cat >> ~/.ssh/id_rsa1"```
4.Connecting to the Server

Now you can connect to your server:

For Linux:

bash
```ssh -i ~/.ssh/id_rsa user@server-ip```
For Windows

```ssh -i C:\Users\username\.ssh\id_rsa_test\id_rsa1 user@server-ip```

File setup.yml
yaml
```
---
- name: Настройка сервера
  hosts: all
  become: true
  roles:
    - { role: base, tags: ["base"] }
    - { role: nginx, tags: ["nginx"] }
    - { role: ssh, tags: ["ssh"] }
    - { role: app, tags: ["app"] }
```
Base role
Directory: roles/base/tasks/main. yml
yaml
```
---
- name: Server update
  apt:
    update_cache: yes
    upgrade: dist

- name: Install required utilities
  apt:
    name:
      - git
      - curl
      - vim
    state: present

- name: Install fail2ban
  apt:
    name: fail2ban
    state: present
```
Nginx role
Directory: roles/nginx/tasks/main. yml
yaml
```
---
- name: Installing Nginx
apt:
    name: nginx
    enabled: yes

- name: Starting and enabling Nginx
  service:
    name: nginx
    state: started
    enabled: yes
```
App Role
Directory: roles/app/tasks/main. yml
yaml
```
---
- name: Cloning a repository with HTML pages
  git:
    repo: 'https://github.com/username/repo.git' # Replace with your repository
    dest: /var/www/html
    version: master

- name: Setting folder permissions
  file:
    path: /var/www/html
    owner: www-data
    group: www-data
    mode: '0755'
```
SSH role
Directory: roles/ssh/tasks/main. yml
yaml
```
---
- name: Adding SSH
authorized_key public key:
    user: your_user # Replace with the user name
    state: present
    key: "{{ lookup('file', 'path/to/your/public/key.pub')}} "# Path to your public key
```
Inventory file inventory.ini
ini
```
[webservers]
your_server_ip_or_hostname ansible_ssh_user=your_user # Replace with your server address and user name
[all:vars]
ansible_ssh_private_key_file=/path/to/your/private/key
```
Running the script
To start all the roles, use the command:

shell
```
ansible-playbook -i inventory.ini setup.yml
```
To run only the app role, use:

shell
```
ansible-playbook -i inventory.ini setup.yml --tags "app"
```
