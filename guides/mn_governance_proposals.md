## Rupaya Governance powered by the blockchain

The Rupaya Community Governance will allow anyone with a Rupaya wallet to submit project proposals for new exchanges, marketing, dev, integrations, etc and get the requested RUPX coins if MasterNode owners vote it **Yes**

### Create a proposal

All the commands assume a CLI wallet. You can run them in the debug console of a Qt wallet as well, just remove the `$ rupaya-cli ` part from the commands.

### Identify the next budget superblock to pin the proposal to

```
$ rupaya-cli getnextsuperblock
129600
```

### Create an address in the wallet that will receive the funds if the proposal is voted Yes by the MasterNodes:
```
$ rupaya-cli getaccountaddress "proposal1"
yKdGJkWJm4fXTrRmpvrXjpL4r8eMnnBeLd
```

### Create the proposal using the superblock and address from above. 

In this example we are asking for 2000 RUPX coins. Min amount that can be requested is `10`. Value `1` after the URL indicates that this is a one-off proposal, targeted to be paid on block 129600 if masternode owners support it.
The URL is there to provide additional details about the proposal so that MN owners can decide if they should vote it yes or no.
Proposal name is limited at 20 characters and URL at 64.
```
$ rupaya-cli preparebudget "test1" "http://example.com/proposal1" 1 129600 "yKdGJkWJm4fXTrRmpvrXjpL4r8eMnnBeLd" 2000
2e21cae5c69209d648734653e3dcca85c67ac96e08a48d99923de58e9ba222d3
```

Use the help subcommand if you want more details about each parameter:
```
rupaya-cli help preparebudget
```

### Wait for the 50 RUPX proposal fee transaction to gain 6 confirmations
```
$ rupaya-cli gettransaction 2e21cae5c69209d648734653e3dcca85c67ac96e08a48d99923de58e9ba222d3
```

### Once we have 6 confirmations for the proposal fee, we can submit the proposal
```
$ rupaya-cli submitbudget "test1" "http://example.com/proposal1" 1 129600 "yKdGJkWJm4fXTrRmpvrXjpL4r8eMnnBeLd" 2000 "2e21cae5c69209d648734653e3dcca85c67ac96e08a48d99923de58e9ba222d3"
```

### See all proposals for this budget cycle
```json
$ rupaya-cli getbudgetinfo
[
    {
        "Name" : "test1",
        "URL" : "http://example.com/proposal1",
        "Hash" : "1c4c744a3b684b535b0eafe30a9c366e189554a53e9c76b542b9cbba8887f886",
        "FeeHash" : "2e21cae5c69209d648734653e3dcca85c67ac96e08a48d99923de58e9ba222d3",
        "BlockStart" : 129600,
        "BlockEnd" : 172800,
        "TotalPaymentCount" : 1,
        "RemainingPaymentCount" : 1,
        "PaymentAddress" : "yKdGJkWJm4fXTrRmpvrXjpL4r8eMnnBeLd",
        "Ratio" : 0.00000000,
        "Yeas" : 0,
        "Nays" : 0,
        "Abstains" : 0,
        "TotalPayment" : 2000.00000000,
        "MonthlyPayment" : 2000.00000000,
        "IsEstablished" : true,
        "IsValid" : true,
        "IsValidReason" : "",
        "fValid" : true
    }
]
```

### Vote yes on the above proposal from the masternode CLI
```
$ rupaya-cli mnbudgetvote local "1c4c744a3b684b535b0eafe30a9c366e189554a53e9c76b542b9cbba8887f886" yes
```

### Alternatively you can vote from the Cold wallet as well on behalf of multiple masternodes
```
$ rupaya-cli mnbudgetvote many "1c4c744a3b684b535b0eafe30a9c366e189554a53e9c76b542b9cbba8887f886" yes
```
