## Building the source

For prerequisites and detailed build instructions please read the [Installation Instructions](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum) on the wiki.

Building `geth` requires both a Go (version 1.10 or later) and a C compiler. You can install
them using your favourite package manager. Once the dependencies are installed, run

```shell
make geth
```

or, to build the full suite of utilities:

```shell
make all
```

# StartUp

```
geth  --identity node1  --port 30303  --rpc --rpcaddr 0.0.0.0 --rpcport 8546 --rpccorsdomain "*" --rpcapi eth,personal,web3,net --allow-insecure-unlock  --datadir ./nodes/node1  --ipcpath /tmp/nodes/node1   --nousb  --gasprice 0 --networkid 12138 --verbosity  5 --nodiscover --syncmode full  console

geth  --identity node2  --port 30304   --datadir ./nodes/node2  --ipcpath /tmp/nodes/node2   --nousb  --gasprice 0 --verbosity 5 --nodiscover --networkid 12138   --syncmode full   console

geth  --identity miner_node  --port 30305   --datadir ./nodes/miner_node  --ipcpath /tmp/nodes/miner_node   --nousb --syncmode full  --gasprice 0  --networkid 12138 --nodiscover --verbosity 5
  --syncmode full   console
  
```



# 改了啥

- 加了一些log

- ProtocolManager 初始化的时候, 置acceptTxs为1

  节点启动后会连接static-nodes中的其他peer, 然后把自己peeding中的tx打包成个batch发过去.

  问题是, 其他节点启动的时候acceptTxs为0, 就算接收成功了, 也会丢弃掉.

  这个acceptTxs只有在 ( 开启miner后 | 和其他节点同步过了 ( 本节点的高度<其他节点的高度) ) 后才会被开启, 开启后, 才会接受其他节点的 tx 到本节点的 txpool 的 quened 队列中, 到quened中的还有可能 (nonce 没有递增) 会被踢掉, 剩下的才会进入pending中.
  
  > https://ethereum.stackexchange.com/questions/3831/what-is-the-max-size-of-transactions-can-clients-like-geth-keep-in-txpool
  >
  > 解释了nonce 对, quened promote to pending 的机制
  >
  > 
  >
  > https://blog.csdn.net/lj900911/article/details/84568054
  >
  > 解释了 acceptTxs是节点是否接受交易的阀门，只有当pm.acceptTxs == 1时，节点才会接受交易。这个操作只会在同步结束后再开始，即同步的时候节点是不会接受交易的；
  
  

