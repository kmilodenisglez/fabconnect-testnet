# firefly-fabconnect

Esta documentación muestra varias formas de desplegar firefly-fabconnect:
- Usar `fabconnect` y la [`test-network de Fabric`](https://github.com/hyperledger/fabric-samples/tree/main/test-network) del repositorio fabric-samples.
- Usar `fabconnect` y [`test-network-nano-bash de Fabric`](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash) del repositorio fabric-samples. __En progreso__
- Usando `FireFly CLI`.

 > **NOTA**: El modo `FireFly CLI` ejecuta algunos supernodos en su máquina. El stack contiene una instancia de firefly-core, firefly-dataexchange-https, ipfs y firefly-sandbox.

## Tabla de contenido

- [Usando fabconnect y test-network](#fabconnect_testnetwork)
  * [Descargue fabric-samples, imágenes de docker y los binarios de fabric](#fabconnect_testnetwork_descarga_prerequisitos)
  * [Iniciar la red de prueba `"test-network"`](#fabconnect_testnetwork_iniciar_testnet)
  * [Descargar la configuración](#fabconnect_testnetwork_descarga_fabconnect)
  * [Iniciar `fabconect`](#fabconnect_testnetwork_iniciar_fabconnect)
- [Usando fabconnect y test-network-nano-bash](#fabconnect_testnetworknanobash)
- [Usando firefly-cli](#fireflycli)
  * [Descarga e instala el __CLI__](#descarga_instalacion)
  * [Crea un stack de __Hyperledger Fabric__](#stack_fabric)
  * [Ficheros de configuración](#ficheros_config)
- [Interactuando con el chaincode `asset-transfer-basic`](#fabconnect_testnetwork_interactuando_cc)
  * [En el modo `fabconnect` y `test-network` (incluyendo `test-network-nano-bash`)](#fabconnect_testnetwork_interactuando_cc_modo1)
  * [En el modo `firefly-cli`](#fabconnect_testnetwork_interactuando_cc_modo2)
- [Documentación](#doc)

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

### Descargar la configuración<a name="fabconnect_testnetwork_descarga_fabconnect"></a>

Descarga este repositorio
```bash
git clone https://github.com/kmilodenisglez/fabconnect-testnet.git
```
> **NOTA**: Este repositorio cuenta con un fichero docker-compose que inicia un contenedor de firefly-fabconnect. Esta configurado para montar el volumen de los artefactos criptograficos desde `$HOME/fabric-samples/test-network/organizations/`.

```bash
cd fabconnect
```

### Iniciar `fabconect`<a name="fabconnect_testnetwork_iniciar_fabconnect"></a>

```bash
docker-compose up -d
```

Visita la url: http://direccion_ip:3000/api

## Usando fabconnect y test-network-nano-bash<a name="fabconnect_testnetworknanobas"></a>

En progreso...

## Usando firefly-cli<a name="fireflycli"></a>

### Descarga e instalación del __CLI__<a name="descarga_instalacion"></a>
Seguimos los pasos del README oficial para la [descarga](https://github.com/hyperledger/firefly-cli/#download-the-package-for-your-os), y [instalación](https://github.com/hyperledger/firefly-cli/#extract-the-binary-and-move-it-to-usrbinlocal) del cli.

### Crea un stack de __Hyperledger Fabric__<a name="stack_fabric"></a>
El comando `ff init` crea un nuevo stack y le pide algunos datos, como la cantidad de miembros (organizaciones) que desea en su stack, el nombre de la(s) organización(es) y del(os) nodo(s)-par.
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
 > **NOTA**: Para este ejemplo se creó el stack `stack-fabric` con un solo miembro al que se denominó `"org1"` y un nodo nombrado `"peer1"`.

### Ficheros de configuración<a name="ficheros_config"></a>

> **NOTA**: Los ficheros de configuración de cada stack se encuentran en `~/.firefly/stacks/`

## Interactuando con el chaincode `asset-transfer-basic`<a name="fabconnect_testnetwork_interactuando_cc"></a>

### En el modo `fabconnect` y `test-network` (incluyendo `test-network-nano-bash`)<a name="fabconnect_testnetwork_interactuando_cc_modo1"></a>

- Si opera con la `test-network` puede instalar el chaincode `asset-transfer-basic` siguiendo los pasos: [Instalar asset-transfer-basic en test-network](https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html#starting-a-chaincode-on-the-channel)

- Si  opera con la `test-network-nano-bash` puede instalar el chaincode `asset-transfer-basic` siguiendo los pasos: [Instalar asset-transfer-basic en test-network-nano-bash](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash#instructions-for-deploying-and-running-the-basic-asset-transfer-sample-chaincode)

> **NOTA**: Para interactuar con los endpoint de fabconnect puede acceder en su navegador al enlace http://__direccion_ip__:3000/api.

### InitLedger
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

### QueryAllAssets
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
Como resultado el cliente debe responder con un JSON similar a este:
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

### En el modo `firefly-cli` <a name="fabconnect_testnetwork_interactuando_cc_modo2"></a>
Si usa `firefly-cli` puede instalar e interactuar con el chaincode `asset-transfer-basic` siguiendo los pasos: [Trabajando el asset-transfer-basic con firefly-cli](https://hyperledger.github.io/firefly/tutorials/custom_contracts/fabric.html#work-with-hyperledger-fabric-chaincodes)


## Documentación <a name="doc"></a>

- [FireFly Fabconnect - Github](https://github.com/hyperledger/firefly-fabconnect)
- [FireFly Cli - Github](https://github.com/hyperledger/firefly-cli/)
- [Firefly - Doc](https://hyperledger.github.io/firefly/)