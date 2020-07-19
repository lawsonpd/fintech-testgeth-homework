# fintech-testgeth-homework
Create an ETH test network and send transactions

## Overview

In this project we build an Ethereum test network for a hypothetical bank called ZBank. The bank is interested in learning more about blockchains and Ethereum in particular, so we set up a private network and experiment with transactions.

Ethereum is a blockchain similar to Bitcoin, with the exception that it's programmable and the network as a whole forms a Turing-complete computational machine. For this experiment we don't use smart contracts (the programmable part of Ethereum) but instead use MyCrypto to send a transaction from one node of the network to another.

### Geth and Puppeth

Geth and Puppeth are the two tools we use to construct the network. 

Geth is the official implementation of Ethereum using Google's Go programming language. It is the main tool for building the components of a network. We use it to instantiate and run the nodes.

Puppeth ships with Geth and simplifies the process of creating a private test network. We use Puppeth to create and initialize the network.

## Building znet (our test network)

The steps we follow are

* Create two nodes using Geth
* Create a network using Puppeth
* Initialize and run the nodes
* Send a transaction between nodes

All commands are executed in the terminal.


### Creating the nodes

Initializing a node is simple with Geth. We run `geth account new --datadir znode1`, and again with `znode2`, to create the nodes. These nodes are the two components of our test network. They are both capable of mining Ether, listening for transactions on the network and recording transactions & blocks to our private blockchain.

### Creating the network

We then run `puppeth`, which will walk us through the steps to create a network. There are several options at each step, but the main options for our purposes are

* Configure a new genesis [create the first block on the chain]
* Choose the Proof-of-Authority consensus algorithm
* Allow both nodes to seal [i.e. confirm transactions]
* Prefund both nodes [with Ether]

This generates a configuration file named `znet.json` that holds information specific to our private network.

### Initialize the nodes

We created two nodes with Geth, but we still have to put them together, so to speak, to build the network. We do this by running `geth init znet.json --datadir znode1` (and again with `znode2`). `znet.json` has information about our network that Geth needs to generate the genesis block and the database that will hold the blocks created by the nodes. For each node we provide a password that protects its credentials.

### Running the network

We can now put our nodes "online" (privately) and begin listening for transactions, mining Ether and recording blocks. We do this with 

`geth --datadir znode1 --mine --miner.threads 1 --unlock <node1 address> --rpc --allow-insecure-unlock` 

and 

`geth --datadir znode2 --mine --miner.threads 1 --unlock <node2 address> --port 30304 --bootnodes <node1 enode address>`.

The `--mine` flag tells Geth to use this node to mine, and `--miner.threads 1` tells it to use 1 CPU thread to mine. We use `--unlock` to authorize the node (this is part of the Proof-of-Authority consensus algorithm). The `--rpc` flag tells it to use the RPC protocol for communicating between the nodes. For `znode2`, we specify a port that doesn't conflict the port used by `znode` and we pass it `znode1`'s enode address so that it knows where to find `znode1`.


### Making a transaction

Now that our network is online, we can connect to it with the MyCrypto desktop app. In MyCrypto we set up a custom node using the name, local address/port and chain ID of our network from the configuration file (`znet.json`) created by Puppeth.

Once connected, we open the wallet associated with `znode1`. Geth created a keystore file for each node, which we can use, along with the respective password, to open it. When we open the wallet in MyCrypto, we can see the balance and history of transactions and can also send Ether to any other node on our network. Since Puppeth pre-funded each node we created, we have ample Ether to send. (Note that since this is a private test network, our Ether has no value *outside* of our znet network).

As a test, we send 1000 ETH from znode1 to znode2. Over in our terminal, where we have each node running, we can see the transaction details showing the send and receipt addresses (namely, the addresses associated with znode1 and znode2). Success!

Once the transaction is confirmed by our network, we can see the results in MyCrypto under the "TX Status" pane. Included are the typical details of an Ether transaction, including the transaction hash and the transaction fee.

## Summary

Our private Ethereum network experiment was successful. We were able to initialize two nodes, run a network and send a transaction between the nodes.

Building a live Ethereum network will certainly require more security measures and networking infrastructure, but we can see from the test that the basic setup is not infeasible.