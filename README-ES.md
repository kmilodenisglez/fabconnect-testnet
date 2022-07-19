# firefly-fabconnect

Esta documentación muestra varias formas de desplegar firefly-fabconnect:
- Usar `fabconnect` y la [`test-network de Fabric`](https://github.com/hyperledger/fabric-samples/tree/main/test-network) del repositorio fabric-samples.
  > Este modo ejecuta una instancia de fabconnect usando un contenedor de docker gestionado por docker-compose.

- Usar `fabconnect` y [`test-network-nano-bash de Fabric`](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash) del repositorio fabric-samples.
  > Este modo ejecuta una instancia de fabconnect compilada a partir  del código fuente.
- Usando `FireFly CLI`.

  > El modo `FireFly CLI` ejecuta algunos supernodos en su máquina. El stack contiene una instancia de firefly-core, firefly-dataexchange-https, ipfs y firefly-sandbox.

👀 For English speaking visit [README-EN](./README.md).

## Tabla de contenido

- [Usando fabconnect y la `test-network` de Fabric](#fabconnect_testnetwork)
  * [Descargue fabric-samples, imágenes de docker y los binarios de fabric](#fabconnect_testnetwork_download_prerequisites)
  * [Iniciar la red de prueba `"test-network"`](#fabconnect_testnetwork_bringup_testnetwork)
  * [Descargar la configuración](#fabconnect_testnetwork_download_fabconnect)
  * [Iniciar `fabconect`](#fabconnect_testnetwork_bringup_fabconnect)
- [Usando fabconnect y la `test-network-nano-bash` de Fabric](#fabconnect_testnetworknanobash)
  * [Iniciar la red de prueba con `"test-network-nano-bash"`](#fabconnect_testnetwork_iniciar_testnetworknanobash)
  * [Descarga y compilación del `fabconnect`](#download_fabconnect_build)
  * [Descargar la configuración](#fabconnect_testnetwork_download_fabconnect2)
  * [Modificar los ficheros cpp y fabconnect para `test-network-nano-bash`](#fabconnect_testnetwork_modify_cpp)  
  * [Iniciar el conector](#fabconnect_testnetwork_iniciar_connector)
- [Usando firefly-cli](#fireflycli)
  * [Descarga e instala el `CLI`](#descarga_instalacion)
  * [Crea un stack de `Hyperledger Fabric`](#stack_fabric)
  * [Ficheros de configuración](#ficheros_config)
- [Interactuando con el chaincode `asset-transfer-basic`](#fabconnect_testnetwork_interactuando_cc)
  * [En el modo `fabconnect` con la `test-network` o `test-network-nano-bash` de Fabric](#fabconnect_testnetwork_interactuando_cc_modo1)
  * [En el modo `firefly-cli`](#fabconnect_testnetwork_interactuando_cc_modo2)
- [Documentación](#doc)
- [Solución de problemas](#troubleshooting)

## Usando fabconnect y la `test-network` de Fabric <a name="fabconnect_testnetwork"></a>
Este modo ejecuta una instancia de fabconnect usando un contenedor de docker gestionado por docker-compose.

> **NOTA**: Este modo a sido solo probado en Ubuntu 20.04.

### Descargue fabric-samples, imágenes de docker y los binarios de fabric<a name="fabconnect_testnetwork_download_prerequisites"></a>

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

### Iniciar la red de prueba `"test-network"`<a name="fabconnect_testnetwork_bringup_testnetwork"></a>

```bash
cd fabric-samples/test-network
```
Ejecute el siguiente comando para iniciar la red, crear un canal con el nombre predeterminado de `mychannel` y generar los artefactos criptográficos con Fabric CA:
```bash
./network.sh up createChannel -ca -c mychannel
```

### Descargar la configuración<a name="fabconnect_testnetwork_download_fabconnect"></a>

Descarga este repositorio
```bash
git clone https://github.com/kmilodenisglez/fabconnect-testnet.git
```
> **NOTA**: Este repositorio cuenta con un fichero docker-compose que inicia un contenedor de firefly-fabconnect. Esta configurado para montar el volumen de los artefactos criptograficos desde `$HOME/fabric-samples/test-network/organizations/`.

```bash
cd fabconnect
```

### Iniciar `fabconect`<a name="fabconnect_testnetwork_bringup_fabconnect"></a>

```bash
docker-compose up -d
```

Visita la url: http://direccion_ip:3000/api

## Usando fabconnect y la `test-network-nano-bash` de Fabric <a name="fabconnect_testnetworknanobash"></a>
En este modo se emplea una instancia del fabconnect que construimos a partir del código fuente.
> **NOTA**: Este modo a sido probado en `Ubuntu` y en `Windows 10 con WSL`. Se inicio la red con la configuracion minima, con dos orderer nodos (**./orderer1.sh** y **./orderer2.sh**) y un único nodo par de Org1 (**./peer1.sh**).

### Iniciar la red de prueba con `"test-network-nano-bash"`<a name="fabconnect_testnetwork_iniciar_testnetworknanobash"></a>

👉🏾 [Siga las instrucciones para iniciar la red e instalar el chaincode.](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash#test-network---nano-bash)

### Descarga y compilación del __fabconnect__<a name="download_fabconnect_build"></a>
Una vez iniciada la red blockchain usando el repositorio `test-network-nano-bash` se continúa con la descarga y compilación del fabconnect.

```bash
cd $HOME
```

Descargar el repositorio de fabconnect
```bash
git clone https://github.com/hyperledger/firefly-fabconnect.git
```

### Compilar __fabconnect__
```bash
go mod vendor
go build -o fabconnect
```

```bash
cd $HOME
```

### Descargar la configuración<a name="fabconnect_testnetwork_download_fabconnect2"></a>

Descarga este repositorio
```bash
git clone https://github.com/kmilodenisglez/fabconnect-testnet.git
```

### Modificar los ficheros cpp y fabconnect para __test-network-nano-bash__ <a name="fabconnect_testnetwork_modify_cpp"></a>

Modifique en el archivo CCP de la test-network-nano-bash nombrado `cpp_nanobash.yaml` el camino a los artefactos. Donde encuentres `'/home/my_user/fabric-samples'` lo remplazas por el camino a la carpeta `fabric-samples`.

Modifique en el archivo `fabconnect-testnet/runtime/blockchain/fabconnect_nanobash.yaml` las rutas. Dondequiera que encuentre `'/home/my_user/fabconnect-testnet', reemplácelo con la ruta a su `fabconnect-testnet`.

###  Iniciar el conector <a name="fabconnect_testnetwork_iniciar_connector"></a>
Antes de iniciar el fabconnect debemos configurar la identidad (signer) que se va emplear para establecer conexión con la red blockchain. El `test-network-nano-bash` no levanta nodos de Fabric-CA, por lo que se tiene que usar la identidad `admin` generada por el script `generate_artifacts.sh`.

Abrimos en un explorador de fichero el camino donde se almacenan las credenciales. El camino está definido en el archivo CCP `"cpp_nanobash.yaml"`, en la sección `cliente.credentialStore`.

Para este ejemplo el `fabric-samples/test-network-nano-bash` se encuentra en el home del usuario.

```bash
cd ~/fabric-samples/test-network-nano-bash/crypto-config/peerOrganizations/org1.example.com/users/
```

Copiamos el `Admin@org1.example.com-cert.pem` para el directorio raiz del `/users` con el formato siguiente `user + @ + MSPID + "-cert.pem"` :
```bash
cp Admin@org1.example.com/msp/signcerts/Admin@org1.example.com-cert.pem ./admin@Org1MSP-cert.pem
```
> **NOTA**: Ese es el formato que usa el fabric-sdk-go para almacenar un usuario.

Copiamos la llave privada `priv_sk` para el directorio  `/users/keystore/`:
```bash
cp Admin@org1.example.com/msp/keystore/priv_sk ./keystore/
```

Utilice el siguiente comando para iniciar el conector:
```bash
./fabconnect -f "/home/my_user/fabconnect-testnet/runtime/blockchain/fabconnect_nanobash.yaml"
```

Si se ejecuta bien, la terminal debe mostrar la siguiente salida:

```bash
[2022-07-18T21:41:57.125-04:00]  INFO Starting REST gateway
[2022-07-18T21:41:57.376-04:00]  INFO HTTP server listening on 0.0.0.0:3000
```

## Usando firefly-cli<a name="fireflycli"></a>

### Descarga e instalación del __CLI__<a name="descarga_instalacion"></a>
Sigue los pasos del README oficial para la [descarga](https://github.com/hyperledger/firefly-cli/#download-the-package-for-your-os), y [instalación](https://github.com/hyperledger/firefly-cli/#extract-the-binary-and-move-it-to-usrbinlocal) del cli.

### Crea un stack de __Hyperledger Fabric__<a name="stack_fabric"></a>
El comando `ff init` crea un nuevo stack y le pide algunos datos, como la cantidad de miembros (organizaciones) que desea en su stack, el nombre de la(s) organización(es) y del(os) nodo(s)-par.
```bash
ff init -b fabric --prompt-names stack-fabric
```
Una vez terminada la ejecución del comando debería ver una salida similar a esta:

```
initializing new FireFly stack...
number of members: 1
name for org 0: org1
name for node 0: peer1
Stack 'stack-fabric' created!
To start your new stack run:

  ff start stack-fabric
```
 > **NOTA**: Para este ejemplo se creó el stack `stack-fabric` con un único miembro que nombrado `"org1"` y un nodo nombrado `"peer1"`.

### Ficheros de configuración<a name="ficheros_config"></a>

> **NOTA**: Los ficheros de configuración de cada stack se encuentran en `~/.firefly/stacks/`

## Interactuando con el chaincode `asset-transfer-basic`<a name="fabconnect_testnetwork_interactuando_cc"></a>

### En el modo `fabconnect` con la `test-network` o `test-network-nano-bash` de Fabric<a name="fabconnect_testnetwork_interactuando_cc_modo1"></a>

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

## Solución de problemas <a name="troubleshooting"></a>
Si el fabconnect devolvió algún error de tipo `TRANSIENT_FAILURE`, una posible razón es que debe agregar al hosts los nombres de dominios "peer0.org1.example.com", "orderer.example.com", y "org1.example.com".

Ejecutar la instrucción siguiente:
```bash
echo '127.0.0.1 peer0.org1.example.com orderer.example.com org1.example.com' | sudo tee -a /etc/hosts
```