![Build](https://github.com/bitcoinnanolabs/btco-work-server/workflows/Build/badge.svg)
## run btco work server
```
./btco-work-server  --gpu 0:0:1024 --gpu-local-work-size 16 --listen-address 127.0.0.1:7076
```
details on work generation and the current network difficulty.
```
LIVE_DIFFICULTY: u64 = 0xffc0000000000000;
LIVE_RECEIVE_DIFFICULTY: u64 = 0xf000000000000000;
```
This project is a dedicated work server for [the btco cryptocurrency](https://bitcoinnano.org/).

**btco-work-server** supports the `work_generate`, `work_cancel`, and `work_validate` commands from the btco RPC.
For details on these commands, see [the btco RPC documentation](https://docs.btco.org/commands/rpc-protocol/).

To see available command line options, run `btco-work-server --help`.

If using more than one work peer, give the flag `--shuffle`. This makes it so that the next request is picked randomly instead of sequentially, which leads to more efficient work generation with multiple peers, especially when they are not in the same network.

## Current base difficulty

`0xffc0000000000000` 

## Installation

### OpenCL 

Ubuntu:

```
sudo apt install ocl-icd-opencl-dev
```

Fedora:

```
sudo dnf install ocl-icd-devel
```

Windows:
- AMD GPU: [OCL-SDK](https://github.com/GPUOpen-LibrariesAndSDKs/OCL-SDK/releases/)
- Nvidia GPU: [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit)

### Rust

Linux:

```
curl https://sh.rustup.rs -sSf | sh
```

Windows: follow instructions in https://www.rust-lang.org/tools/install

### GCC

Ubuntu:

```
sudo apt install gcc
```

Fedora:

```
sudo dnf install gcc
```

### Build

```bash
git clone https://github.com/bitcoinnanolabs/btco-work-server.git
cd btco-work-server
cargo build --release
```

Depending on your system configuration and if the OpenCL library cannot be found in the `PATH`, it may be necessary to link against explicitly:

```bash
cargo rustc --release -- -l OpenCL -L "/path/to/opencl.lib"
```

## Using

`btco-work-server --help`

_Note_ difficulty values may be outdated in these examples.

- `work_generate` example:

    ```json
    {
        "action": "work_generate",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2",
        "difficulty": "ffffffc000000000",
        "multiplier": "1.0" // overrides difficulty
    }
    ```
    Response:

    ```json
    {
        "work": "2bf29ef00786a6bc",
        "difficulty": "ffffffd21c3933f4",
        "multiplier": "1.3946469"        
    }
    ```


- `work_validate` example:

    ```json
    {
        "action": "work_validate",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2",
        "work": "2bf29ef00786a6bc",
        "difficulty": "ffffffc000000000",
        "multiplier": "1.0" // overrides difficulty
    }
    ```
    Response:

    ```json
    {
        "valid_all": "1",
        "valid_receive": "1",
        "difficulty": "ffffffd21c3933f4",
        "multiplier": "1.3946469"
    }
    ```

- `work_cancel` example:
    ```json
    {
        "action": "work_cancel",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2"
    }
    ```
    Response:

    ```json
    {
    }
    ```

## Benchmarking

Example request:

```json
{
    "action": "benchmark",
    "count": "10"
}
```

_Note_ use a sufficiently high count as work generation is a random process.

Example response:

```json
{
    "average": "481",
    "count": "10",
    "difficulty": "fffffff800000000",
    "duration": "4813",
    "hint": "Times in milliseconds",
    "multiplier": "1"
}
```

## Status

Example request:

```json
{
    "action": "status"
}
```

Example response:

```json
{
    "generating": "1",
    "queue_size": "3"
}
```

## Troubleshooting

- Linux OpenCL AMD GPU series error: `thread 'main' panicked at 'Failed to create GPU from string "00:00"` - see [solution here](https://github.com/bitcoinnanolabs/btco-work-server/issues/28)
