
# witness_node launch FAQ

## 1. How to start the witness_node program normally
   
It is recommended to start the program as follows (depending on your own situation, modify the parameter values ​​and port numbers as appropriate)

```bash
export LC_ALL=C
nohup ./programs/witness_node/witness_node --data-dir=trusted_node  --rpc-endpoint="0.0.0.0:28090" --p2p-endpoint="0.0.0.0:6789" --track-account "\"1.2.241993\"" --max-ops-per-account=-1  --fast-replay 1>nohup.out 2>&1 &
```

Start up as above, the console output will be redirected to nohup.out, if there is an exception, you may view the nohup.out location problem.

## 2. How to judge the witness_node is running normally
   Observe the background log file `trusted_node/logs/witness.log`. If you continue to receive the block on the network, it means the node is running normally, as follows
```
2018-08-23T07:10:39 th_a:invoke handle_block         handle_block ] Got block: #6939202 time: 2018-08-23T07:10:39 latency: 29 ms from: init6  irreversible: 6939194 (-8)		application.cpp:487
2018-08-23T07:10:42 th_a:invoke handle_block         handle_block ] Got block: #6939203 time: 2018-08-23T07:10:42 latency: 30 ms from: init4  irreversible: 6939195 (-8)		application.cpp:487
2018-08-23T07:10:45 th_a:invoke handle_block         handle_block ] Got block: #6939204 time: 2018-08-23T07:10:45 latency: 32 ms from: init0  irreversible: 6939195 (-9)		application.cpp:487
2018-08-23T07:10:48 th_a:invoke handle_block         handle_block ] Got block: #6939205 time: 2018-08-23T07:10:48 latency: 34 ms from: init5  irreversible: 6939197 (-8)		application.cpp:487
2018-08-23T07:10:51 th_a:invoke handle_block         handle_block ] Got block: #6939206 time: 2018-08-23T07:10:51 latency: 34 ms from: init1  irreversible: 6939198 (-8)		application.cpp:487
2018-08-23T07:10:54 th_a:invoke handle_block         handle_block ] Got block: #6939207 time: 2018-08-23T07:10:54 latency: 36 ms from: bob  irreversible: 6939199 (-8)			application.cpp:487
2018-08-23T07:10:57 th_a:invoke handle_block         handle_block ] Got block: #6939208 time: 2018-08-23T07:10:57 latency: 37 ms from: init1  irreversible: 6939200 (-8)		application.cpp:487
2018-08-23T07:11:00 th_a:invoke handle_block         handle_block ] Got block: #6939209 time: 2018-08-23T07:11:00 latency: 39 ms from: init7  irreversible: 6939200 (-9)		application.cpp:487
2018-08-23T07:11:03 th_a:invoke handle_block         handle_block ] Got block: #6939210 time: 2018-08-23T07:11:03 latency: 41 ms from: bao  irreversible: 6939201 (-9)			application.cpp:487
2018-08-23T07:11:06 th_a:invoke handle_block         handle_block ] Got block: #6939211 time: 2018-08-23T07:11:06 latency: 41 ms from: init8  irreversible: 6939202 (-9)		application.cpp:487
2018-08-23T07:11:09 th_a:invoke handle_block         handle_block ] Got block: #6939212 time: 2018-08-23T07:11:09 latency: 44 ms from: init2  irreversible: 6939203 (-9)		application.cpp:487
2018-08-23T07:11:12 th_a:invoke handle_block         handle_block ] Got block: #6939213 time: 2018-08-23T07:11:12 latency: 45 ms from: init4  irreversible: 6939204 (-9)
```
## 3. Error when blocking a block
   Observe the background log file `trusted_node/logs/witness.log`. If the log continues to report errors, such as `unlinkable block", "block does not link to known chain", this is a block synchronization error.

   Solution:

   - First close the program, use ```--replay-blockchain``` to restart, wait for the startup to complete, observe whether the history.log log is normal.

   - Block sync exception, it is possible that the local blockchain file is broken. Need to stop the `witness_node` program, then delete the `trusted_node` directory and restart `the witness_node`.

   - Or you do not need to delete the `trusted_node` directory. The startup command plus the parameter `--resync-blockchain` will resynchronize the block.

## 4. How to properly shut down witness\_node

   Witness_node saves all data in the form of an object in memory. When the program exits normally, the data in the memory is written to the disk, so the process cannot be forced to be killed, otherwise the memory database will be broken.

   - If the `witness_node` is not running in the background, execute `Ctrl + C` once and then wait for the program to save the memory data and then exit automatically.

   - If the `witness_node` is running in the background, execute `kill -s SIGINT $(pgrep witness_node)`, and wait for the program to save the memory data and then exit automatically. You can't use kill -9, otherwise the next time you start the index, the index will be rebuilt.

## 5. Witness_node process is normal, but does not receive blocks from the network

   - Check the background log file, found no error message, and the node did not receive a new block from the network. At this point, your version of witness_node may be low. Please visit the [github release page] (https://github.com/gxchain/gxb-core/releases/latest) to download the latest package.

   - You can view the duration_node version with the -v parameter:
```bash
$./programs/witness_node/witness_node -v
Version: 1.0.180713-379-g4c9a2f4
SHA: 4c9a2f4e168503abe3ce1432c699f88b8babe356
Timestamp: 4 hours ago
SSL: OpenSSL 1.0.2o  27 Mar 2018
Boost: 1.67
Websocket++: 0.7.0
```
## 6.What operating system does BP use, and what are the requirements for hardware including bandwidth?
   - ubuntu 14.04 64 os
   - 4 core 32G memory 200G hard disk, 50MB+ bandwidth, according to the flow rate, double machine disaster recovery
   - Can use cloud server

## 7. When the witness_node starts, the port error is reported.
   Check if multiple witness_nodes are started on the server, causing port conflicts.
```bash
ps xf | grep witness_node
```
   If you have started multiple witness_nodes, you need to close them all and restart them:
```bash
for i in $(pgrep witness_node); do echo kill -9 $i; done
```
   
## 8. Witness_node failed to start, no log, process does not exist
Modify the local config.ini file as follows:
```
[logger.default]
level=debug
appenders=stderr,FILE
```
Then restart, the error message will be printed to the console