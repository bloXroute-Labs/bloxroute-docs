## Installation of the bloXroute Gateway and Blockchain-Logger 

This document describes installation of the bloXroute Gateway and Blockchain-Logger for the bloXroute Miner Test. For this test, it is expected that you are running a Bitcoin Cash full node connected to the testnet. 

The bloXroute Gateway is an application that connects your blockchain full node to the bloXroute block distribution network. Using the Gateway, the block distribution network is designed to propagate blocks to your full node faster than the traditional peer-to-peer blockchain network. It is recommended that you install the Gateway on the same server as your full node, or on a server with low latency to your full node. 

The Blockchain-Logger is a small piece of software created by bloXroute to record block propagation statistics across p2p blockchain networks. It does not provide any block propagation performance improvements and permits us to measure block propagation without the bloXroute network. 

This document describes two ways that you may install the bloXroute Gateway and the Blockchain-Logger, using either packaged docker images, or via a github download. In future versions of the Gateway we expect to provide pre-packaged 'pip install' options. 


## bloXroute Gateway

### Installing the bloXroute Gateway from Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This permits an easy, quick installation. 

This section assumes that you have Docker installed. If you would like to install Docker to use the bloXroute gateway, please visit: https://docs.docker.com/install/  There you can find installation notes for the common versions of Linux, MacOS, etc. 

Currently, we are only distributing our docker image from a private Docker Hub repository. (In the future, we expect to make our docker image public.) Accordingly, to access the docker image you will need a Docker Hub account.

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires port 9001 be open. 

1. To ensure that your full node stays connected to the bloXroute Gateway, please whitelist the Gateway. This can be achieved by editing the ‘bitcoin.conf’ file in your bitcoin data directory to include a white list line specifying the IP of the Gateway (or the localhost, if appropriate). An example config line:
    ```
    whitelist=127.0.0.1
    ```

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
   docker run -d -t Gateway -p 9001:9001 bloxroute/bxgateway:latest --blockchain-protocol BitcoinCash --blockchain-network Testnet
   ```

    Once you've successfully installed the Gateway docker container, please stop the Gateway container and install the Blockchain-Logger. The Gateway will be used only during the second phase of our test and should be stopped during the first phase. You may stop the docker container by running:
    ```
    docker stop -t Gateway
    ```

### Installing the bloXroute Gateway From Github

This tutorial assumes that you are installing the Gateway from Github onto linux and will be connecting to the Bitcoin Cash daemon on the localhost. These instructions assume that the machine you are installing on already has installations of python 3, git, and pip. 

1. Provide bloXroute with username or email address for the Github account you will use to access the repositories. We will open access for you to our repositories. 

1. To ensure that your full node stays connected to the bloXroute Gateway, please whitelist the Gateway. This can be achieved by editing the ‘bitcoin.conf’ file in your bitcoin data directory to include a white list line specifying the IP of the Gateway (or the localhost, if appropriate). An example config line:
    ```
    whitelist=127.0.0.1
    ```

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
    ./gateway-run.sh    
    ```
    The gateway script additionally accepts parameters as described in the Gateway Parameters section below. It is recommended that gateway is run using the 'nohup' or 'screen' commands to permit the gateway to persist even if the terminal is closed. For example,
      ```
      nohup ./gateway-run.sh &
      ```

### Gateway Parameters
**Required Gateway Parameters**

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| blockchain-protocol | Blockchain protocol gateway is connecting to. This test is only on the Bitcoin Cash network. | BitcoinCash |
| blockchain-network | Blockchain network gateway is connecting to (Mainnet or Testnet). This test is only on the Testnet. | Testnet |

**Optional Gateway Parameters**

These parameters should not be changed unless needed. 

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| external-port | External port is the port on which the gateway can receive external connections from other gateways in the network. The default is 9001. | 9001 |
| blockchain-ip | The IP address of your Bitcoi Cash full node. Default is "127.0.0.1".  | "127.0.0.1" |
| blockchain-port | The port of your Bitcoin Cash full node. Default is 8333.  | 8333 |
| continent | The continent of your Bitcoin Cash full node. Valid `continent` values are `NA` (North America), `SA` (South and Central America), `EU` (Europe), `OC` (Oceania), `AS` (Asia), `AF` (Africa), `AN` (Antarctica). | AS |
| country | The country of your Bitcoin Cash full node. | China |





## Blockchain-Logger

### Installing the Blockchain-Logger From Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This section assumes you have Docker installed and that you have created an account with Docker Hub and provided the account name or email address to bloXroute. Additionally, this section assumes that the RPC ports for the full node are open to receive RPC commands. 

1. Docker login

    ```
    docker login --username [username]
    ```
	
2. Pull gateway image

   ```
   docker pull bloxroute/blockchain-logger:latest
   ```
 
3. Create a file called logger.env with environment variables as shown below. Please fill in the requested information shown in brackets. See the Logger Environmental Variables table below for a description of each variable. A worked example is also shown below. BLOCKCHAIN_LOG is the log file of the Bitcoin Cash node. It can often be found in the Bitcoin Cash data directory along with the wallet.dat files and blockchain data. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. The RPCUSER and RPCPASS may be found in the bitcoin.conf file also in the Bitcoin Cash data directory. For DNS_NAME, please use the hostname of your full node, or a unique name.
    ```
    NETWORK='btc'
    RPCUSER='[RPC USER NAME]'
    RPCPASS='[RPC PASSWORD]'
    RPCIP='[IP address of Bitcoin Cash full node]'
    RPCPORT='[Bitcoin Cash full node RPC port]'
    NETWORK_NAME='BCH_TESTNET'
    DNS_NAME=[hostname of the machine or a unique name]
    BLOCKCHAIN_LOG=[log location]
    NTP_TIME=1
    ```

    Complete example (Note: the RPCIP is the IP of the host machine from the perspective of docker. You should not have to change it):
    ```
    NETWORK='btc'
    RPCUSER='cKavQG4zBjI2gY1V'
    RPCPASS='l4YYz3DhKrzIyPAd'
    RPCIP='172.17.0.1'
    RPCPORT='8332'
    NETWORK_NAME='BCH_TESTNET'
    DNS_NAME='bch.testnet1.bxrtest.com'
    BLOCKCHAIN_LOG=/var/bitcoin/bitcoin_debug.log
    NTP_TIME=1 
  

4. Run the docker as a daemon. Change "/path/to/your/bitcoin" to point to your bitcoin log directory.
    ```
    docker run -d bloxroute/blockchain-logger:latest -v /path/to/your/bitcoin/bitcoin_debug.log:/var/bitcoin/bitcoin_debug.log --env-file logger.env
    ```


### Installing the Blockchain-Logger From Github 

The Blockchain-Logger collects logs from your Bitcoin Cash full node and provides them to bloXroute. This installation assumes that you are installing the Blockchain-Logger on a server with a running full node. Additionally, these instructions assume that the machine you are installing on already has installations of python 3, git, and pip. Note that the installation installs `psutils`, if this fails then you may need to install the Python Development libraries `python36-devel`. Further, this section assumes that the RPC ports for the full node are open to receive RPC commands. 
    
1. Clone the repository (this is already done if you've already installed the gateway)
   ```
   git clone https://github.com/bloXroute-Labs/bloXroute-miner-test.git
   ```
   
2. Run the Blockchain-Logger installation 
    ```
    ./logger-setup.sh
    ```

4. Edit the environment variables in the `logger-run.sh` file . See the Logger Environmental Variables table below for a description of each variable.  BLOCKCHAIN_LOG is the log file of the Bitcoin Cash node. Please make sure the Blockchain-Logger has permission to read the log file. It can often be found in the Bitcoin Cash data directory along with the wallet.dat files and blockchain data. For our test, NETWORK, NETWORK_NAME, and NTP_TIME are all fixed values as shown below. The RPCUSER and RPCPASS may be found in the bitcoin.conf file also in the Bitcoin Cash data directory. For DNS_NAME, please use the hostname of your full node, or a unique name.
    ```
    export NETWORK='btc'
    export RPCUSER='[RPC USER NAME]'
    export RPCPASS='[RPC PASSWORD]'
    export RPCIP='[IP address of Bitcoin Cash full node]'
    export RPCPORT='[Bitcoin Cash full node RPC port]'
    export NETWORK_NAME='BCH_TESTNET'
    export DNS_NAME=[hostname of the machine or a unique name]
    export BLOCKCHAIN_LOG=[log location]
    export NTP_TIME=1
    ```

    Complete example:
    ```
    export NETWORK='btc'
    export RPCUSER='cKavQG4zBjI2gY1V'
    export RPCPASS='l4YYz3DhKrzIyPAd'
    export RPCIP='127.0.0.1'
    export RPCPORT='8332'
    export NETWORK_NAME='BCH_TESTNET'
    export DNS_NAME='bch.testnet1.bxrtest.com'
    export BLOCKCHAIN_LOG=/var/bitcoin/bitcoin_debug.log
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
| NETWORK | Blockchain network type - for this test 'btc' is used | btc |
| RPCUSER | If applicable, username to authenticate with client RPC API | rpcuser1 |
| RPCPASS | If applicable, password to authenticate with client RPC API | rpcpass1 |
| RPCIP | IP address the full node is listening for RPCs on | 127.0.0.1 |
| RPCPORT | Port the full node is listening for RPCs on | 9332 |
| NETWORK_NAME | Name of the network, this is fixed for the test  |'BCH_TESTNET' |
| DNS_NAME | DNS name of the full node, acts as an identifier in the reporting | **None** |
| BLOCKCHAIN_LOG | Location of the blockchain client's logs | **None** |
| NTP_TIME | Whether or not to use NTP time, 1 for ntp time | 1 |