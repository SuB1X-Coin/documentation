# Rupaya Hot + Cold wallet MasterNode setup guide

> This is a community contributed guide. Feel free to suggest improvements via Issues or opening Pull Requests. Thank you!

**!!! This guide is for setting up a new MasterNode using the new (dark theme) Rupaya v4 wallet and chain !!!**

---

## Setup overview

### Hot Wallet
In this guide, we refer to **Hot** wallet as the Rypaya wallet (Linux or Windows) that is running on the MasterNode server, with public IP address on it, providing services to the blockchain network for which it's rewarded with coins.
It's **Hot** because it's out on the public internet 24/7, directly accessible on the peer-to-peer port (TCP **9020**), much more vulnerable than a **Cold** wallet. 
That's why we are running it with a balance of 0 coins.

It's strongly recommended not to run MasterNode Hot wallet at home! See a list of reasons here: TODO 

### Cold Wallet
On the other side, the **Cold** wallet (Windows, OSX, Linux) holds the RUPX collateral (**10,000** RUPX) and is used to enable the MasterNode server and collect rewards for it services.

This is normally run at home, behind firewall, without direct connectivity from the internet, making it a more secure wallet. Once the MasterNode is enabled. The local wallet can then be stopped and MasterNode rewards will still show up on the next wallet start and sync.

---

## Requirements

### Hot Wallet
* Ubuntu 14.04 or 16.04 running on a server in the cloud 24/7. e.g: VPS such as Vultr, Amazon EC2 instance, Azure instance
* Public IP Address
* Basic Linux skills
* 1GB of RAM, 20GB or more disk space

You can get servers like this for $5 a month and can run 2,3 masernode wallets from different coins if the monthly cost is a concern.

### Cold Wallet
* Windows 7 or higher, Mac OS or Linux

---


## **Hot** MasterNode VPS Setup(Part 1) with Linux CLI only wallet

This will run 24/7 and provide services to the network via TCP port **9020** for which it will be rewarded with coins. It will run with an empty wallet reducing the risk of loosing the funds in the event of an attack.

### 1. Get a VPS server from a provider like Vultr, DigitalOcean, Linode, Amazon AWS, etc. 

Requirements:
* Ubuntu **14.04** or **16.04** running on a server in the cloud 24/7. e.g: VPS such as Vultr, Amazon EC2 instance, Azure instance
* Dedicated Public IP Address
* Recommended at least 1GB of RAM and 20GB of disk space
* Basic Linux skills

You can get servers like this for $5 a month and can run 2,3 masernode wallets from different coins if the monthly cost is a concern.


### 2. Login via SSH into the server and type the following command in the console as root:

If you are using Windows, [PuTTY](https://putty.org) is a very good SSH client that you can use to connect to a remote Linux server.
If you are running a VPS from Vultr or similar, you need to use SSH such as putty if you want to copy and paste these commands otherwise you will have to type them all out!

Update and Install new packages by running these commands line by line *ONE* by *ONE*:

**!!!  Do not copy the block and try to paste, it will not work! Type or paste only one line at a time and hit enter after each line !!!**

```
apt-get update
apt-get upgrade -y
apt-get install wget nano unrar unzip libboost-all-dev libevent-dev software-properties-common -y
add-apt-repository ppa:bitcoin/bitcoin -y
apt-get update
apt-get install libdb4.8-dev libdb4.8++-dev -y
```

### 3. Configure swap to avoid running out of memory:

```
fallocate -l 1500M /mnt/1500MB.swap
dd if=/dev/zero of=/mnt/1500MB.swap bs=1024 count=1572864
mkswap /mnt/1500MB.swap
swapon /mnt/1500MB.swap
chmod 600 /mnt/1500MB.swap
echo '/mnt/1500MB.swap  none  swap  sw 0  0' >> /etc/fstab
```

### 4. Allow the MasterNode p2p communication port through the OS firewall:

```
ufw allow 22/tcp
ufw limit 22/tcp
ufw allow 9020/tcp
ufw logging on
```

If you are running the MasterNode server in Amazon AWS or if additional firewalls are in place, you need to allow incoming connections on port 9020/TCP

