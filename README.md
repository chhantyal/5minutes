# 5minutes

Secure your Linux server with single command.

A while back, I wrote this (article)[1] about what you should do on first five minutes after you get new server.
Doing so manually is quite boring, and error prone. So, I tried to automate with (Ansible)[2], and added few extra
security feature like completely disabling root and password login. 


## Install

So you have new servers with root access, please follow following steps.
 
1. Install Ansible on your local computer. It's really easy if you have updated Python
 
 ```sudo pip install ansible```

2. Clone this repo and change `hosts` file with IP address your servers.


## Use

Using it is very easy. From within in `5minutes` directory, run this Ansible command.

```ansible-playbook 5minutes.yml -u uhura -K```

Enter password for your server and that't it. Single command!


### Under the Hood

If you are wondering what it does, here it is:

- Updates APT cache
- Performs APT upgrade
- Adds user specified in variable `server_user_name` which has sudo permission
- Adds specified public key in variable `user_public_keys` in ssh authorized_keys.
- Disables root SSH access. Yes, from next time you need to use new user to access server.
- Disables password authentication. Again you will need to use new user with SSH public key auth method.
- Installs `ufw` as firewall, `fail2ban` to ban IPs that show the malicious signs, `logwatch` to analyze and report logs.
- It also installs `unattended-upgrades` to enable automatic security updates.

PS: This is tested and works on Ubuntu, as that's what I use :)

[1]: https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers
[2]: https://www.ansible.com
