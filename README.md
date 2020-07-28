# README #

Currently, the only way to install the ```cardano-node``` and the ```cardano-cli```, necessary to run a stake pool on Shelley, is to compile them. Compilation can be troublesome for some and lead to issues. To reduce complexity and have predictable results, this setup uses a Docker build environment. This setup will always build against the latest ```cardano-node``` version and ```pioneer-*``` tag as in [the official documentation](https://github.com/input-output-hk/cardano-tutorials/blob/master/node-setup/build.md).

**IMPORTANT: you must run these steps to compile ```cardano-node``` and ```cardano-cli``` on your local computer, and copy them over to your servers. This is to avoid useless load and installing compilation software on your servers.**

## Install docker ##

Install and run Docker Desktop for your OS from [the official site](https://www.docker.com/products/docker-desktop), on your local computer.

## Clone the build repo ##

```bash
git clone https://github.com/gacallea/cardanoNodeBuilder.git
```

## Compile the software ##

```bash
cd cardanoNodeBuilder
docker-compose up --build -d
```

## Copy the binaries to your host ##

```bash
docker container cp build_builder_1:/usr/local/bin/* cardano-bins/
```

## Test cardano-node locally 

Follow below steps to test cardano-node testnet locally. Note, this setup is not expected for live mainnet setup. Follow steps on [Mainnet stake pool setup](#mainnet-stake-pool-setup)

```
cd cardano-bins
```

**Verify cardano-node & cardano-cli version**

Verify cardano-node and cli ouput version: **1.18.0**

```bash
./bin/cardano-node --version
```

```bash
./bin/cardano-cli --version
```

**Download configs**

Download the [official cardano configurations](https://hydra.iohk.io/build/3627488/download/1/index.html) based on your desired cluster target. For this setup we will use `testnet` configs

```
wget https://hydra.iohk.io/build/3627488/download/1/testnet-config.json -P configs
wget https://hydra.iohk.io/build/3627488/download/1/testnet-byron-genesis.json -P configs
wget https://hydra.iohk.io/build/3627488/download/1/testnet-shelley-genesis.json -P configs
wget https://hydra.iohk.io/build/3627488/download/1/testnet-topology.json -P configs
```


### Optionally - tweak config 

If you prefer graphical interface, You can update the `configs/testnet-config.json` `ViewMode` config to `LiveView` 


### Create db and socket files

The `db` is to store the blockchain information and `socket` for node socket.

```
mkdir -p db socket
```

### Run node

Start node on port **3000** with the downloaded testnet configs.


```
./bin/cardano-node run --database-path ./db/core/ --socket-path ./socket/core --port 3000 --config configs/testnet-config.json  --topology configs/testnet-topology.json
```

If everything went right you should see node starts syncing.


## Mainnet stake pool setup

### Copy the binaries to your nodes ##

**NOTE: Make sure you point to your actual servers.**

```bash
scp cardano-bins/bin/cardano-node node-server:/usr/local/bin/
scp cardano-bins/bin/cardano-cli node-server:/usr/local/bin/
```

```bash
scp cardano-bins/bin/cardano-node relay-server:/usr/local/bin/
scp cardano-bins/bin/cardano-cli relay-server:/usr/local/bin/
```

### Configure nodes

You can follow [this setup instructions](https://cardano-node-installation.stakepool247.eu/cardano-node-configuration/cardano-node-configuration) to configure nodes properly for live mainnet nodes stake pool operation.
