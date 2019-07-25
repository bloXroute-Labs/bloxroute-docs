## Installation of the Blockchain-Logger 

This document describes installation of the bloXroute Blockchain-Logger for the bloXroute Ethereum Miner Test. For this test, it is expected that you are running an Ethereum geth node connected to the mainnet. 

The Blockchain-Logger is a small piece of software created by bloXroute to record block propagation statistics across p2p blockchain networks. It does not provide any block propagation performance improvements and permits us to measure block propagation without the bloXroute network. 

This document describes two ways that you may install the Blockchain-Logger, using either packaged docker images, or via a github download. 


## Blockchain-Logger

### Installing the Blockchain-Logger From Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This section assumes you have Docker installed and that you have created an account with Docker Hub and provided the account name or email address to bloXroute. Additionally, this section assumes that the RPC ports for the full node are open to receive RPC commands. 

1. For proper functioning, the Blockchain-Logger needs your ethereum node to be running at verboisty level 3 and exposing the appropriate rpc apis. You can ensure that your geth client has sufficient verbosity for its logs by starting geth with `--verbosity=3` or `--vmodule="miner=3,core=3"`. You may also change verbosity from the `geth attach` console by using the command `debug.verbosity(3)` or `debug.vmodule("miner=3,core=3")`. Additionally, please enable the proper rpc apis using the following switches at startup `--rpc --rpcapi web3,eth,personal,miner,net,txpool`.
1. Docker login

    ```
    docker login --username [username]
    ```
	
2. Pull gateway image

   ```
   docker pull bloxroute/blockchain-logger:latest
   ```
 
3. Create a file called logger.env with environment variables as shown below. Please fill in the requested information shown in brackets. See the Logger Environmental Variables table below for a description of each variable. A worked example is also shown below. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. For DNS_NAME, please use a unique name that includes your organization name.
    ```
    NETWORK=eth
    RPCIP=[IP address of Ethereum full node]
    RPCPORT=[Ethereum full node RPC port]
    NETWORK_NAME=ETH
    DNS_NAME=[unique name for the full node, should include your organization name, e.g, "bestpool-01"]
    BLOCKCHAIN_LOG=/var/ethereum/ethereum_debug.log
    NTP_TIME=1
    ```

    Complete example (Note: the RPCIP is the IP of the host machine from the perspective of docker. You should not have to change it):
    ```
    NETWORK=eth
    RPCIP=172.17.0.1
    RPCPORT=8545
    NETWORK_NAME=ETH_MAINNET
    DNS_NAME=bestpool-01
    BLOCKCHAIN_LOG=/var/ethereum/log/ethereum_debug.log
    NTP_TIME=1 
  

4. Run the docker as a daemon. Change "/path/to/your/ethereum" to point to your ethereum log directory.
    ```
    docker run -d --env-file logger.env -v /path/to/your/ethereum/debug.log:/var/ethereum/ethereum_debug.log bloxroute/blockchain-logger:latest
    ```


### Installing the Blockchain-Logger From Github 

The Blockchain-Logger collects logs from your Ethereum full node and provides them to bloXroute. This installation assumes that you are installing the Blockchain-Logger on a server with a running full node. Additionally, these instructions assume that the machine you are installing on already has installations of python 3, git, and pip. Note that the installation installs `psutils`, if this fails then you may need to install the Python Development libraries `python36-devel`. Further, this section assumes that the RPC ports for the full node are open to receive RPC commands. 
    
1. For proper functioning, the Blockchain-Logger needs your ethereum node to be running at verboisty level 3 and exposing the appropriate rpc apis. You can ensure that your geth client has sufficient verbosity for its logs by starting geth with `--verbosity=3` or `--vmodule="miner=3,core=3"`. You may also change verbosity from the `geth attach` console by using the command `debug.verbosity(3)` or `debug.vmodule("miner=3,core=3")`. Additionally, please enable the proper rpc apis using the following switches at startup `--rpc --rpcapi web3,eth,personal,miner,net,txpool`.
1. Clone the repository (this is already done if you've already installed the gateway)
   ```
   git clone https://github.com/bloXroute-Labs/bloXroute-miner-test.git
   ```
   
2. Run the Blockchain-Logger installation 
    ```
    ./logger-setup.sh
    ```

4. Edit the environment variables in the `logger-run.sh` file . See the Logger Environmental Variables table below for a description of each variable.  BLOCKCHAIN_LOG is the log file of the Ethereum node. Please make sure the Blockchain-Logger has permission to read the log file. It can often be found in the Ethereum data directory along with the wallet.dat files and blockchain data. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. For DNS_NAME, please use the hostname of your full node, or a unique name.
    ```
    export NETWORK='eth'
    export RPCIP='[IP address of Ethereum full node]'
    export RPCPORT='[Ethereum full node RPC port]'
    export NETWORK_NAME='ETH_MAINNET'
    export DNS_NAME=[unique name for the full node, should include your organization name, e.g, "bestpool-01"]
    export BLOCKCHAIN_LOG=[log location]
    export NTP_TIME=1
    ```

    Complete example:
    ```
    export NETWORK='eth'
    export RPCIP='127.0.0.1'
    export RPCPORT='8545'
    export NETWORK_NAME='ETH_MAINNET'
    export DNS_NAME='bestpool-01'
    export BLOCKCHAIN_LOG=/var/ethereum/ethereum_debug.log
    export NTP_TIME=1
     ```
5. Run the Blockchain-Logger
    ```
    ./logger-run.sh
    ```
    It is recommended that Blockchain-Logger is run using the 'nohup' or 'screen' commands to permit the gateway to persist even if the terminal is closed. For example,
    ```
    nohup ./logger-run.sh &
    ```
    
### Logger Environmental Variables
The following are environment variables that must be set for the logger to access client data.
Any variables that do not have a default value **must** be set.

| Environment Variables | Description | Default |
| --------- | ----------- | -------------|
| NETWORK | Blockchain network type - for this test 'eth' is used | btc |
| RPCUSER | If applicable, username to authenticate with client RPC API | rpcuser1 |
| RPCPASS | If applicable, password to authenticate with client RPC API | rpcpass1 |
| RPCIP | IP address the full node is listening for RPCs on | 127.0.0.1 |
| RPCPORT | Port the full node is listening for RPCs on | 9332 |
| NETWORK_NAME | Name of the network, this is fixed for the test  |'ETH_MAINNET' |
| DNS_NAME | Unique name for the full node, should include your organization name, e.g, "bestpool-01" | **None** |
| BLOCKCHAIN_LOG | Location of the blockchain client's logs | **None** |
| NTP_TIME | Whether or not to use NTP time, 1 for ntp time | 1 |
