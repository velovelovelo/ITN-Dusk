### ITN DUSK NETWOK

### System Requirements ###
- OS : Ubuntu 22.04
- CPU : 2 Cores
- RAM : 4 GB
- SSD : 80 GB

### installation ###
# update dependencies
```
sudo apt update -y
```

# Install Rusk
```
curl --proto '=https' --tlsv1.2 -sSfL https://github.com/dusk-network/itn-installer/releases/download/v0.1.0/itn-installer.sh | sudo sh
```

# Configure Rusk
Once everything has been set up, you will be asked to add your consensus keys. These keys are used to sign and vote for blocks.

If you haven’t made a wallet yet, go to [here](https://wallet.dusk.network/setup/) and create a new wallet. You can request faucet [here](https://faucet.dusk.network/). The faucet will give you 1100 tDUSK. The minimum to stake is 1000 tDUSK.

Once you have access to a Dusk mnemonic, run the following command:
```
rusk-wallet restore
```

You will be asked to provide your recovery phrase/mnemonic, in lower caps, and to enter a password for the wallet.

Once you’ve done so, run the following command to export a consensus key for the given wallet:
```
rusk-wallet export -d /opt/dusk/conf -n consensus.keys
```

You will be asked to set an encryption password for the consensus key. Remember it and run the following script to set it as an environment variable:
```
sh /opt/dusk/bin/setup_consensus_pwd.sh
```

If you’ve configured everything correctly, you can now start rusk:
```
service rusk start
```

Your node will now start syncing. You can check if it indeed is by running:
```
grep "block accepted" /var/log/rusk.log
```
It is best to wait until your node is synced up. You can find the latest block height [here](https://explorer.dusk.network/)

# Stake tDUSK
The final step is to stake. You can stake by running:
```
rusk-wallet stake --amt 1000
```
Once the transaction has gone through, you can view your staking information by running:
```
rusk-wallet stake-info
```
To see if your node is participating in consensus and creating blocks:
```
tail -F /var/log/rusk.log | grep "execute_state_transition"
```
### Note that this can take a while, given that your stake needs at least 2 epochs, or 4320 blocks, to mature. Your stake, relative to the total stake, also plays a factor.
### If everything went right, and your node starts accepting and creating blocks, you have succesfully set up your ITN node!

# Manual resync

# Steps to Resync Your Node

run this command:
```
curl --location --request POST 'http://127.0.0.1:8080/02/Chain' --header 'Rusk-Version: 0.7.0-rc' --header 'Content-Type: application/json' --data-raw '{
    "topic": "gql",
    "data": "query { block(height: -1) { header { height } } }"
}' | jq '.block.header.height'
```

If your node is confirmed to be stuck (e.g. at block 50636) or significantly behind the current block height, follow these steps to resync:
# 1. Unstake (if applicable)
If you are staked, the first step is to unstake to prevent any potential loss of stake due to node downtime:
```
rusk-wallet unstake
```

# 2.Stop the Rusk Service
Stop the Rusk service to prevent any new data from being written to the database:
```
service rusk stop
```

# 3. Remove Chain Data and Cache
Delete the existing chain database and cache to remove any potentially corrupt data:
```
rm -rf /opt/dusk/rusk/chain.db/
rm -rf /opt/dusk/rusk/state/
rm -rf ~/.dusk/rusk-wallet/cache*
```

# 4. Restart Rusk
With the old data removed, restart the Rusk service to begin syncing from scratch:
```
service rusk start
```

# 5. Monitor Sync Progress
Monitor the progress of your node’s sync by checking the last block accepted by your node:
```
curl --location --request POST 'http://127.0.0.1:8080/02/Chain' --header 'Rusk-Version: 0.7.0-rc' --header 'Content-Type: application/json' --data-raw '{
    "topic": "gql",
    "data": "query { block(height: -1) { header { height } } }"
}' | jq '.block.header.height'
```

# 6. Restake (if applicable)
Once your node is close to the current block height, you can restake your DUSK tokens:
```
rusk-wallet stake --amt 1000
```

# Node upgrade
To upgrade to the latest ITN version, run:
```
curl --proto '=https' --tlsv1.2 -sSfL https://github.com/dusk-network/itn-installer/releases/download/v0.1.1/itn-installer.sh | sudo sh
```

Once it’s done and gives no errors, start Rusk again:
```
service rusk start
```

Keep an eye on the logs to see if it’s making and accepting new blocks:
```
tail -F /var/log/rusk.log
```

If you see new blocks being accepted, you’re up and running again. Is your node stuck, or are you not sure if it is? Check out the manual resync instructions.
