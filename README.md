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
