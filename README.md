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

## To Be Done
  * add schemas (databases)
  * set up database(s) from a mysqldump

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

Create a file named 'hosts'<br>
In the 'hosts' file, out the ip(s) of the servers you wish to set up.<br>

From the root of the project directory, run the playbook.
`ansible-playbook playbook.yml`
<br>
The script should complete within 10 minutes, depending on your connection speed and the speed of the server and its connection.<br>

## Contributing
Any ideas for improvement are welcome. Feel free to make pull requests (please add your basic idea as an issue first). If you prefer direct contact over making a PR, email me at roger@rogercreasy.com.