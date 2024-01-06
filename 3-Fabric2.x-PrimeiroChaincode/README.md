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
matheuslazaro@matheuslazaro:~$ sudo apt-get install git curl jq
```

* Verificação:
    ```shell
    matheuslazaro@matheuslazaro:~$ git -v
    git version 2.43.0

    matheuslazaro@matheuslazaro:~$ curl --version
    curl 7.81.0 (x86_64-pc-linux-gnu) ...
    Release-Date: 2022-01-05
    ...

    matheuslazaro@matheuslazaro:~$ jq --help
    jq - commandline JSON processor [version 1.6]
    ```
### Docker
Docker é uma plataforma de código aberto que permite automatizar o desenvolvimento, implantação e execução de aplicativos em contêineres. Um contêiner é uma unidade padrão de software que empacota o código e todas as suas dependências para que o aplicativo seja executado de forma rápida e confiável de um ambiente de computação para outro. O Docker simplifica o fluxo de gerenciamento de processos de aplicações em contêineres, permitindo que os desenvolvedores dividam um ambiente de aplicação em vários serviços isolados.

* Remover versão antiga do Docker:
```shell
sudo apt-get remove docker
sudo apt-get purge docker.io
sudo apt-get autoremove --purge docker.io
sudo rm -rf /var/lib/docker
sudo groupdel docker
sudo rm -rf /etc/docker
sudo rm /usr/bin/docker
sudo rm /snap/bin/docker
```
* Instalar:
```shell
sudo apt-get install docker
sudo apt-get remove containerd.io
sudo apt-get install containerd
sudo apt-get install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

* Verificação: 
    ```shell
    matheuslazaro@matheuslazaro:~$ docker --version
    Docker version 24.0.5, build 24.0.5-0ubuntu1~22.04.1


    ```

### Docker-compose
Docker-compose é uma ferramenta para definir e executar aplicativos multi-contêiner com o Docker. Ele usa as definições de serviço para compilar ambientes totalmente personalizados com contêineres múltiplos que podem compartilhar redes e volumes de dados. O Docker-compose permite construir e executar vários contêineres no mesmo host, bastando apenas um arquivo YAML com as instruções e parâmetros desejados para os nossos contêineres e que com um único comando conseguimos realizar a execução e/ou atualização de todos eles.

* Remover versão antiga:
```shell
matheuslazaro@matheuslazaro:~$ sudo apt-get remove docker-compose
matheuslazaro@matheuslazaro:~$ sudo rm /usr/local/bin/docker-compose
```

* Istalar:
```shell
matheuslazaro@matheuslazaro:~$ sudo apt-get install -y docker-compose

matheuslazaro@matheuslazaro:~$ sudo ln -s /usr/bin/docker-compose /usr/local/bin/docker-compose

# Atualizar biblioteca paramiko
matheuslazaro@matheuslazaro:~$ pip install --upgrade paramiko

```

* Verificação:
    ```shell
    matheuslazaro@matheuslazaro:~$ docker-compose -v
    docker-compose version 1.29.2, build unknown
    ```

#### Modificar o usuário
```shell
echo $USER
ewgrp docker # logar no grupo docker
sudo usermod -a -G docker $USER
```

Obs.: O Fabric usa por padrão a linguagem GO, entretanto, por escolha própria, usaremos a linguagem Javascript, com Node.js.

### Node.js 12.x (Versão suportada)
* Instalação
```shell
sudo apt-get install nodejs

ou

curl -fsSL https://deb.nodesource.com/setup_12.x | sudo -E bash -

sudo apt update

sudo apt-get install -y nodejs
```

* Verificação:
    ```shell
    matheuslazaro@matheuslazaro:~$ nodejs -v
    v12.22.9
    ```
### npm
* Instalação
```shell
sudo apt-get install npm
```

* Verificação:
    ```shell
    matheuslazaro@matheuslazaro:~$ npm -- version
    {
        npm: '8.5.1',
        node: '12.22.9',
        v8: '7.8.279.23-node.56',
        uv: '1.43.0',
        zlib: '1.2.11',
        brotli: '1.0.9',
        ares: '1.18.1',
        modules: '72',
        nghttp2: '1.43.0',
        napi: '8',
        llhttp: '2.1.6',
        http_parser: '2.9.4',
        openssl: '1.1.1m',
        cldr: '40.0',
        icu: '70.1',
        tz: '2023c',
        unicode: '14.0'
    }
    ```