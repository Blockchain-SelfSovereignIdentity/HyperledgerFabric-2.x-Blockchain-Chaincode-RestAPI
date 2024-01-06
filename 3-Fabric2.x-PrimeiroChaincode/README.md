# Fabric 2.x - Primeiro Chaincode
```Configuração e pré-requisitos do Fabric 2.x```
* Recomendado: Ubuntu 20.04
    * Usado: Ubuntu 22.04.1 LTS
* Certificar-se do bash estar acessível.
    ```shell
    matheuslazaro@matheuslazaro:~/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI$ which bash
    /usr/bin/bash
    ```

## Utilitários básicos
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


```
* Instalar:
https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/

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

## A arquitetura
Uma vez que temos um ambiente de desenvolvimento configurado, podemos seguir e baixar os binários do Fabric, para conseguirmos executar os Peers, os Orderers, e todos os componentes que são necessários.

Desta forma, na arquitetura a ser criada, vamos ter um lado de Chaincode. Então tudo isso vai rodar em cima de uma Blockchain. Também vamos ter vários Peers ou várias Orgs que estará funcionnando no nosso Contrato Inteligente ou Chaincode. Eles são usados de forma intercambiável no Chaincode do ecossistema maior, nosso Contrato Inteligente, são a mesma coisa.

Também teremos uma Aplicação cliente básica, que usará as bibliotecas do Node (Node-SDK) para se conectar com a Blockchain.

Então vamos ter as partes da Blockchain, que vão ser duas Orgs e um Orderer, e vamos ter uma Aplicação cliente que vai se conectar com a Blockchain usando as APIs do Node.

<img src="https://raw.githubusercontent.com/Blockchain-SelfSovereignIdentity/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI/main/3-Fabric2.x-PrimeiroChaincode/arquitetura.png" alt="Arquitetura">

Podemos ter a arquitetura básica do Orderer e vários Peers. Como exemplo, vamos ter dois Peers, Org 1 e Org 2. Vamos ter um canal único e vamos criar blocos nesse canal usando as APIs que nos são fornecidas.

<img src="https://raw.githubusercontent.com/Blockchain-SelfSovereignIdentity/HyperledgerFabric-2.x-Blockchain-Chaincode-RestAPI/main/3-Fabric2.x-PrimeiroChaincode/diagramaTransacoesAplicacao.png" alt="Diagrama de Transacoes da Aplicação">

## Obtendo binários da versão de testes do Fabric (Incluindo Chaincode de amostra)
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

* Subir a rede, Criar o canal, e configurar com uma Autoridade de Certificação
```shell
./network.sh up createChannel -c channel1 -ca
```

* Matar a rede: 
```shell
./network.sh down

```
* matar conteiners:
```shell
docker kill $(docker ps -q)
```
########################################################################################################################################################################

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