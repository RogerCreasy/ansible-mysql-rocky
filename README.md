[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
# Set up MySQL on Rocky Linux using Ansible
This project is an ansible script that configures the server and installs MySQL.

## Prerequisites
  * basic knowledge of, and the ability to edit yaml files
  * Ansible installed on your control machine (your local computer)
  * A server runing Rocky Linux (we have only tested on a Digital Ocean droplet)
  * ssh access to the server
  * at least one regular user to be set up with sudo privileges
  * a hashed password for each user to be set up
  * an ssh keypair for each user to be set up
  * an email address to which system emails will be sent

## What the script does
  * install epel-release (required for some packaages)
  * update the system
  * install chrony (for time sync)
  * install postfix (for sending system emails)
  * create sudo user accounts
  * basic system hardening
  * install, conifgure, and secure mysql
  * add schema(s) (databases in MySQL speak)
  * upload and import dump files
  * optionally set up a backup system

## To Be Done
  * check for absense of schemas - then skip add schema and import

## Basic instructions
Generate a hash of each sudo user password.
`python -c "from passlib.hash import sha512_crypt; import getpass; print(sha512_crypt.hash(getpass.getpass()))"`
(you may have to install the Python passlib module)<br>
Generate a key pair for the system and each user to be added. (Or, use existing key pairs)
`ssh-keygen -f ~/.ansible/.ssh/[keyname] -t ed25519 -a 100` (search for how to generate ssh keys, if you need further info)<br>
Copy vars/general.yml.example to vars/general.yaml. This file has the variables that the configuration files and playbooks need to set up the system.<br>
Replace all '[ ]'s and everything inside them. What you need to replace with is explained within the file.<br><br>

Create a 'keys' directory at the project root. THis directory should be at the same directory level as the 'vars' directory.<br>
Create a directory within the 'keys' directory for each user you want to add to the server. These sub-directories should be named with teh linux username of the users. Copy each user's public key into the appropriate keys sub-directory. i.e. the public key for linuxuser1 should be copied into keys/linuxuser1/<br>
*the public key filename should end with .pub*<br>

Gzip your dump files, name the files the same as the schema(s) without the .gz extension, place the files in `roles/mysql/files/`<br>

In the ansible.cfg file set the location of the private key (private_key_file) you want to use as root as the script runs. The default is to use the ed25519 key for the user who is running the script.<br>

Create a file named 'hosts'<br>
In the 'hosts' file, put the server group name and ip(s) of the server(s) you wish to set up.
```
     [mysql_servers]
     111.111.111.111
```
From the root of the project directory, run the playbook.
`ansible-playbook playbook.yml`
<br>
The script should complete within 10 minutes, depending on your connection speed and the speed of the server and its connection.<br>

## Backup System (optional)
The script can set up a database backup system. The installation of this system is triggered by setting the "backups" variable in vars/general.yaml to "true.
The script checks the value of this variable, and runs the backups playbook if the variable is set to true.<br>
There is a little prework involved in setting up the backup system.
  * On AWS S3 or Digital Ocean Spaces create a bucket to store your backups

## Contributing
Any ideas for improvement are welcome. Feel free to make pull requests (please add your basic idea as an issue first). If you prefer direct contact over making a PR, email me at roger@rogercreasy.com.
