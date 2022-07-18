# firefly-fabconnect

En esta documentación mostramos varias vias para desplegar firefly-fabconnect:
- Usando `FireFly CLI`
- Usando `fabconnect` y la `test-network` de fabric-samples
- Usando `fabconnect` y la `test-network-nano-bash` de fabric-samples

 > **NOTA**: El `FireFly CLI` despliega ademas del fabconnect un nodo del firefly-core, firefly-dataexchange-https, ipfs y el firefly-sandbox.

## Table of Contents

- [Usando firefly-cli](#fireflycli)
  * [Descarga e instala el __CLI__](#descarga_instalacion)
  * [Crea un stack de __Hyperledger Fabric__](#stack_fabric)
  * [Ficheros de configuración](#ficheros_config)
- [Usando fabconnect y test-network](#fabconnect_testnetwork)
  * [Descargue fabric-samples, imágenes de docker y los binarios de fabric](#fabconnect_testnetwork_descarga_prerequisitos)
  * [Iniciar la red de prueba `"test-network"`](#fabconnect_testnetwork_iniciar_testnet)
  * [Descargar la configuración](#fabconnect_testnetwork_descarga_fabconnect)
  * [Iniciar `fabconect`](#fabconnect_testnetwork_iniciar_fabconnect)
  * [Interactuando con el chaincode `asset-transfer-basic`](#fabconnect_testnetwork_interactuando_cc)
- [Usando fabconnect y test-network-nano-bash](#fabconnect_testnetworknanobash)
- [Others commands](#others)
- [Documentación](#doc)

<br/>

## Usando firefly-cli<a name="fireflycli"></a>

### Descarga e instalación del __CLI__<a name="descarga_instalacion"></a>
Seguimos los pasos del README oficial para la [descarga](https://github.com/hyperledger/firefly-cli/#download-the-package-for-your-os), y [instalación](https://github.com/hyperledger/firefly-cli/#extract-the-binary-and-move-it-to-usrbinlocal) del cli.

### Crea un stack de __Hyperledger Fabric__<a name="stack_fabric"></a>
El comando ff init crea un nuevo stack y le pide algunos datos, como la cantidad de miembros (organizaciones) que desea en su stack, el nombre de la(s) organización(es) y del(os) nodo(s)-par.
```bash
ff init -b fabric --prompt-names stack-fabric
```
Una vez terminada la ejecución del comando deberia ver una salida similar a esta:

```
initializing new FireFly stack...
number of members: 1
name for org 0: org1
name for node 0: peer1
Stack 'stack-fabric' created!
To start your new stack run:

  ff start stack-fabric
```
 > **NOTA**: Para nuestro ejemplo se creó el stack `stack-fabric` con un solo miembro nombrado `org1` y un nodo nombrado `peer1`.

### Ficheros de configuración<a name="ficheros_config"></a>

> **NOTA**: Los ficheros de configuración de cada stack se encuentran en `~/.firefly/stacks/`


## Usando fabconnect y test-network<a name="fabconnect_testnetwork"></a>

### Descargue fabric-samples, imágenes de docker y los binarios de fabric<a name="fabconnect_testnetwork_descarga_prerequisitos"></a>

```bash
cd $HOME
```
Para obtener el script de instalación:

```bash
curl -sSLO https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/install-fabric.sh && chmod +x install-fabric.sh
```
Ejecutar el script:
```bash
./install-fabric.sh -f 2.4.4 d b s
```
> **NOTA**: Con esos parámetros se obtienen los contenedores `docker de Fabric 2.4.4`, se clona el repositorio `fabric-samples`, y se descargan los `binarios` de Fabric.

[Visitar este enlace para profundizar en las instrucciones de instalación de Fabric ...](https://hyperledger-fabric.readthedocs.io/en/latest/install.html)

### Iniciar la red de prueba `"test-network"`<a name="fabconnect_testnetwork_iniciar_testnet"></a>

```bash
cd fabric-samples/test-network
```
Ejecute el siguiente comando para iniciar la red, crear un canal con el nombre predeterminado de `mychannel` y generar los artefactos criptográficos con Fabric CA:
```bash
./network.sh up createChannel -ca -c mychannel
```

Puede instalar el chaincode `asset-transfer-basic` siguiendo los pasos: [Instalar asset-transfer-basic](https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html#starting-a-chaincode-on-the-channel)

### Descargar la configuración<a name="fabconnect_testnetwork_descarga_fabconnect"></a>

Descarga este repositorio
```bash
git clone https://github.com/kmilodenisglez/fabconnect-testnet.git
```
> **NOTA**: Este repositorio cuenta con un fichero docker-compose que usa la imagen firefly-fabconnect. El docker-compose esta configurado para montar el volumen de los artefactos criptograficos desde `$HOME/fabric-samples/test-network/organizations/`.

```bash
cd fabconnect
```

### Iniciar `fabconect`<a name="fabconnect_testnetwork_iniciar_fabconnect"></a>

```bash
docker-compose up -d
```

Visita la url: http://direccion_ip:3000

### Interactuando con el chaincode `asset-transfer-basic`<a name="fabconnect_testnetwork_interactuando_cc"></a>

#### InitLedger
Invoca el endpoint POST /transactions, con los datos siguientes:
```json
{
  "headers": {
    "type": "SendTransaction",
    "signer": "admin",
    "channel": "mychannel",
    "chaincode": "basic"
  },
  "func": "InitLedger",
  "args": [],
  "init": false
}
```
Como resultado el cliente debe responder con un JSON similar a este:
```json
{
  "headers": {
    "id": "cee5a0b8-e207-49c9-76a2-ca0d106fa139",
    "type": "TransactionSuccess",
    "timeReceived": "2022-07-18T06:00:37.323214822Z",
    "timeElapsed": 2.217562622,
    "requestOffset": "",
    "requestId": ""
  },
  "blockNumber": 6,
  "signerMSP": "Org1MSP",
  "signer": "admin",
  "transactionID": "44574737473df89f0183827f10ede4b5e99563ba44df6b7d48a49763d9179228",
  "status": "VALID"
}
```

#### QueryAllAssets
Invoca el endpoint POST /query, con los datos siguientes:
```json
{
  "headers": {
    "signer": "admin",
    "channel": "mychannel",
    "chaincode": "basic"
  },
  "func": "GetAllAssets",
  "args": [
    "string"
  ],
  "strongread": true
}
```

```json
{
  "headers": {
    "channel": "mychannel",
    "timeReceived": "",
    "timeElapsed": 0,
    "requestOffset": "",
    "requestId": ""
  },
  "result": [
    {
      "AppraisedValue": 300,
      "Color": "blue",
      "ID": "asset1",
      "Owner": "Tomoko",
      "Size": 5
    },
    {
      "AppraisedValue": 400,
      "Color": "red",
      "ID": "asset2",
      "Owner": "Brad",
      "Size": 5
    },
    ...
  ]
}
 ```

## Usando fabconnect y test-network-nano-bash<a name="fabconnect_testnetworknanobas"></a>

En progreso...

## Documentación <a name="doc"></a>

- [FireFly Fabconnect - Github](https://github.com/hyperledger/firefly-fabconnect)
- [FireFly Cli - Github](https://github.com/hyperledger/firefly-cli/)
- [Firefly - Doc](https://hyperledger.github.io/firefly/)