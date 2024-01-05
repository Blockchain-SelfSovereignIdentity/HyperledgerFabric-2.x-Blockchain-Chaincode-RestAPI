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