### ITN DUSK NETWOK

# official guide
https://docs.dusk.network/itn/node-running-guide/

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


