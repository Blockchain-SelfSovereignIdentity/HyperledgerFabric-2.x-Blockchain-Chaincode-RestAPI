# Fabric 2.x - Primeiro Chaincode
```Configuração e pré-requisitos do Fabric 2.x```
* Recomendado: Ubuntu 20.04
    * Usado: Ubuntu 22.04.1 LTS
* Certificar-se do bash estar acessível.
    ```shell
    matheuslazaro@matheuslazaro:~/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI$ which bash
    /usr/bin/bash
    ```

# Utilitários básicos
### Git, Curl e jq
Git é um sistema de controle de versão que permite gerenciar o código-fonte de um projeto, registrar as mudanças feitas e colaborar com outros desenvolvedores.

Curl é um comando que permite fazer requisições para uma URL usando diversos protocolos, como HTTP e HTTPS. O Curl é útil para configurar o Fabric 2.x, pois ele permite testar a comunicação com a API do framework e verificar se os serviços estão funcionando corretamente.

Jq é uma aplicação que permite formatar e manipular dados em JSON, que é um formato comum para troca de informações entre sistemas. O Jq é útil para utilizar o Fabric 2.x, pois ele permite visualizar e filtrar as respostas da API do framework, que são retornadas em JSON.

```shell
sudo apt-get install git curl jq
```

* Verificação:
    ```shell
    git -v
    git version 2.43.0

    curl --version
    curl 7.81.0 (x86_64-pc-linux-gnu) ...
    Release-Date: 2022-01-05
    ...

    jq --help
    jq - commandline JSON processor [version 1.6]
    ```
### Docker (19.03.12 - compatível com Fabric 2.x (Inclusive 2.5.x))
Docker é uma plataforma de código aberto que permite automatizar o desenvolvimento, implantação e execução de aplicativos em contêineres. Um contêiner é uma unidade padrão de software que empacota o código e todas as suas dependências para que o aplicativo seja executado de forma rápida e confiável de um ambiente de computação para outro. O Docker simplifica o fluxo de gerenciamento de processos de aplicações em contêineres, permitindo que os desenvolvedores dividam um ambiente de aplicação em vários serviços isolados.

* Remover versão antiga do Docker:
```shell
sudo apt-get remove docker
sudo apt-get remove docker-ce
sudo apt-get remove docker-ce-cli
sudo apt-get purge docker.io
sudo apt-get autoremove --purge docker.io
sudo rm -rf /var/lib/docker
sudo groupdel docker
sudo rm -rf /etc/docker
sudo rm /usr/bin/docker
sudo rm /snap/bin/docker
sudo rm /usr/bin/dockerd
sudo rm /etc/apt/sources.list.d/docker.list


```
* Instalar:
https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/
ou
https://download.docker.com/linux/ubuntu/dists/jammy/pool/stable/amd64/

```shell
sudo apt-get install ./docker-ce-cli_19.03.12~3-0~ubuntu-bionic_amd64.deb
sudo apt-get install ./docker-ce_19.03.8~12-0~ubuntu-bionic_amd64.deb
sudo apt-get install containerd
sudo systemctl start docker
sudo systemctl enable docker
```
* Modificar o usuário
```shell
echo $USER
sudo groupadd docker
ewgrp docker # logar no grupo docker
sudo chmod 666 /var/run/docker.sock
sudo usermod -a -G docker $USER
```

* Verificação (Se necessário, reiniciar a VM): 
    ```shell
    docker --version
    Docker version 19.03.12, build 48a66213fe
    docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    ```
* Matar tudo no Docker:
```shell
docker kill $(docker ps -q)
```



### Docker-compose (Versão 1.29.2 - Compatível com Fabric 2.x)
Docker-compose é uma ferramenta para definir e executar aplicativos multi-contêiner com o Docker. Ele usa as definições de serviço para compilar ambientes totalmente personalizados com contêineres múltiplos que podem compartilhar redes e volumes de dados. O Docker-compose permite construir e executar vários contêineres no mesmo host, bastando apenas um arquivo YAML com as instruções e parâmetros desejados para os nossos contêineres e que com um único comando conseguimos realizar a execução e/ou atualização de todos eles.

* Remover versão antiga:
```shell
sudo apt-get remove docker-compose
sudo rm /usr/local/bin/docker-compose
```

* Istalar (docker-compose version 1.29.2 - Compatível com Fabric 2.x (Inclusive 2.5.x)):
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
export PATH=$PATH:/usr/local/bin
export PATH=$PATH:/snap/bin

# Atualizar biblioteca paramiko
pip install --upgrade paramiko

```

* Verificação:
    ```shell
    docker-compose -v
    docker-compose version 1.29.2, build 5becea4c
    ```



Obs.: O Fabric usa por padrão a linguagem GO, entretanto, por escolha própria, usaremos a linguagem Javascript, com Node.js.

### Node.js 12.18.2 e npm 6.14.5(Versão suportada)
* Instalação
```shell
sudo apt-get remove nodejs

sudo apt-get install npm
sudo npm install -g n
sudo rm /usr/bin/node
sudo n 12.18.2
sudo ln -s /usr/local/bin/node /usr/bin/node
```

* Verificação:
    ```shell
   node -v
    v12.18.2

    npm --version
    6.14.5
    ```

# A arquitetura
Uma vez que temos um ambiente de desenvolvimento configurado, podemos seguir e baixar os binários do Fabric, para conseguirmos executar os Peers, os Orderers, e todos os componentes que são necessários.

Desta forma, na arquitetura a ser criada, vamos ter um lado de Chaincode. Então tudo isso vai rodar em cima de uma Blockchain. Também vamos ter vários Peers ou várias Orgs que estará funcionnando no nosso Contrato Inteligente ou Chaincode. Eles são usados de forma intercambiável no Chaincode do ecossistema maior, nosso Contrato Inteligente, são a mesma coisa.

Também teremos uma Aplicação cliente básica, que usará as bibliotecas do Node (Node-SDK) para se conectar com a Blockchain.

Então vamos ter as partes da Blockchain, que vão ser duas Orgs e um Orderer, e vamos ter uma Aplicação cliente que vai se conectar com a Blockchain usando as APIs do Node.

<img src="https://raw.githubusercontent.com/Blockchain-SelfSovereignIdentity/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI/main/3-Fabric2.x-PrimeiroChaincode/arquitetura.png" alt="Arquitetura">

Podemos ter a arquitetura básica do Orderer e vários Peers. Como exemplo, vamos ter dois Peers, Org 1 e Org 2. Vamos ter um canal único e vamos criar blocos nesse canal usando as APIs que nos são fornecidas.

<img src="https://raw.githubusercontent.com/Blockchain-SelfSovereignIdentity/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI/main/3-Fabric2.x-PrimeiroChaincode/diagramaTransacoesAplicacao.png" alt="Diagrama de Transacoes da Aplicação">

# Obtendo binários da versão de testes do Fabric (Incluindo Chaincode de amostra)
### Criando uma pasta no diretório pessoal
```Siga todos os comandos à risca, para evitar problemas```

* Criar um diretório chamado "Fabric"
```shell
mkdir ~/fabric
```

* Entrar na pasta "Fabric"
```shell
cd ~/fabric
```

### Importando os binários da versão de testes do Fabric (É necessário instalar exatamente a versão 2.x (Inclusive 2.5.x), devido à compatibilidade com os outros componentes)
Mais atualizada:
```shell
curl -sSL https://bit.ly/2ysbOFE | bash -s
```
versão mais recente: 2.5.5 (TESTADA E COMPATÍVEL)


* Versão específica, EXEMPLO:
```shell
curl -sSL https://bit.ly/2ysbOFE | bash -s 2.3.0
```
### Configurar a rede de testes
* Abrir o diretório de testes
```shell
cd fabric-samples/test-network
```
* Matar a rede: 
```shell
./network.sh down

```
* Subir a rede, Criar o canal, e configurar com uma Autoridade de Certificação
```shell
./network.sh up createChannel -c channel1 -ca
```
* matar conteiners:
```shell
docker kill $(docker ps -q)
```
## Implantando o Chaincode em Javascript (Nodejs)
* Pasta de Implementações da Blockchain
```shell
cd ../asset-transfer-basic
```
* Pasta chaincode-javascript
```shell
cd chaincode-javascript
```
* Instalar as dependências necessárias:
```shell
npm install
```

* Em caso de vulnerabilidades encontradas, executar:
    ```shell
    npm audit fix
    ```

* Exportar variáveis de ambiente
```shell
cd ~/fabric/fabric-samples/test-network

export FABRIC_CFG_PATH=$PWD/../config/ 
echo $FABRIC_CFG_PATH 
/home/matheuslazaro/fabric/fabric-samples/test-network/../config/

export PATH=$PATH:/home/$USER/fabric/fabric-samples/bin/ 
```
* Criar um pacote a partir do nosso código-fonte chaincode

```Queremos criar um pacote e o nome do pacote será basic.tar.gz, direcionado para a pasta "../asset-transfer-basic/chaincode-javascript", linguagem node e com rótulo de pacote basic_1.0```
```shell
peer lifecycle chaincode package basic.tar.gz \
    --path ../asset-transfer-basic/chaincode-javascript \
    --lang node \
    --label basic_1.0

matheuslazaro@matheuslazaro:~/fabric/fabric-samples/test-network$ ls
addOrg3       bft-config                          channel-artifacts  configtx  monitordocker.sh  network.sh     prometheus-grafana  scripts       system-genesis-block
basic.tar.gz  CHAINCODE_AS_A_SERVICE_TUTORIAL.md  compose            log.txt   network.config    organizations  README.md           setOrgEnv.sh
```
Com esse pacote "basic.tar.gz" criado na pasta test-network, podemos aplicar no nosso canal para que ele possa ser executado pelos diferentes Peers

# Entendendo e publicando o Contrato Inteligente
* Abrir a pasta
```shell
cd /home/matheuslazaro/fabric/fabric-samples/asset-transfer-basic/chaincode-javascript/lib
```
* Abrir o arquivo assetTransfer.js
    * Este é o arquivo principal que vai ser executado
    * Vamos chamar algumas funções deste código para interagir com o nosso Chaincode.
    * Algumas dessas funções está em um Ledger.
    * Este código, em um Ledger essencialmente cria uma coleção de ativos.
        * A ideia do primeiro ativo (asset1) tem uma cor, tamanho, dono e valor avaliado
        * São 6 ativos
        * Há um contexto definido sobre os ativos:
        ```js
         for (const asset of assets) {
            asset.docType = 'asset';
            await ctx.stub.putState(asset.ID, Buffer.from(stringify(sortKeysRecursive(asset))));
            console.info(`Asset ${asset.ID} inicialized`);
        }
        ```
        * Esse contexto nos permite interagir com o código
        * Essa é o Chaincode de toda a nossa arquitetura.
        * Uma vez que temos essa configuração, uma vez que ele está sendo executado em cima do nosso Ledger, vamos escrever um aplicativo que vai chamar essas funções por meio do Hyperledger Fabric.
            * Tudo é escrito com Javascript básico.
            * Por enquanto, a função mais importante é:
            ```js
            return ctx.stub.putState(id, Buffer.from(stringify(sortKeysRecursive(updatedAsset))));
            ```
        * Uma vez entendido, podemos retornar e colocar este chaincode em nosso Ledger para que possa ser executado em cima de um canal.
### No terminal
* Abrir a pasta:
```shell
cd /home/matheuslazaro/fabric/fabric-samples/test-network
```
* Vamos instalar o pacote "basic.tar.gz" nas duas Orgs e Ledger.
* Essencialmente vamos ter um canal e para esse canal vamos ter duas Orgs diferentes, Org1 e Org2, executando em um Ledger distribuído. Ambas as Orgs vão usar esse Chaincode. Temos que implantá-lo para ambas as Orgs.

#### Definir as variáveis de ambiente necessárias por meio do script fornecido.
```shell
source ./scripts/envVar.sh
```
* Este script define algumas variáveis de ambiente que apontam para alguns certificados
* Possui uma função que é definida globalmente e o conjunto global também essencialmente define algumas variáveis de ambiente que serão utilizadas.
#### Execução para Org 1
```shell
setGlobals 1
```
* Definimos a variávelde ambiente para que qualquer comando que executamos agore seja executado na Org 1.
* Em produção, é possível ter Org 1 em uma Máquina e Org 2 em outra máquina, mas por motivos de aprendizado, temos as duas Orgs na mesma máquina. Então, precisamos selecionar em qual Org estamos atuando.
* Com a Org 1 selecionada, vamos instalar esse Chaincode que criamos.
* O basic.tar.gz, que é um código básico Chaincode, vai ser instalado em no canal da nossa Org 1.

* Precisamos, antes disso, definir algumas variáveis de ambiente.
* Vamos salvar os locais dos arquivos de certificado para as organizações e para o Orderer.
```shell
export ORDERER_CERTFILE=$PWD/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

export ORG1_PEER_CERTFILE=$PWD/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export ORG2_PEER_CERTFILE=$PWD/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
```

* Vamos instalar o Chaincode usando o pacote basic.tar.gz:
    * Então, vamos ter o Chaincode do ciclo de vida Peer e agora vamos tentar instalar o Chaincode. 
    * Pode levar bastante tempo para carregar, visto que tem que haver muita comunicação, entre os Peers e a informação também está sendo transmitida em Broadcast.
```shell
matheuslazaro@matheuslazaro:~/fabric/fabric-samples/test-network$ peer lifecycle chaincode install basic.tar.gz 

2024-01-08 15:49:45.504 -03 0001 INFO [cli.lifecycle.chaincode] submitInstallProposal -> Installed remotely: response:<status:200 payload:"\nJbasic_1.0:80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345\022\tbasic_1.0" > 
2024-01-08 15:49:45.506 -03 0002 INFO [cli.lifecycle.chaincode] submitInstallProposal -> Chaincode code package identifier: basic_1.0:80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345
```
* Note que a execução retornou um hash. Ele será usado para identificar e refernciar o Chaincode posteriormente:
    ```basic_1.0:80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345```

#### Execução para Org 2:
* Vamos fazer o mesmo para a Org 2:
```shell
setGlobals 2

matheuslazaro@matheuslazaro:~/fabric/fabric-samples/test-network$ peer lifecycle chaincode install basic.tar.gz 

2024-01-08 19:01:51.769 -03 0001 INFO [cli.lifecycle.chaincode] submitInstallProposal -> Installed remotely: response:<status:200 payload:"\nJbasic_1.0:80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345\022\tbasic_1.0" > 
2024-01-08 19:01:51.769 -03 0002 INFO [cli.lifecycle.chaincode] submitInstallProposal -> Chaincode code pack

```
* Isto irá instalar o Chaincode na nossa Org 2
* Feito isso, podemos voltar para a Org 1:
```shell
setGlobals 1
```

#### Definir as variáveis de ambiente para este pacote :
```shell
export PKGID=basic_1.0:80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345
```

#### Aprovar o Chaincode para execução em nossas Orgs
* Há 2 coisas são são feitas: instalação do Chaincode e Aprová-lo para a nossa própria Org.

```shell
peer lifecycle chaincode approveformyorg \
      -o localhost:7050 \
      --ordererTLSHostnameOverride  orderer.example.com \
      --tls --cafile $ORDERER_CERTFILE  \
      --channelID channel1 \
      --name basic \
      --version 1 \
      --package-id $PKGID \
      --sequence 1  
```
* Com este comando, estamos pedindo para o Peer executar o comando do ciclo de vida do Chaincode e o subcomando é aprovado para a Org
* Ele indica o localhost:7050, que é padrão para a Orderer que configuramos em nossa rede de testes.
* Também temos que substituir o nome do host orderer.example.com
* Usaremos TLS e indicamos o arquivo indicado na variável de ambiente ORDERER_CERTFILE, que definimos anteriormente.
* Temos que identificar qual será o canal (nome do canal), neste caso, o nome que foi especificado em:
```shell
./network.sh up createChannel -c channel1 -ca
```
* Neste caso, é channel1
* O nome do pacote será basic
* A versão é 1
* O pacote é definido pela variável de ambiente que definimos anteriormente $PKGID
* Sua sequência também é 1
    * Quando você faz alterações no código, você pode indicar as sequẽncia como 1, 2, 3, ...

* Resumidamente, vamos permitir que este pacote nesta visão identificada com esse ID de pacote seja executado neste canal para a nossa Org, que selecionamos até o momento, a Org 1. Este pacote será permitido para execução
* ANÁLISE DO RETORNO:
```shell
matheuslazaro@matheuslazaro:~/fabric/fabric-samples/test-network$ peer lifecycle chaincode approveformyorg \
      -o localhost:7050 \
      --ordererTLSHostnameOverride  orderer.example.com \
      --tls --cafile $ORDERER_CERTFILE  \
      --channelID channel1 \
      --name basic \
      --version 1 \
      --package-id $PKGID \
      --sequence 1

2024-01-08 19:23:18.827 -03 0001 INFO [chaincodeCmd] ClientWait -> txid [9b6e34c0cde624674f297067e265ed32cc0e2b91a01349f02c98048076e94c89] committed with status (VALID) at localhost:9051
```
* Recebemo uma mensagem de sucesso com status válido em localhost:9051
* 9051 é o nosso Peer da Org 1.
* 7050 é a Orderer
* Executando "docker ps", é possível visualizar que realmente estão sendo executados:
```shell

matheuslazaro@matheuslazaro:~$ docker ps
CONTAINER ID        IMAGE                               COMMAND                  CREATED             STATUS              PORTS                                                                                                                             NAMES
...
cb98b776457a        hyperledger/fabric-peer:latest      "peer node start"        5 hours ago         Up 5 hours          0.0.0.0:7051->7051/tcp, :::7051->7051/tcp, 0.0.0.0:9444->9444/tcp, :::9444->9444/tcp                                              peer0.org1.example.com
69ad96687460        hyperledger/fabric-orderer:latest   "orderer"                5 hours ago         Up 5 hours          0.0.0.0:7050->7050/tcp, :::7050->7050/tcp, 0.0.0.0:7053->7053/tcp, :::7053->7053/tcp, 0.0.0.0:9443->9443/tcp, :::9443->9443/tcp   orderer.example.com
2fe9abe28a4e        hyperledger/fabric-peer:latest      "peer node start"        5 hours ago         Up 5 hours          0.0.0.0:9051->9051/tcp, :::9051->9051/tcp, 7051/tcp, 0.0.0.0:9445->9445/tcp, :::9445->9445/tcp                                    peer0.org2.example.com
...
```
* Temos:
    * orderer.example.com, executando em 0.0.0.0:7050->7050/tcp ou localhost:7050
    * peer0.org1.example.com, executando em 0.0.0.0:7051->7051/tcp ou localhost:7051
    * peer0.org2.example.com, executando em 0.0.0.0:9051->9051/tcp ou localhost:9051

* Temos que fazer o mesmo procedimento para a Org 2:
```shell
setGlobals 2 

peer lifecycle chaincode approveformyorg \
      -o localhost:7050 \
      --ordererTLSHostnameOverride  orderer.example.com \
      --tls --cafile $ORDERER_CERTFILE  \
      --channelID channel1 \
      --name basic \
      --version 1 \
      --package-id $PKGID \
      --sequence 1  
```

* Feito isto, está aprovado para ambas as Orgs (Org 1 e Org 2)

#### Comprometar isso com o Ledger - Commit do ciclo de vida
```shell
peer lifecycle chaincode commit \
      -o localhost:7050 \
      --ordererTLSHostnameOverride orderer.example.com \
      --tls --cafile $ORDERER_CERTFILE \
      --channelID channel1 --name basic \
      --peerAddresses localhost:7051 --tlsRootCertFiles $ORG1_PEER_CERTFILE \
      --peerAddresses localhost:9051 --tlsRootCertFiles $ORG2_PEER_CERTFILE \
      --version 1 --sequence 1 
```
* Neste comando temos a cadeia do ciclo de vida, com domínio do compromentimento 
* O Orderer é novamente localhost:7050
* Indicamos Peers das duas Orgs, com seus respectivos arquivos de certificado
```Precisa-se de uma maioria para que os Peers concordem com um Commit antes que ele possa entrar no Ledger```
* Retorno:
```shell
2024-01-08 20:25:23.347 -03 0001 INFO [chaincodeCmd] ClientWait -> txid [f8303869a3f0c83f67501a7ebb35d7ee62e9340b0f1be9fed1dc90720a0fd8c5] committed with status (VALID) at localhost:9051
2024-01-08 20:25:23.375 -03 0002 INFO [chaincodeCmd] ClientWait -> txid [f8303869a3f0c83f67501a7ebb35d7ee62e9340b0f1be9fed1dc90720a0fd8c5] committed with status (VALID) at localhost:7051

docker ps:
CONTAINER ID        IMAGE                                                                                                                                                                    COMMAND                  CREATED              STATUS              PORTS                                                                                                                             NAMES
a10ccf31f105        dev-peer0.org1.example.com-basic_1.0-80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345-4eb813b6d190ed45fdea591e71483f31332e5f5e2c671ad0cfc74bd42a7d646c   "docker-entrypoint.s…"   About a minute ago   Up About a minute                                                                                                                                     dev-peer0.org1.example.com-basic_1.0-80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345
e8139a6220d4        dev-peer0.org2.example.com-basic_1.0-80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345-4d87cdd8b4a64e6a9a3ccbfd1056a14a59382ca97b8d05e294d3505a0a31921f   "docker-entrypoint.s…"   About a minute ago   Up About a minute                                                                                                                                     dev-peer0.org2.example.com-basic_1.0-80872a0c36a4c6285579422197775acd99298b37bdae4de6078be0018a52f345
```
* Significa que o Chaincode foi commitado, e está executando no Channel para 2 Orgs.
* Com isso, na sequência, podemos chamas as funções do Chaincode para que possamos iniciar o Ledger, criar ativos e excluir, etc.

# Interagindo com o Contrato Inteligente através do terminal
## Observando os Logs da Blockchain, no Back-end:
* Abra um novo terminal e execute:
```shell
cd ~/fabric/fabric-samples/test-network 
export PATH=$PATH:~/fabric/fabric-samples/bin/
export FABRIC_CFG_PATH=$PWD/../config/
export ORDERER_CERTFILE=$PWD/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
export ORG1_PEER_CERTFILE=$PWD/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export ORG2_PEER_CERTFILE=$PWD/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
source ./scripts/envVar.sh
setGlobals  1   
```

### Logs anteriores:
```shell
docker-compose -f docker/docker-compose-test-net.yaml logs  | more 
```

### Log de execução
```shell
docker-compose -f docker/docker-compose-test-net.yaml logs -f 
```









<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>
<hr/>

# Versão funcional (06/01/2023):
```shell
matheuslazaro@matheuslazaro:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.1 LTS
Release:        22.04
Codename:       jammy

matheuslazaro@matheuslazaro:~$ git --version
git version 2.43.0

matheuslazaro@matheuslazaro:~$ curl --version
curl 7.81.0 (x86_64-pc-linux-gnu)

matheuslazaro@matheuslazaro:~$ jq --version
jq-1.6

matheuslazaro@matheuslazaro:~$ docker --version
Docker version 19.03.12, build 48a66213fe

matheuslazaro@matheuslazaro:~$ docker-compose -v
docker-compose version 1.29.2, build 5becea4c

matheuslazaro@matheuslazaro:~$ npm --version
6.14.5

matheuslazaro@matheuslazaro:~$ nodejs --version
v12.18.2
```
