# Introdução-Hashes-Blockchains-SmartContracts
O Composer tornou muito mais fácil aprender Fabric, na versão 1.x. Entretanto, após a versão 2.x do Fabric, essa situação mudou.

## Hash
```Como Hashes permitem a construção de uma cadeia de blocos e o que realmente é uma Blockchian```

Bitcon e Criptomoedas são os mais populares casos de uso para Blockchain. Hashes são o núcleo de uma Blockchain.

Imagine um cenário em que você tenha um arquivo muito grande, de 1GB, e que vocẽ queira enviá-lo para uma amigo. Entretanto, você não confia no proprietário do arquivo, e você não tem certeza se o proprietário será capaz de entregar o arquivo 100% intacto para o seu amigo.
É importante que nem um único bit mude no arquivo. Enviar este arquivo através de um canal seguro seria muito lento e muito caro. Portanto, vocẽ deseja enviar esse arquivo no canal normal, mas há um problema de se certificar na outra ponta de que o arquivo foi secreto.

Solução: 
* Imagine que o arquivo é a frase: "I owe you 1,000,000. Collect it after 10 years.". Vamos aplicar nesta frase uma função que computa uma representação muito menor de todo esse texto. Como isso é feito não é importante. O importante é que essa será uma força de tamanho fixo. Será um número fixo de bits.
* Usando SHA-256, temos: 38630713476a2ff199e14a161acf1fe49f5f5ce4b4fb5ee2e5ff8624a10de4fe
* Temos sempre 64 bytes, independente do valor.
* Não é possível voltar do hash para os dados originais.
* Se você tiver os dados e puder colocar um hash usando o mesmo algoritmo, você sempre vai obter o mesmo valor de volta. Isso é muito importante, e é este o conceito de hash. Você escolhe uma informação que você coloca em uma caixa preta e sai um número de tamanho fixo. Se você alterar a entrada, a saída será alterada.

Hashes têm uso além de Blockchain. Existem há muito tempo, como por exemplo para garantir integridade de dados

## Blockchain
Imagine um cenário em que temos algumas informações para que tenhamos um número de bloco e que vamos chamar de 1. Temos algumas peças de informação. O primeiro é o número do bloco que é 1. Em seguida temos o Nonce, que tembém é um número (0). Então temos os dados, e um hash anterior.

Queremos calcular o hash de todas essas coisas combinadas.
Block number: 1
Nonce: 0
Data: A >> B: 40
Previous Block hash: 00000000000000000000000000000000000000000000000000000000000000000000
This block hash:     xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

O conceito de Mineração está relacionado à alteração do Nonce, incrementando, para satisfazer a um critério. Por exemplo, que o Hash comece com 3 zeros. Obs.: Bitcoin exige, atualmente, que o hash comece com 18 zeros, tornando um trabalho difícil computacionalmente.

Neste contexto, a Blockchain é formada pela cadeia de blocos conectados uns aos outros através dos hashes. Cada Bloco possui o próprio hash e o hash do bloco anterior.

## Contratos inteligentes
Consiste em pegar um contrato, ou seja, um acordo entre duas partes, e colocá-lo em um bloco de dados. Então, se você colocá-lo no bloco, ele vai ter algum pedaço de código que vai garantir que certas condições estão reunidas. Então, se essas condições forem atendidas, o próximo bloco será criado, e se o próximo bloco for criado, ele garantirá que naquele momento as condições foram atendidas. Isso vai levar a um conceito muito amplo de validação, em que certas condições em um determinado momento foram atendidas e depois foram desenhadas sendo capazes de garantir que foram atendidas.

Um exemplo disso pode ser que você tenha algum alguma parte que solicite alguns dados de outra parte, e a primeira parte pode escrever um contrato inteligente que dirá que se você nos fornecer esses dados, divulgaremos tal e daremos tal quantidade de dinheiro para você. Se as condições forem atendidas de ambos os lados, a transação ocorrerá; caso contrário, não.

Um contrato inteligente não precisa necessariamente envolver uma criptomoeda. Pode-se fazer qualquer coisa acontecer através de um contrato inteligente. Como tudo está na Blockchain, todas as partes serão capazes de garantir que toda a atividade foi realizada de acordo com o contrato inteligente.

Um contrato inteligente é muito maior do que a criptomoeda, então a criptomoeda é apenas um caso de uso de contratos inteligentes. Exatamente por isso, que existe o Hyperledger.

O Hyerpeldger é um ecossistema que está incubando muitos projetos diferentes entre si. o mais popular é o Fabric. Essencialmente, o que o Fabriz faz é criar ume estrutura que permite que você use o que é chamado de Ledger Distribuído. Na Blockchain, todos os blocos são distribuídos e descentralizados. Todos têm acesso ao Ledger e todos podem usá-lo para verificar informações. Ele permite criar aplicações de negócios com muita facilidade

Um Contrato inteligentes pode ser escrito em diferentes linguagens. Escreveremos um Contrato Inteligente com Javascript.

