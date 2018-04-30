## SUB1X Governance powered by the blockchain

The implementation of SUB1X Community Governance will allow anyone with an up-to-date wallet to submit project proposals for: integration, promotion, new exchange listings, marketing, development, etc and get the requested SUB1X coins if the majority of MasterNode owners choose to support it with **Yes** votes.

The example below is done using the testnet chain. This guide will be updated appropriately to reflect the real chain once governance goes into mainnet. 

### Create a proposal

All the commands assume a CLI wallet.

You can run them in the debug console of a Qt wallet as well, just remove the `$ zsub1x-cli ` part from the commands.

### Identify the next budget superblock to pin the proposal to

```
$ zsub1x-cli getnextsuperblock
129600
```

### Create an address in the wallet that will receive the funds if the proposal is voted Yes by the MasterNodes:
```
$ zsub1x-cli getaccountaddress "proposal1"
xySqwRc5pNt7nD6KNr2QLWJ7sgpWD7Pj6X
```

### Create the proposal using the superblock and address from above. 

In this example we are asking for 0.04 SUB1X coins. Min amount that can be requested is `0.04`. Value `1` after the URL indicates that this is a one-off proposal, targeted to be paid on block 40800 if masternode owners support it.
The URL is there to provide additional details about the proposal so that MN owners can decide if they should vote it yes or no.
Proposal name is limited at 20 characters and URL at 64.
```
$ zsub1x-cli preparebudget "test1" "http://sub1x-testnet.mn.zone/proposal1" 1 129600 "xySqwRc5pNt7nD6KNr2QLWJ7sgpWD7Pj6X" 0.04
c9567750806e2c1a01fd71984279d4d3f44c253d82389c9df583f248b8ccada4
```

Use the help subcommand if you want more details about each parameter:
```
zsub1x-cli help preparebudget
```

### Wait for the 0.04 SUB1X proposal fee transaction to gain 6 confirmations
```
zsub1x-cli gettransaction c9567750806e2c1a01fd71984279d4d3f44c253d82389c9df583f248b8ccada4

```

### Once we have 6 confirmations for the proposal fee, we can submit the proposal
```
$ zsub1x-cli submitbudget "test1" "http://sub1x-testnet.mn.zone/proposal1" 1 129600 "xySqwRc5pNt7nD6KNr2QLWJ7sgpWD7Pj6X" 0.04 "c9567750806e2c1a01fd71984279d4d3f44c253d82389c9df583f248b8ccada4"
```

### See all proposals for this budget cycle
```json
$ zsub1x-cli getbudgetinfo
{
        "Name" : "test1",
        "URL" : "http://sub1x-testnet.mn.zone/proposal1",
        "Hash" : "9001bbdc91c30d1a10a7863107280e3c6151b1b16481d43d2325f99e9a57bf7c",
        "FeeHash" : "c9567750806e2c1a01fd71984279d4d3f44c253d82389c9df583f248b8ccada4",
        "BlockStart" : 129600,
        "BlockEnd" : 172800,
        "TotalPaymentCount" : 1,
        "RemainingPaymentCount" : 1,
        "PaymentAddress" : "xySqwRc5pNt7nD6KNr2QLWJ7sgpWD7Pj6X",
        "Ratio" : 0.00000000,
        "Yeas" : 0,
        "Nays" : 0,
        "Abstains" : 0,
        "TotalPayment" : 0.04000000,
        "MonthlyPayment" : 0.04000000,
        "IsEstablished" : true,
        "IsValid" : true,
        "IsValidReason" : "",
        "fValid" : true
    },

```

### Vote yes on the above proposal from the masternode CLI
```
$ zsub1x-cli mnbudgetvote local "9001bbdc91c30d1a10a7863107280e3c6151b1b16481d43d2325f99e9a57bf7c" yes
```

### Alternatively, you can vote from the Cold wallet as well on behalf of multiple masternodes
```
$ zsub1x-cli mnbudgetvote many "1c4c744a3b684b535b0eafe30a9c366e189554a53e9c76b542b9cbba8887f886" yes
```
