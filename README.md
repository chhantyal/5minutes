# 5Minutes - Server Security Essentials

**Secure your Linux server with single command!**

A while back, I read this [article][1] about what you should do first when you get new server.
Doing so manually is quite boring though, and error prone. So, I tried to automate with [Ansible][2], 
and added few extra security features like completely disabling root and password login etc.
 

## Install

So you have new servers with root access, please follow these steps.
 
1. Install Ansible on your local computer. It's really easy if you have updated `Python
 
 ```sudo pip install ansible```

2. Clone this repo and change `hosts` file with IP address of your servers.
 
 ```git clone git@github.com:chhantyal/5minutes.git && cd 5minutes && open -t hosts```

## Usage

Using it is very easy. From within in `5minutes` directory, run this Ansible command.

```ansible-playbook 5minutes.yml -u <user_name> -K```

Enter password for your server and that't it. Single command!


### Under the Hood

If you are wondering what it does, here it is:

- Connects to server using SSH
- Updates APT cache
- Performs APT upgrade
- Adds user specified in variable `server_user_name` which has sudo permission
- Adds specified public key in variable `user_public_keys` in ssh authorized_keys.
- Disables root SSH access. Yes, from next time you need to use new user to access server.
- Disables password authentication. Again you will need to use new user with SSH public key auth method.
- Installs `ufw` as firewall, `fail2ban` to ban IPs that show malicious signs, `logwatch` to analyze and report logs.
- It also installs `unattended-upgrades` to enable automatic security updates.


### Notes

There are few other variables that you need/might want to change. See `vars:` defined in `5minutes.yml` file.

- `server_user_name`: default `trinity`
- `logwatch_email`: default `devops@example.com`, you won't get report email from `logwatch` if you don't change.
- `user_public_keys`: default `~/.ssh/id_rsa.pub`, if you use different key pair name, you need to change this path
 to public key file.

Ansible is perfect for this automation because it's dead simple to install and use without having to learn it.    
It uses SSH as agent, so you don't need to setup anything else.

PS: This is tested and works on Ubuntu, as that's what I use.    
You are welcome to add support for other distributions :)

[1]: https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers
[2]: https://www.ansible.com