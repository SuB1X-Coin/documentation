# Rupaya wallet upgrade guide

It is recommended that you upgrade your wallets (local or MasterNode ones) as soon as a new version is released.
This ensures that the consensus across the decentralized network and improvements. These are crucial for the success of the project.
Wallet upgrade will not cause MasterNode rewards being lost or delayed. On the opposite, not upgraded wallets will be banned by the other nodes in the network at certain block thresholds. This will eliminate the node from the network and cause lost rewards and ban expire delays.


## Check existing wallet version

Depending on the type of wallet you are running, here are a few ways to check the version that is currently running:

* For Windows GUI wallet, select from the top menu: `Help` -> `About Rupaya Core`
* For OSX GUI wallet, select from the top menu: `Rupaya Core` -> `About Rupaya Core`

If you see `Rupaya Core version v4.0.0.0 ...`, you are running version `4.0.0.0` of the Rupaya wallet

For Linux CLI wallets, you can check the wallet version with this command from the shell:
```
rupaya-cli getnetworkinfo | grep subversion
```

You will see `"subversion" : "/Rupaya Core:4.0.0/"` when you are running version `4.0.0.0` of the Rupaya wallet


## Check for new wallet version releases

Compiled wallets and source code archives will be available here for every release:

https://github.com/rupaya-project/rupaya/releases


## Upgrade your wallet to a newer version

It's a good practice to stop the wallet and backup your `wallet.dat`, `rupaya.conf` and `masternode.conf` files regularily, before upgrades or other operations.

These are the default directories for the data directory where these files are stored:
 * Windows: `~\AppData\Roaming\Rupaya`
 * Linux: `~/.rupaya/`
 * Mac: `~/Library/Application Support/Rupaya`

where `~` represents the home directory of the login user.

[Download](https://github.com/rupaya-project/rupaya/releases) the archive with the new wallet. We are not covering compiling from sources in this guide to keep it short and sweet.

### Windows and OSX GUI wallets

For Windows and OSX, after you stop the wallet, just replace the old `qt` executable with the new one. Start it back and verify that the version of the wallet changed.

### Linux CLI wallet

Here are the steps needed to upgrade the wallet normally used by Linux MasterNodes 

#### 1. Login via SSH into the server and type the following command in the console as root:

If you are using Windows, [PuTTY](https://putty.org) is a very good SSH client that you can use to connect to a remote Linux server.

Best to run these commands as user root to avoid having to deal with filesystem permissions.

#### 2. Check the current wallet version:
```
rupaya-cli getnetworkinfo | grep subversion
```

#### 3. Stop the wallet service with:
```
rupaya-cli stop
```
Run the following command until the `rupayad` process disappears. It usually takes around a minute for the process to this to happen.
```
ps aux | grep rupayad | grep -v grep
```

#### 4. Download the new wallet and unpack it:
```
cd /tmp
wget https://github.com/rupaya-project/rupaya/releases/download/v4.0.0.0/rupaya-4.0.0-x86_64-linux.tar.gz
tar -xzvf rupaya-4.1.0-x86_64-linux.tar.gz -C /usr/local/bin/
```

#### 5. Start the service:
```
rupayad
```
It will take 10 seconds or so before we can call it with the cli command.

#### 6. Use the cli command to check the wallet version:
```
rupaya-cli getnetworkinfo | grep subversion
```

If this reports the new version, you are golden.


## Verify that the new wallet is synching blocks

After every upgrade, please ensure that the wallet is synching the blocks and having connections to the network. 

To check this on a Linux CLI wallet, run the following command:
```
rupaya-cli getinfo | egrep "blocks|connections"
```

For Windows or OSX GUI wallets you can find this information from the top menu: `Tools` -> `Information`. You are looking for:
 * `Client version`
 * `Current number of blocks`
 * `Number of connections (out)`

Thank you for upgrading and helping this chain move forward :rocket:
