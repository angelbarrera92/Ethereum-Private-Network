# Red privada Ethereum en Amazon (AWS)

INTRODUCCION VA AQUI

## Preparación

### Creación de Security Group

### Inicialización de un nodo. Amazon Linux AMI
#### Instalación de geth
Desafortunadamente no hay una distrubución de ```geth``` lista para instalar desde los repositorios de software de la AMI, por lo que debemos instalar ```geth``` desde el código fuente:
```bash
sudo yum update
sudo yum install golang -y
git clone https://github.com/ethereum/go-ethereum.git
cd go-ethereum/
sudo make all
```
Los binarios compilados se encuentran en la siguiente ruta: ```~/go-ethereum/build/bin``` por lo que podemos añadir esta ruta al Path para que podamos usarlo de una forma mas comoda.
```bash
export PATH=$PATH:/home/ec2-user/go-ethereum/build/bin
```
Ùna vez hecho esto podemos comprobar la instalación escribiendo:
```bash
$ geth version
Geth
Version: 1.6.7-unstable
Git Commit: 65f0e905ddda9be97368839b67be5754b33a0eea
Architecture: amd64
Protocol Versions: [63 62]
Network Id: 1
Go Version: go1.7.5
Operating System: linux
GOPATH=
GOROOT=/usr/lib/golang
```

#### Inicialización de la red. Genesis
Para inicializar la red vamos a clonarnos este repositorio en la maquina donde acabamos de instalar ```geth```
```bash
cd
git clone https://github.com/angelbarrera92/Ethereum-Private-Network.git
cd Ethereum-Private-Network
cd scripts
./init_the_genesis.sh
```
Debe de mostrar unas trazas similares a: 
```bash
./init_the_genesis.sh
WARN [07-08|16:22:35] No etherbase set and no accounts found as default 
INFO [07-08|16:22:35] Allocated cache and file handles         database=/home/ec2-user/data/ethPrivate/geth/chaindata cache=16 handles=16
INFO [07-08|16:22:35] Writing custom genesis block 
INFO [07-08|16:22:35] Successfully wrote genesis state         database=chaindata                                     hash=b38dfb…566e58
INFO [07-08|16:22:35] Allocated cache and file handles         database=/home/ec2-user/data/ethPrivate/geth/lightchaindata cache=16 handles=16
INFO [07-08|16:22:35] Writing custom genesis block 
INFO [07-08|16:22:35] Successfully wrote genesis state         database=lightchaindata                                     hash=b38dfb…566e58
```
#### Inicialización del nodo
Estando en el directorio de scripts de este repositorio podemos ejecutar:
```bash
./node_init.sh
WARN [07-08|16:32:25] No etherbase set and no accounts found as default 
INFO [07-08|16:32:25] Starting peer-to-peer node               instance=Geth/v1.6.7-unstable-65f0e905/linux-amd64/go1.7.5
INFO [07-08|16:32:25] Allocated cache and file handles         database=/home/ec2-user/data/ethPrivate/geth/chaindata cache=128 handles=1024
WARN [07-08|16:32:25] Upgrading chain database to use sequential keys 
INFO [07-08|16:32:25] Initialised chain configuration          config="{ChainID: 154321 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Metropolis: <nil> Engine: unknown}"
INFO [07-08|16:32:25] Disk storage enabled for ethash caches   dir=/home/ec2-user/data/ethPrivate/geth/ethash count=3
INFO [07-08|16:32:25] Disk storage enabled for ethash DAGs     dir=/home/ec2-user/.ethash                     count=2
WARN [07-08|16:32:25] Upgrading db log bloom bins 
INFO [07-08|16:32:25] Bloom-bin upgrade completed              elapsed=82.023µs
INFO [07-08|16:32:25] Initialising Ethereum protocol           versions="[63 62]" network=154321
INFO [07-08|16:32:25] Loaded most recent local header          number=0 hash=b38dfb…566e58 td=200
INFO [07-08|16:32:25] Loaded most recent local full block      number=0 hash=b38dfb…566e58 td=200
INFO [07-08|16:32:25] Loaded most recent local fast block      number=0 hash=b38dfb…566e58 td=200
INFO [07-08|16:32:25] Starting P2P networking 
INFO [07-08|16:32:25] Database conversion successful 
INFO [07-08|16:32:27] UDP listener up                          self=enode://b0cb65c92e1e3e06001e6ca70acc73e09104bac5915541b980983ae46d96bfd24ef94ff61e54b8d3aa10781deff93de36a73b6b2c3d9a030ba3d8126cbec5014@[::]:30303
INFO [07-08|16:32:27] RLPx listener up                         self=enode://b0cb65c92e1e3e06001e6ca70acc73e09104bac5915541b980983ae46d96bfd24ef94ff61e54b8d3aa10781deff93de36a73b6b2c3d9a030ba3d8126cbec5014@[::]:30303
INFO [07-08|16:32:27] IPC endpoint opened: /home/ec2-user/data/ethPrivate/geth.ipc 
Welcome to the Geth JavaScript console!

instance: Geth/v1.6.7-unstable-65f0e905/linux-amd64/go1.7.5
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> 
```
Àhora estaremos dentro de la consola de este nodo de Ethereum. 

#### Creación de una cuenta
Estando dentro de la consola, podemos ejecutar:
```bash
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0x499689c2238cd94b7ce4b19c09d7e11071824795"
> INFO [07-08|16:35:08] New wallet appeared                      url=keystore:///home/ec2-user/data/… status=Locked
```
Ỳ ya tendremos nuestra cuenta creada. Esta la usaremos para configurarla como destino de los Ether minados en esta red privada (Se configura automagicamente). Para comprobar el id de la cuenta destino del minado: 
```
eth.coinbase
```
#### Obtención del identificador del nodo.
Para poder crear un cluster, debemos conocer el identificador de nuestro nodo. Para ello, siguiente en la consola de Ethereum tenemos que escribir el siguiente comando: 
```bash
> admin.nodeInfo
{
  enode: "enode://b0cb65c92e1e3e06001e6ca70acc73e09104bac5915541b980983ae46d96bfd24ef94ff61e54b8d3aa10781deff93de36a73b6b2c3d9a030ba3d8126cbec5014@[::]:30303",
  id: "b0cb65c92e1e3e06001e6ca70acc73e09104bac5915541b980983ae46d96bfd24ef94ff61e54b8d3aa10781deff93de36a73b6b2c3d9a030ba3d8126cbec5014",
  ip: "::",
  listenAddr: "[::]:30303",
  name: "Geth/v1.6.7-unstable-65f0e905/linux-amd64/go1.7.5",
  ports: {
    discovery: 30303,
    listener: 30303
  },
  protocols: {
    eth: {
      difficulty: 200,
      genesis: "0xb38dfbafcacd3c8a6324969358b95d54cc36d7d87362045375b83d83d5566e58",
      head: "0xb38dfbafcacd3c8a6324969358b95d54cc36d7d87362045375b83d83d5566e58",
      network: 154321
    }
  }
}
```
Podemos observar varios datos: 
1. El identificador de ```protocols.eth.genesis``` es el mismo que ```
protocols.eth.head``` esto es de bido a que nuestra cadena solo tiene un bloque, el bloque relativo a nuestra genesis. 
2. El ```protocols.eth.network``` indica nuestro network id. 
3. El puerto ```ports.listener``` es el que hemos abierto anteriormente en la sección de security groups. 

El dato que estabamos buscando es el valor de ```enode```. A este valor se le debe sustituir ```[::]``` por la ip de nuestra instancia. 
Para descubir la  ip publica de esta instancia (debemos abrir otra terminal para no cerrar geth)
```bash
curl http://169.254.169.254/latest/meta-data/public-ipv4 && echo
54.194.91.164
```
Entonces nuestro enode será: ```enode://b0cb65c92e1e3e06001e6ca70acc73e09104bac5915541b980983ae46d96bfd24ef94ff61e54b8d3aa10781deff93de36a73b6b2c3d9a030ba3d8126cbec5014@54.194.91.164:30303```