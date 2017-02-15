# 5Minutes - Server Security Essentials

**Secure your Linux server with single command!**

A while back, I read this [article][1] about what you should do first when you get new server.
Doing so manually is quite boring though, and error prone. So, I tried to automate with [Ansible][2], 
and added few extra security features like completely disabling root and password login etc.
 

## Install

So you have new servers with root access, please follow these steps.
 
1. Install Ansible on your local computer. It's really easy if you have updated `Python`
 
 ```sudo pip install ansible```

2. Clone this repo and change `hosts` file with IP address of your servers.
 
 ```git clone git@github.com:chhantyal/5minutes.git && cd 5minutes && open -t hosts```

3. Change var `server_user_password` in `vars.yml` file with crypted password.
This will be password for `server_user_name`. To generate, run:

 ```sudo pip install passlib```    
 ```python -c "from passlib.hash import sha512_crypt; import getpass; print sha512_crypt.encrypt(getpass.getpass())"```

## Usage

Using it is very easy. From within in `5minutes` directory, run this Ansible command. Make sure you don't have old entries
in known_hosts file from the server with the same IP address.
Also note that you need to login manually into the server before you access it with the ansible script. New server
should be already in known_hosts

```ansible-playbook 5minutes.yml -u <user_name> -K```

Enter password for your server and that't it. Single command!

## Try with Vagrant

You can try on Vagrant box before running this on real servers.
There is `Vagrantfile` included.

```vagrant up```

Change `hosts` to `127.1.1.0:2200` (see `vagrant up` output for exact port) and run command:

```ansible-playbook 5minutes.yml -u vagrant --private-key .vagrant/machines/default/virtualbox/private_key```

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

There are few other variables that you need/might want to change. See `vars:` defined in `vars.yml` file.

- `server_user_name`: default `trinity`
- `server_user_password`: Please change this. See [Ansible docs][3]
- `logwatch_email`: default `devops@example.com`, you won't get report email from `logwatch` if you don't change.
- `user_public_keys`: default `~/.ssh/id_rsa.pub`, if you use different key pair name, you need to change this path
 to public key file.

Ansible is perfect for this automation because it's dead simple to install and use without having to learn it.    
It uses SSH as agent, so you don't need to setup anything else.

PS: This is tested on Ubuntu, as that's what I use. You are welcome to add support for other distributions :)

[1]: https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers
[2]: https://www.ansible.com
[3]: http://docs.ansible.com/ansible/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module