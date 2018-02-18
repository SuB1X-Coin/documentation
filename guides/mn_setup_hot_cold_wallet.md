# Rupaya Hot + Cold wallet MasterNode setup guide

> This is a community contributed guide. Feel free to suggest improvements via Issues or opening Pull Requests. Thank you!

**!!! This guide is for setting up a new MasterNode using the new (dark theme) Rupaya v4 wallet and chain !!!**

---

## Setup overview

### Hot Wallet
In this guide, we refer to **Hot** wallet as the Rypaya wallet (Linux or Windows) that is running on the MasterNode server, with public IP address on it, providing services to the blockchain network for which it's rewarded with coins.
It's **Hot** because it's out on the public internet 24/7, directly accessible on the peer-to-peer port (TCP **9020**), much more vulnerable than a **Cold** wallet. 
That's why we are running it with a balance of 0 coins.

It's strongly recommended not to run a MasterNode Hot wallet at home! See a list of reasons [here](mn_dont_do_this_at_home.md).

### Cold Wallet
On the other side, the **Cold** wallet (Windows, OSX, Linux) holds the RUPX collateral (**10,000** RUPX) and is used to enable the MasterNode server and collect rewards for its services.

This is normally run at home, behind firewall, without direct connectivity from the internet, making it a more secure wallet. Once the MasterNode is enabled. The local wallet can then be stopped and MasterNode rewards will still show up on the next wallet start and sync.


---


## **Hot** MasterNode VPS Setup(Part 1) with Linux CLI only wallet

This will run 24/7 and provide services to the network via TCP port **9020** for which it will be rewarded with coins. It will run with an empty wallet reducing the risk of loosing the funds in the event of an attack.

### 1. Get a VPS server from a provider like Vultr, DigitalOcean, Linode, Amazon AWS, etc. 

Requirements:
* Ubuntu **14.04** or **16.04** running on a server in the cloud 24/7. e.g: VPS such as Vultr, Amazon EC2 instance, Azure instance
* Dedicated Public IP Address
* Recommended at least 1GB of RAM and 20GB of disk space
* Basic Linux skills

You can get servers like this for $5 a month and can run 3,4 MasterNode wallets from different coins if the monthly cost is a concern.


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

### 3. Configure swap to avoid running out of memory if you don't have a swap :

```
fallocate -l 1500M /mnt/1500MB.swap
dd if=/dev/zero of=/mnt/1500MB.swap bs=1024 count=1572864
mkswap /mnt/1500MB.swap
swapon /mnt/1500MB.swap
chmod 600 /mnt/1500MB.swap
echo '/mnt/1500MB.swap  none  swap  sw 0  0' >> /etc/fstab
```

### 4. Allow SSH and MasterNode p2p communication port through the OS firewall:

```
ufw allow 22/tcp
ufw limit 22/tcp
ufw allow 9020/tcp
ufw logging on
ufw --force enable
```

If you are running the MasterNode server in Amazon AWS or if additional firewalls are in place, you need to allow incoming connections on port TCP **9020** from any IP address.

### 5. Install the Rupaya CLI wallet. Always download the latest [release available](https://github.com/rupaya-project/rupaya/releases), unpack it


For **Ubuntu 14.04**

```
apt-get install libzmq3 libminiupnpc-dev -y
wget https://github.com/rupaya-project/rupaya/files/1734792/rupaya-4.0.0-ubuntu14.04.zip
unzip rupaya-4.0.0-ubuntu14.04.zip
rm rupaya-4.0.0-ubuntu14.04.zip
mv rupaya-cli rupayad /usr/local/bin/
rupayad
```

For **Ubuntu 16.04***

```
apt-get install libzmq3-dev libminiupnpc-dev -y
wget https://github.com/rupaya-project/rupaya/files/1733802/rupaya-4.0.0-ubuntu16.04.zip
unzip rupaya-4.0.0-ubuntu16.04.zip
rm rupaya-4.0.0-ubuntu16.04.zip
mv rupaya-cli rupayad /usr/local/bin/
rupayad
```

You'll get a start error like `Error: To use rupayad, or the -server option to rupaya-qt, you must set an rpcpassword in the configuration file`. It's expected because we haven't created the config file yet.

The service will only start for a second and create the initial data directory(`/root/.rupaya/`).

### 6. Edit the MasterNode main wallet configuration file:
```
nano /root/.rupaya/rupaya.conf
```

Enter this wallet configuration data and change accordingly:
```
rpcuser=<alphanumeric_rpc_username>
rpcpassword=<alphanumeric_rpc_password>
rpcport=9021
rpcallowip=127.0.0.1
rpcconnect=127.0.0.1
rpcbind=127.0.0.1
listen=1
daemon=1
externalip=<ip_address_here>:9020
masternodeaddr=<ip_address_here>:9020
```
You can right click in SSH (putty) to paste all of the above

Exit the editor by CTRL+X and hit Y + ENTER to commit your changes.

This is a real example:
```
rpcuser=rupxuser
rpcpassword=someSUPERsecurePASSWORD3746375620
rpcport=9021
rpcallowip=127.0.0.1
rpcconnect=127.0.0.1
rpcbind=127.0.0.1
listen=1
daemon=1
externalip=199.247.10.25:9020
masternodeaddr=199.247.10.25:9020
```

The IP address(`199.247.10.25` in this example) will be different for you. Use the `ifconfig` command to find out your IP address, normally the address of the `eth0` interface. We are going to use this IP and port(9020) in the Cold Wallet setup(Step 2) as well.

### 7. Start the service and let's obtain the value for `masternodeprivkey`:
```
rupayad
```

Wait a few seconds then run this command to generate the masternode private key:
```
rupaya-cli masternode genkey
```

Copy to your clipboard the value returned, similar to this:
```
87LBTcfgkepEddWNFrJcut76rFp9wQG6rgbqPhqHWGvy13A9hJK
```

Edit the configuration file again: 
```
nano /root/.rupaya/rupaya.conf
```

Add these two lines at the end of the file. The second line is taking the value you received from the `rupaya-cli masternode genkey` command:
```
masternode=1
masternodeprivkey=<your_masternode_genkey_output>
```

In my case this was:
```
masternode=1
masternodeprivkey=87LBTcfgkepEddWNFrJcut76rFp9wQG6rgbqPhqHWGvy13A9hJK
```

Exit the editor by CTRL+X and hit Y + ENTER to commit your changes.



Stop the wallet and wait 2 minutes before attempting a start:
```
rupaya-cli stop && sleep 120
rupayad
```

### 8. Verify that the wallet is synching the blockchain:
Run this command every few mins until you see the blocks increasing.
```
rupaya-cli getinfo
``` 
We can now go to the next step while this wallet syncs up with the network and gets social with the other MasterNodes.


---


## **Cold** Wallet Setup(Part 2) using the Qt GUI wallet on Windows, OSX, etc

Requirements:
* Windows 7 or higher, Mac OS or Linux
* Outgoing internet access to sync the blockchain and enable the MasterNode remotely

This is the wallet where the MasterNode collateral will have to be transferred and stored. After the setup is complete, this wallet doesn't have to run 24/7 and will be the one receiving the rewards.

### 1. Install and open the Rupaya-Qt wallet on your machine.

 * If you have a previous Rupaya wallet installed, backup the `wallet.dat`, uninstall it then delete its original data directory.
 * Download the newest Rupaya Qt wallet from: https://github.com/rupaya-project/rupaya/releases
 * The Windows wallet needs to be extracted to a permanent location, OSX Wallet goes into `Applications`
 * Start the wallet software and ignore the unidentified developer warning.
 * If you are prompted to Allow Access by the firewall, do so.
 * Let the wallet sync until you see this in the bottom right corner of your Wallet
![Wallet Sync Completed](images/qt-wallet-synced.png "Wallet Sync Completed")

### 2. Create a receiving address for the Masternode collateral funds.

   Go to File -> Receiving addresses...
   
   Click **New**, type in a label and press **Ok**.

![Create Collateral Address](images/qt-wallet-new-collateral-address.png "Create Collateral Address")

Select the row of the newly added address and click **Copy** to store the destination address in the clipboard.

### 3. Send EXACTLY 10000 RUPX coins to the address you just copied. Double check you've got the correct address before transferring the funds.
If you are sending from an exchange, make sure you account for the withdrawal fee so that you get EXACTLY EXACTLY EXACTLY 10000 RUPX in. This is a common error that will cause the next step to not give you the transaction id needed later on. 

After sending, you can verify the balance in the Transactions tab. This can take **a few minutes** to be confirmed by the network. Go get a glass of water. No alcoholic beverages please, we are not out of the woods yet.

### 4. Open the debug console of the wallet in order to type a few commands. 

Go to `Tools` -> `Debug console`

### 5. Run `masternode outputs` command to retrieve the transaction ID of the collateral transfer.

   You should see an output that looks like this:
   ```
   [
      {
        "txhash" : "c19972e47d2a77d3ff23c2dbd8b2b204f9a64a46fed0608ce57cf76ba9216487",
        "outputidx" : 1
      }
   ]
   ```

   Both `txhash` and `outputidx` will be used in the next step. `outputidx` can be `0` or `1`, both are valid values
   
### 6. Go to `Tools` -> `Open Masternode Configuration File` and add a line in the newly opened `masternode.conf` file. 

If you get prompted to choose a program, select notepad.exe to open it.

This is an example of what you need in `masternode.conf`. Ignore any example text that may already be in there that contains a '#' in front of each line, that is just an example to help you. Read it if it helps.

This is an example of `masternode.conf`
```
mn1 your_vps_ip_address:9020 your_masternode_key_output_from_masternode_genkey txhash_from_masternode_outputs outputidx_from_masternode_outputs
```

The file will contain an example that is commented out(with a # in front), but based on the above values, I would add this line in:
```
MN1 199.247.10.25:9020 87LBTcfgkepEddWNFrJcut76rFp9wQG6rgbqPhqHWGvy13A9hJK c19972e47d2a77d3ff23c2dbd8b2b204f9a64a46fed0608ce57cf76ba9216487 1
```

Where `199.247.10.25` is the external IP of the masternode server that will provide services to the network.

Where `87LBTcfgkepEddWNFrJcut76rFp9wQG6rgbqPhqHWGvy13A9hJK` is your masternode key from (Part 1), the value used for `masternodeprivkey` in `/root/.rupaya/rupaya.conf`.

Where `c19972e47d2a77d3ff23c2dbd8b2b204f9a64a46fed0608ce57cf76ba9216487` is your txhash from `masternode outputs`.

Where `1` is your outputidx from `masternode outputs`.
      
### 7. Restart the Qt wallet to pick up the `masternode.conf` changes.
### 8. Go to Masternodes tab and check if your newly added masternode is listed.

If you want to control multiple hot wallets from this cold wallet, you will need to repeat the previous 2-7 steps. The `masternode.conf` file will contain an entry for each masternode that will be added to the network.

### 9. Enable the MasterNode

Open `Tools` > `Debug console`.

Type this command to see all the MasterNodes loaded from the `masternode.conf` file with their current status:
```
masternode list-conf
```

You should now see the newly added MasterNode with a status of `MISSING`.

Run the following command, in order to enable it:
```
startmasternode alias false MN1
```
In this ^ case, the alias of my MasterNode was MN1, in your case, it might be different.

---

## Verify that the MasterNode is enabled and contributing to the network

Switch back to the MasterNode console.
Give it a few minutes and go to the Linux VPS console and check the status of the masternode with this command:
```
rupays-cli masternode status
```

If you see status `Masternode successfully started`, you've done it, congratulations. Go hug someone now :)
It will take a few hours until the first rewards start coming in.

You should now be able to see your MasterNode(s) **ENABLED** on this web page: [http://rupx.mn.zone](http://rupx.mn.zone)

Cheers !
