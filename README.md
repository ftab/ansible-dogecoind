# ansible-dogecoind

## What it does

Run a dogecoin node and wallet on just about any Linux server, virtual machine, VPS, aka the cloud.

Run it on one server or however many servers you have with one deployment command.

## How it does it

Downloads and installs Dogecoin through some magic

## Why?

Help the Dogecoin network, or just make it easier on yourself to set up a new node.

## Technical details

[Ansible](https://ansible.com/) playbook
* Downloads the official Dogecoin release from GitHub and puts it in /usr/local
* Sets up a `dogecoin` user on the machine
* Installs a systemd service file so that the machine starts dogecoind when rebooted
* Runs the systemd service file

## Requirements

* Server(s): One or more Linux servers you want to set up Dogecoin nodes on (each on separate networks)
* Host: A control computer with Linux, macOS, or Windows 10 Pro with Windows Subsystem for Linux installed

### On your server(s)

Linux server with at least 2 GB of RAM and 60 GB of hard drive space (preferably more, as the blockchain is about 48 GiB now and will grow over time)

[Near the end of the page are handy referral links if you need a new server](#servers)

### On your host computer

On the host you will be controlling the servers from, you need Ansible and an SSH key to the root user on the machines you will be controlling.

[Follow Ansible's install guide for your operating system](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

(If you're using Windows, install WSL, install Ubuntu 20.04, and follow the above guide for Ubuntu)

Ansible really doesn't want to enter passwords, so that's why we use SSH keys.

# Step by step guide

## 1. Clone the repository

```shell script
git clone https://github.com/ftab/ansible-dogecoind.git
cd ansible-dogecoind
cp dogecoin.conf.example dogecoin.conf 
cp hosts.example hosts
```

## 2. Set up your Ansible inventory in the hosts file

Edit the `hosts` file. This will be used as your **Ansible inventory**

```shell script
nano hosts
```

Populate this file with the IP of your server, or list of IPs if you have more than one

The ansible_user should either be root, or a user that has access to sudo without password, or can create users and all that. It's probably easiest to just use root.

For example:

```
[dogecoin]
192.168.2.19 ansible_user=root
104.200.67.252 ansible_user=root
```

This means your **inventory** for the **dogecoin** role consists of two hosts: a local network server at 192.168.2.19 and a VPS server at 104.200.67.252

## 3. Get SSH keys to root for all your hosts

If you don't have an SSH key already generated, do so now:
```shell script
ssh-keygen -N '' -t ed25519
```

Get an SSH key set up to your root user on **each host** you put into the hosts file:
```sh
ssh-copy-id root@your.host.ip
# Answer yes if asked to confirm a host key, then enter your root password

ssh root@your.host.ip
# if you get to your root terminal here, you're good to go
exit
```

If all is well, proceed to step 4.

##### Not accepting your password even though you know it should work?
If this is a fresh Debian VM install and root has no password or is not enabled for passworded login over SSH (probably the default), first copy it to a user that does have a passworded login, and administrator privileges (sudo), then copy the authorized_keys file to /root/.ssh/

```sh
ssh-copy-id fury@your.host.ip
ssh fury@your.host.ip
sudo mkdir -p /root/.ssh
cat .ssh/authorized_keys | sudo tee -a /root/.ssh/authorized_keys
exit

ssh root@your.host.ip
# if you get to your root terminal here, you're good to go
exit
```


## 4. Deploy

```shell script
ansible-playbook dogecoind.yml -i hosts
```

That's it. Now dogecoin is running on all of your servers.

You can check in on each of your hosts like this:

```shell script
ssh root@your.host.ip
su dogecoin -s /bin/bash
dogecoin-cli getinfo
dogecoin-cli getnettotals
dogecoin-cli help
```

## 5. Extra credit points

Running on your home network or behind a firewall? You'll experience a much faster node sync if you configure your router or firewall to forward port **22556** to the server that will be running dogecoind. Consult your router or firewall documentation.

This is not necessary for most cloud/VPS as they are already wide open.

## 6. Warnings

This has been tested on servers dedicated exclusively to running a Dogecoin node. Don't add existing servers with other things on them to your inventory without understanding the implications. Don't store anything valuable here without first securing your node against prying eyes. It would be inadvisable to store any wallet data on these nodes as-is.

## 7. Further reading

Google on the bing for setting up fail2ban, hardening your SSH server, and other fun things like that.

# Donations/Credits

Give socks to the homeless by tipping [9vnaTWu71XWimFCW3hctSxryQgYg7rRZ7y](dogecoin:9vnaTWu71XWimFCW3hctSxryQgYg7rRZ7y)

DirtMcGirt
* Tip: [D5fNgytaNohWECCd8NSb82B3YCd78akHjB](dogecoin:D5fNgytaNohWECCd8NSb82B3YCd78akHjB)

fury
* Tip: [DATfurydmRTZ6vJnBtaibHJYMdx9JYjL4n](dogecoin:DATfurydmRTZ6vJnBtaibHJYMdx9JYjL4n)
* Referral Links: [servers](#servers) list at the bottom

This was based on Scott Motte's original ansible playbook
* Tip: [DJEBk3QoBGbNL7oWzXqvjgW1A9DuFKHs8q](dogecoin:DJEBk3QoBGbNL7oWzXqvjgW1A9DuFKHs8q)

# Noteworthy VPS providers

No referral kickback, I just thought these would be good options if you want to pay with cryptocurrency

## BitVPS

Accepts payment directly in Dogecoin

https://bitvps.com/

Get at least 60 GB SSD and 2 GB RAM

## CrownCloud

Accepts Dogecoin payment through CoinPayments after checkout process

The [classic KVM HDD 2](https://crowncloud.net/classic_kvm.php) or [classic OpenVZ OVZ-C-2048](https://crowncloud.net/classic_openvz.php) plans should do (select Debian or Ubuntu as your operating system for best results, I haven't tried CentOS)

# Referral Links <a name="servers"></a>

If you don't already have a server, and want to support this effort, use these handy referral links to some good VPS providers

### DigitalOcean

You get: $100 credit good for 60 days

Link: https://m.do.co/c/a011044154d9

You need: $15/mo basic droplet (2GB RAM, 60GB SSD)

### SSD Nodes

Link: https://www.ssdnodes.com/manage/aff.php?aff=1218

Pricing varies frequently, check out their sales

### SkySilk

You get: $25 credit good for 60 days

Link: https://www.skysilk.com/ref/xoIKOwbm4r

You need: Basic -> Ubuntu -> Optima VPS plan ($9/mo, 2GB RAM, 60GB SSD)

### Linode

Link: https://www.linode.com/?r=1c5f99e5c025bef54b1bc219df0276f33259ee9b

You need: $20/mo Shared Linode (4GB RAM, 80GB SSD)

# Chat

Bug fury on IRC in Freenode #dogecoin

Find more help, memes, and chat on the Dogecoin discord at https://dogecoin.gg/invite

# Contributions

Patches are welcome :)

Would-be-nice: Ansible role to optionally set up and download the Dogecoin blockchain torrent before starting the node