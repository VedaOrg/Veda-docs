# Quick Start

## Deployment

This guide provides instructions on deploying a BVM node, with a strong recommendation to utilize Docker instead of bare-metal deployment. All instructions presented herein are tailored for the Ubuntu operating system.

### Docker

Install docker will not be disscussed here. Please refer to [docker official website](https://docs.docker.com/engine/install/) for more details.

Change directory to `veda-bvm` folder and run:

```bash
docker-compose up -d
```

The Docker container will autonomously retrieve or build the image before executing it.

### Baremental
Install python 3.9 or later is required, and simply install the package:

```bash
python3 setup.py install
```

after that, you can run the veda-bvm by:

```bash
veda --veda-root-dir=/tmp/evmdata --data-dir=/tmp/evmdata --enable-http-apis=eth,veda
```

You will see the output on the console like the following:

```bash

    INFO  2024-01-03 14:00:58,123                  Veda  
      _   __       __    
     | | / /__ ___/ /__ _
     | |/ / -_) _  / _ `/
     |___/\__/\_,_/\_,_/ 
                         
    
    INFO  2024-01-03 14:00:58,124                  Veda  Started main process (pid=51382)
    INFO  2024-01-03 14:00:58,124                  Veda  Veda DEBUG log file is created at /private/tmp/evmdata/logs-veda1/veda.log
    INFO  2024-01-03 14:00:58,131           IPCListener  Starting <veda._utils.logging.IPCListener object at 0x111c2c0a0> server over IPC socket: /private/tmp/evmdata/ipcs-veda1/logging.ipc
    INFO  2024-01-03 14:00:59,486                  veda  Started DB server process (pid=51384)
    INFO  2024-01-03 14:01:01,045             DBManager  Starting <veda.db.manager.DBManager object at 0x10bcb9e20> server over IPC socket: /private/tmp/evmdata/ipcs-veda1/db.ipc
    INFO  2024-01-03 14:01:01,096      ComponentManager  Starting components: Sync HTTP Service/JSON-RPC API
    INFO  2024-01-03 14:01:01,096      ComponentManager  Components started
    INFO  2024-01-03 14:01:02,458          JSON-RPC API  JSON-RPC modules exposed via HTTP: ('eth', 'veda')
    INFO  2024-01-03 14:01:02,460             IPCServer  IPC started at: /private/tmp/evmdata/ipcs-veda1/jsonrpc.ipc
    INFO  2024-01-03 14:01:02,460            HTTPServer  Running HTTP Server(JSONRPC) 0.0.0.0:8545
    INFO  2024-01-03 14:01:02,460     Sync HTTP Service  Internal RPC Server exposed via HTTP: 127.0.0.1:8679
    INFO  2024-01-03 14:01:02,461             IPCServer  IPC started at: /private/tmp/evmdata/ipcs-veda1/internal.ipc
    INFO  2024-01-03 14:01:02,461            HTTPServer  Running HTTP Server(Syncer) 127.0.0.1:8679
```

### Executable arguments

## Services

| Port | Description                        | External Service |
|------|------------------------------------|------------------|
| 8545 | Veda RPC HTTP Sevice               | ✓                |
| 8679 | Internal RPC Service for veda-core | ✗                |

### Internal RPC
The Internal RPC service serves as the communication bridge connecting `veda-bvm` and `veda-core`. Alternatively, you have the option to make direct BVM calls to this service.

This internal RPC service strictly adheres to the JSON-RPC 2.0 protocol and is configured to listen on port `8679`.

#### RPC Methods
| Method | Description                    | Params                   | Return      |
|--------|--------------------------------|--------------------------|-------------|
| `sync`  | Synchronize the latest block. Returning `None` indicates success, returning anything else indicates failure, and the returned content is the reason for the failure.   | `SyncBlockModel`                   | `None` or `String` |
| `get_latest_block` | Get latest synchronized block number  | `null`                   | `number` |

#### Types

##### SyncBlockModel
| Field | Type   | Description                    |
|-------|--------|--------------------------------|
| `blockHash` | `string` | Block hash                     |
| `blockNumber` | `number` | Block number                    |
| `mixHash` | `string` | Prevrandao of the synchronizing block                        |
| `timestamp` | `number` | Timestamp of the synchronizing block                        |


#### Exceptions

| Type | Message Template |
|------|------------------|
| TypeError | `Invalid parameters. Check parameter count and types. {exc}`|
| NotImplementedError | `Method not implemented: {method} {exc}` |
| ValidationError | `Validation error while executing RPC method` |
| Internal Exception | `RPC method caused exception` |

#### Examples

The file `veda-bvm/scripts/tests/testinternalrpc.py` provides a straightforward illustrative case that demonstrates the utilization of the internal RPC service.

This function is used to obtain the latest synchronized block number.

```python
def internal_get_block():
    jsonrpc_payload = {
        "id": 1,
        "jsonrpc": "2.0",
        "method": "get_latest_block",
        "params": []
    }

    ret = requests.post('http://YOUR_DOCKER_IP:8679/', json=jsonrpc_payload).json()
    return ret
```

This function provides a fundamental illustration of invoking a method, such as `mint` within a VRC20 contract by synchronizing with a blockchain block.

```python
def internal_rpc_simple(veda_block_number):
    jsonrpc_payload = {
      "id": 1,
      "jsonrpc": "2.0",
      "method": "sync",
      "params": [
        {
          "blockHash": '0x' + '00' * 31 + '1f',
          "blockNumber": veda_block_number + 1,
          'mixHash': '00' * 31 + '6f', 
          "timestamp": 123123123123
        },
        [
            {
                'to': 'TARGET_VRC20_CONTRACT_ADDRESS', 
                'sender': '0x' + '00' * 19 + '04',
                'nonce': 0,
                'txHash': '0x' + '00' * 31 + '04',
                'data': 'MINT_CALL_BYTECODE_HERE',
            }
        ]
      ]
    }

    ret = requests.post('http://YOUR_DOCKER_IP:8679/', json=jsonrpc_payload).json()
    return ret
```

By integrating the aforementioned code snippets, we obtain the following result:

```python
ret = internal_get_block()
print(ret)
veda_block_number = ret['result']['veda_block_number']
print('veda_block_number', veda_block_number)
print(internal_rpc_simple(veda_block_number))
```

You can locate the logs within the `${ROOT_DIR}/logs_veda1/` directory. This is where you can access the log files.

```bash
   DEBUG  2024-01-03 14:08:57,310     InternalRPCServer  Syncing block 822268
   DEBUG  2024-01-03 14:08:57,329     InternalRPCServer  Block #822268-0x0000..001f contains 1 transactions, 1 succeeded, veda blockHash: 0x000000000000000000000000000000000000000000000000000000000000001f
   DEBUG  2024-01-03 14:08:57,331           rpc_handler  Receiving POST request: /
```

If a transaction within the submitted block encounters a ValidationError, such as a bad nonce, the error will be duly logged. You may access the corresponding log entries within the 'logs-veda1/' directory as follows.

```bash
   DEBUG  2023-12-24 18:08:28,426                VedaVM  Transaction b"XXXXX" raise an validation error, reason: Invalid transaction nonce: Expected 1, but got 0
```

You can also perform queries on the `LOG` generated by the contract and invoke certain **readOnly** methods, such as `balanceOf`, associated with the contract. The contract's address is generated in accordance with Ethereum's rule: `kec256(abi.encodePacked(address, nonce))`.

Query `LOG` and `balanceOf` example:
```python
from web3 import Web3

contract_abi = [ ... YOUR_CONTRACT_ABI ... ]

# Dummy addresses
contract_address = '0x5faa1ff9cb31ff3a32b347a26476bb52ae4bf353'
account_address = '0x5096950709f0085221847c1618aed1fa4ab9e1da'

w3 = Web3(Web3.HTTPProvider('http://127.0.0.1:8545/'))
contract = w3.eth.contract(address=Web3.to_checksum_address(contract_address), abi=contract_abi)

# Query logs
logs = w3.eth.get_logs({
    'fromBlock': '0xc8bfc', # 822268
    'toBlock': '0xc8bfc', # 822268
    'address': Web3.to_checksum_address(contract_address),
})

# Call balanceOf through eth_call
balance = contract.functions.balanceOf(Web3.to_checksum_address(account_address)).call()
```



### Veda RPC
TBD
