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
* Ubuntu 14.04 or 16.04 running on a VPS such as Vultr, or other server running 24/7
* Static IP Address
* Basic Linux skills

### Cold Wallet
* Windows 7 or higher, Mac OS or Linux

---
