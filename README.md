# DAML on Hyperledger Fabric Example

## Sections

-  [Prerequisites](#prerequisites)
-  [Setup and Run Fabric](#setup-fabric)
-  [Get DAML runtime](#get-daml-on-fabric)
-  [Write DAML smart contract (.daml file)]()
-  [Deploy DAML smart contract](#deploy-daml-application)
-  [Create JSON API to interact with Fabric ledger](#create-json-api)
-  [Use DAML Navigator or use own Frontend application](#navigator)

## Prerequisites

Referece [DAML on Fabric](https://github.com/digital-asset/daml-on-fabric).

To go ahead with this, we need the following tools are required. For installing them please refer respective manuals.

-  **docker CE** version 18.09.6 or higher
-  **docker-compose** version 1.24.0. or higher
-  **DAML SDK** version 1.2.0
-  **Hyperledger Fabric Tools** version 2.0.0 or higher

## Setup Fabric

Run the following commands to get the latest Hyperledger Fabric Tools. It downloads necessary tools and binaries to the <download_directory>/bin directory.

```bash
cd <download_directory>
curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s -- 2.1.0 -s
```

Add the bin path to your PATH variable by the following way.

```bash
export PATH=<download_directory>/bin:$PATH
```

To know more on Hyperledger Fabric and its installation: https://hyperledger-fabric.readthedocs.io/en/latest/install.html

## Get DAML on Fabric

Create a seperate directory for DAML on Fabric and change into it.

```bash

cd
git clone https://github.com/digital-asset/daml-on-fabric.git
```

### Run Hyperledger Fabric

To use DAML on Fabric, we can either use a new Fabric network or can use an already up and running Fabric network. In this tutorial we use the Fabric network setup provided with DAML on Fabric project to reduce the complexity for the beginners.

Fabric is usually setup by docker-compose tool. The following setup spins up 2 endorsing peers.

```bash

cd <download_directory>/daml-on-fabric/src/test/fixture/
./gen.sh
./restart_fabric.sh
```

The _restart_fabric.sh_ script used above is a shortcut for

> _fabric.sh down && fabric.sh up_

script which erases all current data and starts the network afresh.

### Connect DAML runtime with the Fabric Network

Please follow the [deployment guide](https://github.com/digital-asset/daml-on-fabric/blob/master/DEPLOYMENT_GUIDE.md) for a general process of deploying a full E2E application.

Run the following command from the root directory of _daml-on-fabric_.

```bash
run --port 6865 --role provision,time,ledger" -J-DfabricConfigFile=config-local.yaml
```

_config-local.yaml_ file in the above command provides the specification for the Fabric network to the DAML runtime to connect/interact. This makes the DAML on the fabric runtime to talk to the Fabric.

The command format:

```bash
sbt "run --role <roles> [--port NNNN] [DAMLArchive.dar DAMLArchive2.dar ...]"
```

## Deploy DAML Application

_my-app_ directory contains our DAML smart contract _Value.daml_ file. In this step we are deploying our DAML contract on the Fabric. This step includes compiling of DAML file Value.daml into a .dar file.

To accompany this, from the _my-app_ directory run

```bash
daml deploy --host localhost --port 6865
```

If we want to compile the .daml file, we can use _daml build_ command which compiles and generates .dar file.

## Create JSON API

To be able co communicate with Fabric ledger, we can automatically create a JSON API. The following command generates JSON API and serve a JSON endpoint. Our UI component will access and use this exposed JSON API to interact with the ledger.
Issue the following command to auto generate the JSON API.

```bash

daml json-api --ledger-host localhost --ledger-port 6865 --http-port 7575
```

## Navigator

On top of the JSON API we can develop our own frontend UI applications. But for the purpose of this tutorial, we are going to use the _Navigator_ tool which is provided with _DAML on Fabric_. This provides a basic UI to interact with the deployed ledger and our business logic application. To start up the navigator: cd <your app directory>

```bash

cd my-app
daml navigator server localhost 6865 --port 4000
```

The navigator is available on localhost port 4000. Noe we can exxecute our contracts from this UI.
