# SUB1X wallet upgrade guide

It is recommended that you upgrade your wallets (local or MasterNode ones) as soon as a new version is released.
This ensures that the consensus across the decentralized network and improvements. These are crucial for the success of the project. Non upgraded wallets will be banned by the other nodes in the network at certain block thresholds. This will eliminate the node from the network and cause lost rewards and ban expire delays.


## Backup your wallet

It's a good practice to stop the wallet and backup your `wallet.dat`, `zSub1x.conf` and `masternode.conf` files regularily, before upgrades or other operations.

These are the default directories for the data directory where these files are stored:
 * Windows: `~\AppData\Roaming\zSub1x`
 * Linux: `~/.zSub1x/`
 * Mac: `~/Library/Application Support/zSub1x`

where `~` represents the home directory of the login user.


## Check existing wallet version

Depending on the type of wallet you are running, here are a few ways to check the version that is currently running:

* For Windows GUI wallet, select from the top menu: `Help` -> `About zSub1x Core`
* For OSX GUI wallet, select from the top menu: `zSub1x Core` -> `About zSub1x Core`

If you see `zSub1x Core version v1.2.1 ...`, you are running version `1.2.1` of the zSub1x wallet

For Linux CLI wallets, you can check the wallet version with this command from the shell:
```
zsub1x-cli getnetworkinfo | grep subversion
```

You will see `"subversion" : "/zSub1x Core:1.2.1/"` when you are running version `1.2.1` of the zSub1x wallet


## Check for new wallet version releases

Compiled wallets and source code archives will be available here for every release:

https://github.com/SuB1X-Coin/zSub1x/releases

## Upgrade your wallet to a newer version

[Download](https://github.com/SuB1X-Coin/zSub1x/releases) the archive with the new wallet. We are not covering compiling from sources in this guide to keep it short and sweet.

### Windows and OSX GUI wallets

For Windows and OSX, after you stop the wallet, just replace the old `qt` executable with the new one. Start it back and verify that the version of the wallet changed.

### Linux CLI wallet

Here are the steps needed to upgrade the wallet normally used by Linux MasterNodes 

#### 1. Login via SSH into the server and type the following command in the console as root:

If you are using Windows, [PuTTY](https://putty.org) is a very good SSH client that you can use to connect to a remote Linux server.

Best to run these commands as user root to avoid having to deal with filesystem permissions.

#### 2. Check the current wallet version:
```
zsub1x-cli getnetworkinfo | grep subversion
```

#### 3. Stop the wallet service with:
```
zsub1x-cli stop
```
Run the following command until the `zsub1xd` process disappears. It usually takes around a minute for the process to this to happen.
```
ps aux | grep zsub1xd | grep -v grep
```

#### 4. Download the new wallet and unpack it:

This command uses a short github url to download [zsub1x-1.3.4-x86_64-linux.tar.gz](https://github.com/SuB1X-Coin/zSub1x/releases/download/v1.3.4/zsub1x-1.3.4-x86_64-linux.tar.gz) and unpack the binaries in the PATH:
```
wget -qO- https://git.io/vpV8V | sudo tar xvz -C /usr/local/bin/
```

#### 5. Start the service:
```
zsub1xd
```
It will take 10 seconds or so before we can call it with the cli command.

#### 6. Use the cli command to check the wallet version:
```
zsub1x-cli getnetworkinfo | grep subversion
```

If this reports the new version, you are golden.


## Verify that the new wallet is synching blocks

After every upgrade, please ensure that the wallet is synching the blocks and having connections to the network. 

To check this on a Linux CLI wallet, run the following command:
```
zsub1x-cli getinfo | egrep "blocks|connections"
```

For Windows or OSX GUI wallets you can find this information from the top menu: `Tools` -> `Information`. You are looking for:
 * `Client version`
 * `Current number of blocks`
 * `Number of connections (out)`

## If you are running a MasterNode

Verify the status of the masternode with `zsub1x-cli masternode status` from the CLI or `masternode status` from a GUI masternode.
You should see `"message" : "Masternode successfully started"`

If you get `"message" : "Not capable masternode: Hot node, waiting for remote activation."`, go the the upgraded Cold wallet and START the masternode again (i.e. `startmasternode alias false MN1`)

Wait a minute and then go to the MasterNode server, stop the wallet and start it again. Check the masternode status now.

You might need to wait around 20 minutes before the masternode reports `Masternode successfully started`.
After this, check the masternode on [sub1x.mn.zone](http://sub1x.mn.zone). If the node is listed with `a few sec` in the `Active Time` column, check the status of the masternode again and ensure you get `"message" : "Masternode successfully started"`. You might also need to stop and start the masternode wallet again to activate it.

Thank you for upgrading and helping this chain move forward :rocket:
