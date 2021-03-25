# Bitcoin SV Node

## Regtest (multi-node)

This repository allows you run a full bitcoin network in an isolated environment. It uses bitcoin SV's regtest capability to setup an isolated bitcoin network, and then uses docker to setup a network with 2 nodes.

This is useful because normally in regtest mode you would generate all coins in the same wallet as where you'd send the coins. With this setup, you can use one node to generate the coins and then send it to one of the other nodes, which can then again send it to another node to simulate more real-life bitcoin usage.

Simple run

```
docker-compose up
```

to start all the containers. This will start the bitcoin nodes, and expose RPC on all of them. The nodes will run on the following ports:

| Node  | P2P port \* | RPC port \* | RPC Username | RPC Password |
| ----- | ----------- | ----------- | ------------ | ------------ |
| node1 | 18332       | 18333       | bitcoin      | bitcoin      |
| node2 | 18501       | 18401       | bitcoin      | bitcoin      |
| node3 | 18502       | 18402       | bitcoin      | bitcoin      | 

\* Port as exposed on the host running docker.

### Samples

Note these samples use `curl` to exercise the API, but this would usually be `bitcoin-cli`. We're using `curl` so we don't have a dependency on bitcoin in the host.

You can also use the `b.sh` script passing any parameters. `b.sh` makes a call to the `bitcoin-cli` on `regtest_node1_1`

eg `./b.sh getinfo`

#### Initial block count

Checks that the initial block count is 0.

```
# docker-compose up -d
Creating regtest_node1_1 ... done
Creating regtest_node2_1 ... done
Creating regtest_node3_1 ... done
# curl -d '{"jsonrpc":"2.0","id":"1","method":"getblockcount"}' -u bitcoin:bitcoin localhost:18332
{"result":0,"error":null,"id":"1"}
```

#### Check connected nodes

Check the "miner" node is connected to other nodes.

```
# curl -d '{"jsonrpc":"2.0","id":"1","method":"getpeerinfo","params":[]}' -u bitcoin:bitcoin -s localhost:18332 | jq '.result[] | {addr, inbound} '
{
  "addr": "node2:18333",
  "inbound": false
}
{
  "addr": "172.20.0.2:48504",
  "inbound": true
}
{
  "addr": "node3:18333",
  "inbound": false
}
{
  "addr": "172.20.0.4:57912",
  "inbound": true
}
```
