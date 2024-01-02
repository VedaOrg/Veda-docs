# Quick Start

## Installation

This guide teaches how to use deploy a BVM node. We suggest you to use Docker rather than baremental way. All instructions are based on Ubuntu.

### Docker

Install docker will not be disscussed here. Please refer to [docker official website](https://docs.docker.com/engine/install/) for more details.

Change directory to `veda-bvm` folder and run:

```bash
docker-compose up -d
```

The docker will automatically pull or buid the image and run it.

### Executable arguments

## Services

| Port | Description                        | External Service |
|------|------------------------------------|------------------|
| 8545 | Veda RPC HTTP Sevice               | ✓                |
| 8679 | Internal RPC Service for veda-core | ✗                |

### Internal RPC
Interal RPC service is used to communicate between veda-bvm and veda-core. You can also try your BVM calls directly to this service.

The internal RPC service complies with JSONRPC 2.0 protocol, and it's listening on port `8679`.

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

`veda-bvm/scripts/tests/testinternalrpc.py` has a simple example showing how to use the internal RPC service.

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

This function shows a basic example of synchronizing a block.

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
                'to': '0xe0E09f974F6B8C35a9c73fbbC3433F7ef83e4d09', 
                'sender': '0x' + '00' * 19 + '04',
                'nonce': 0,
                'txHash': '0x' + '00' * 31 + '04',
                'data': '0x123456',
            }
        ]
      ]
    }

    ret = requests.post('http://YOUR_DOCKER_IP:8679/', json=jsonrpc_payload).json()
    return ret
```

Combined above codes, and we get

```python
ret = internal_get_block()
print(ret)
veda_block_number = ret['result']['veda_block_number']
print('veda_block_number', veda_block_number)
print(internal_rpc_simple(veda_block_number))
```



### Veda RPC
TBD
