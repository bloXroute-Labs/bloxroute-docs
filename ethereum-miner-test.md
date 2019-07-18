## Installation of the bloXroute Gateway and Blockchain-Logger 

This document describes installation of the bloXroute Gateway and Blockchain-Logger for the bloXroute Ethereum Miner Test. For this test, it is expected that you are running an Ethereum geth node connected to the mainnet. 

The bloXroute Gateway is an application that connects your blockchain full node to the bloXroute block distribution network. Using the Gateway, the block distribution network is designed to propagate blocks to your full node faster than the traditional peer-to-peer blockchain network. It is recommended that you install the Gateway on the same server as your full node, or on a server with low latency to your full node. 

The Blockchain-Logger is a small piece of software created by bloXroute to record block propagation statistics across p2p blockchain networks. It does not provide any block propagation performance improvements and permits us to measure block propagation without the bloXroute network. 

This document describes two ways that you may install the bloXroute Gateway and the Blockchain-Logger, using either packaged docker images, or via a github download. In future versions of the Gateway we expect to provide pre-packaged 'pip install' options. 


## bloXroute Gateway

### Installing the bloXroute Gateway from Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This permits an easy, quick installation. 

This section assumes that you have Docker installed. If you would like to install Docker to use the bloXroute gateway, please visit: https://docs.docker.com/install/  There you can find installation notes for the common versions of Linux, MacOS, etc. 

Currently, we are only distributing our docker image from a private Docker Hub repository. (In the future, we expect to make our docker image public.) Accordingly, to access the docker image you will need a Docker Hub account.

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires port 9001 be open. 

1. Create username/password on Docker Hub (https://hub.docker.com/).

2. Provide bloXroute with username or email address for the account. We will add you to our team so you can access the docker image. 

3. Docker login

    ```
    docker login --username [username]
    ```
	
4. Pull gateway image

   ```
   docker pull bloxroute/bxgateway:latest
   ```
	
5. Run the docker as a daemon. The gateway accepts various parameters as described in the Gateway Parameters section below. The parameters may be passed in the run command to provide the IP address and port of the full node, etc. If using the "external-port" parameter, please update "-p 9001:9001" to "-p (new port):(new port)".
   ```
   docker run -d --name gateway -p 9001:9001 bloxroute/bxgateway:latest --blockchain-protocol Ethereum --blockchain-network Mainnet --blockchain-ip 172.17.0.1 --blockchain-port 30303 
   ```

    Once you've successfully installed the Gateway docker container, please stop the Gateway container and install the Blockchain-Logger. The Gateway will be used only during the second phase of our test and should be stopped during the first phase. You may stop the docker container by running:
    ```
    docker stop -t gateway
    ```

### Installing the bloXroute Gateway From Github

This tutorial assumes that you are installing the Gateway from Github onto linux and will be connecting to the Ethereum daemon on the localhost. These instructions assume that the machine you are installing on already has installations of python 3, git, and pip. 

1. Provide bloXroute with username or email address for the Github account you will use to access the repositories. We will open access for you to our repositories. 

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires port 9001 be open. 

2. Make sure the basic requirements are installed. For example, you may check for the basic requirements using the 'which' command.
    ```
    user@host$ which python3
    /usr/local/bin/python3
    
    user@host$ which git
    /usr/local/bin/git
    
    user@host$ python3 -m pip -V
    pip 19.0.3 from /home/ubuntu/.local/lib/python3.6/site-packages/pip (python 3.6)
    
    ```
    
5. Clone the repository. Please note that our organization requires two-factor authentication.  
    ```
    git clone https://github.com/bloXroute-Labs/bloXroute-miner-test.git
    ```

7. Run the setup script
    ```
    cd bloXroute-miner-test/
    ./gateway-setup.sh
    ```
    
8. Run the Gateway. 
    ```
    ./gateway-run.sh --blockchain-port 30303  
    ```
    The gateway script additionally accepts parameters as described in the Gateway Parameters section below. It is recommended that gateway is run using the 'nohup' or 'screen' commands to permit the gateway to persist even if the terminal is closed. For example,
      ```
      nohup ./gateway-run.sh &
      ```

### Gateway Parameters
**Required Gateway Parameters**

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| blockchain-protocol | Blockchain protocol gateway is connecting to. This test is only on the Ethereum network. | Ethereum |
| blockchain-network | Blockchain network gateway is connecting to (Mainnet or Testnet). This test is only on the Mainnet. | Mainnet |

**Optional Gateway Parameters**

These parameters should not be changed unless needed. 

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| external-port | External port is the port on which the gateway can receive external connections from other gateways in the network. The default is 9001. | 9001 |
| blockchain-ip | The IP address of your Bitcoi Cash full node. Default is "127.0.0.1".  | "127.0.0.1" |
| blockchain-port | The port of your Ethereum full node. Default is 8333. Because we are using the testnet, this should be set to 18333.  | 8333 |
| continent | The continent of your Ethereum full node. Valid `continent` values are `NA` (North America), `SA` (South and Central America), `EU` (Europe), `OC` (Oceania), `AS` (Asia), `AF` (Africa), `AN` (Antarctica). | AS |
| country | The country of your Ethereum full node. | China |





## Blockchain-Logger

### Installing the Blockchain-Logger From Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This section assumes you have Docker installed and that you have created an account with Docker Hub and provided the account name or email address to bloXroute. Additionally, this section assumes that the RPC ports for the full node are open to receive RPC commands. 

1. Please ensure that your geth client has sufficient verbosity for its logs. This can be achieved by start geth with `--verbosity=3` or `--vmodule="miner=3,core=3"`. You may also change verbosity from the `geth attach` console by using the command `debug.verbosity(3)` or `debug.vmodule("miner=3,core=3")`.
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
    BLOCKCHAIN_LOG=/var/ethereum/log/ethereum_debug.log
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
    
1. Please ensure that your geth client has sufficient verbosity for its logs. This can be achieved by start geth with `--verbosity=3` or `--vmodule="miner=3,core=3"`. You may also change verbosity from the `geth attach` console by using the command `debug.verbosity(3)` or `debug.vmodule("miner=3,core=3")`.
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
    export BLOCKCHAIN_LOG=/var/ethereum/log/ethereum_debug.log
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