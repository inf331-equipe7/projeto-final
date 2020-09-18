# Projeto Marketplace

# Equipe
- Aruã Puertas
- Daniela Bouwman
- Guilherme Kayo Shida
- Nicholas Borba
- Simeão Lamine

# Nível 1

Apresente aqui o detalhamento do Nível 1 conforme detalhado na especificação com, no mínimo, as seguintes subseções:

## Diagrama Geral do Nível 1

> ![Diagrama no nível 1](images/coreografia.png)

### Detalhamento da interação de componentes

#### Processo de compra

* O `componente Buyer` inicia o processo de compra publicando no barramento a mensagem de tópico `"product/search"` através da interface ISearch;
* O `componente Product` assina no barramento de mensagens de tópico `"product/search"` através da `interface ISearch`. Quando recebe uma mensagem, ele mostra os produtos;
* O `componente Buyer` seleciona um produto publicando no barramento a mensagem de tópico `"product/{id}/details"` através da `interface IProduct`;
* O `componente Product` assina no barramento de mensagens de tópico `"product/{id}/details"` através da `interface IProduct`. Quando recebe uma mensagem, ele mostra os dados do produto selecionado;
* O `componente Buyer` publica no barramento de mensagem de tópico `"order/{fornecedorId}/create/{produtoId}"` através da `interface IOrder` realizando o pedido;
* O `componente Order` assina no barramento de mensagens de tópico `"order/{fornecedorId}create/{produtoId}"` através da `interface IOrder`. Quando recebe uma mensagem, ele valida as informações do pedido publicando no barramento de mensagem de tópico `"payment/check/{orderId}"` através da `interface IPayment`;
* O `componente Payment` assina no barramento de mensagens de tópico `"payment/check/{orderId}"` através da `interface IPayment`. Quando recebe a mensagem,  ele publica no barramento de mensagem de tópico `"payment/order/{orderId}/denied"` através da `interface IPayment` caso o pagamento não foi aprovado ou publica no barramento de mensagem de tópico `"payment/order/{orderId}/confirmed"` atráves da `interface IPayment` caso o pagamento foi confirmado;
* O `component Buyer` assina no barramento de mensagem de tópico `"payment/order/{orderId}/+"` através da `interface IPayment`. Quando recebe uma mensagem, ele verifica se o pedido foi confirmado ou negado;
* O `componente Seller` assina no barramento de mensagem de tópico `"payment/order/{orderId}/confirmed"` através da `interface IPayment`. Quando ele recebe a mensagem, dá início ao processo de envio do produto publicando no barramento de mensagem de tópico `"dispatcher/order/{orderId}"` através da `interface ISeller`;
* O `componente Shipping` assina no barramento de mensagem de tópico `"dispatcher/order/{orderId}"` através da `interface ISeller`. Quando recebe uma mensagem, ele publica no barramento de mensagens de tópico `"location/status"` através da `interface IShipping` atualizando o estado da entrega;
* O `component Buyer` assina no barramento de mensagem de tópico `"location/status"` através da `interface IShipping`. Quando recebe uma mensagem, exibe as informações da entrega;
* O `componente Seller` assina no barramento de mensagem de tópico `"location/status"` através da `interface IShipping`. Quando ele recebe a mensagem, atualiza as informações na base de dados;
* O `componente Recommendation` assina o tópico `"order/create"` atraveś da `interface IOrder` para monitorar os produtos mais requisitados e seus respectivos fornecedores afim de melhorar o seu algoritmo de recomendação. Além disso, também assina o tópico `"payment/order/+"` através da `interface IPayment` para entender qual é a forma de pagamento mais comum praticado pelos compradores.

#### Leilão Invertido

* O `componente Buyer` inicia o processo de leilão publicando no barramento a mensagem de tópico `"auction/create"` através da `interface ICreateAuction`;
* O `componente Auction` assina no barramento de mensagem de tópico `"auction/create"` através da `interface ICreateAuction`. Quando ele recebe a mensagem, ele inicia o leilão com o produto que o comprador deseja e com um tempo limite publicando uma mensagem no barramento de tópico `"auction/{auctionId}/begin"` através da `interface IAuction`;
* O `componente Seller` assina no barramento de mensagem de tópico `"auction/{auctionId}/begin"` através da `interface IAuction`. Quando recebe uma mensagem, os fornecedores podem enviar seus lances para o produto desejado publicando no barramento de mensagens de tópico `"auction/{auctionId}/bid"` através da `interface IAuction`;
* O `componente Seller` assina no barramento de mensagem de tópico `"auction/{auctionId}/bid"` através da `interface IAuction`. Quando recebe uma mensagem, o fornecedor verifica o novo lance do fornecedor concorrente e determina se vai oferecer um valor menor publicando outra mensagem no barramento usando o mesmo tópico e interface;
* O `componente Buyer` assina no barramento de mensagem de tópico `"auction/{auctionId}/bid"` através da `interface IAuction`. Quando recebe uma mensagem, o comprador pode ver os lances do leilão;
* O `componente Auction` informa o final do leilão publicando uma mensagem no barramento de tópico `"auction/{auctionId}/finish"` através da `interface IAuction`;
* O `componente Buyer` assina no barramento de mensagem de tópico `"auction/{auctionId}/finish"` através da `interface IAuction`. Quando recebe uma mensagem, o comprador pode ver lance final;
* O `componente Seller` assina no barramento de mensagem de tópico `"auction/{auctionId}/finish"` através da `interface IAuction`. Quando recebe uma mensagem, o informa ao fornecedor que ofereceu o menor lance que ele ganhou o leilão;
* O `componente Recommendation` assina o tópico `"auction/+/finish"` atraveś da `interface IAuction` para monitorar o produto, o menor preço e o fornecedor.

## Componente `<Nome do Componente>`

> <Resumo do papel do componente e serviços que ele oferece.>

![Componente](diagrama-componente-mensagens.png)

**Interfaces**
> * Listagem das interfaces do componente.

As interfaces listadas são detalhadas a seguir:

## Detalhamento das Interfaces

### Interface `<nome da interface>`

> Resumo do papel da interface.

**Tópico**: `<tópico que a respectiva interface assina ou publica>`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-rest.png)

~~~json
<Formato da mensagem JSON associada ao objeto enviado/recebido por essa interface.>
~~~

Detalhamento da mensagem JSON:

Atributo | Descrição
-------| --------
`<nome do atributo>` | `<objetivo do atributo>`

## Exemplo

### Interface DadosPedido

Interface para envio de dados do pedido com itens associados.

**Tópico**: `pedido/{id}/dados`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-rest.png)

~~~json
{
  "number": 16,
  "duoDate": "2009-10-04",
  "total": 1937.01,
  "items": {
    "item": {
       "itemid": "1245",
       "quantity": 1
    },
    "item": {
       "itemid": "1321",
       "quantity": 1
    }
  }  
}
~~~

Detalhamento da mensagem JSON:

**Order**
Atributo | Descrição
-------| --------
number | número do pedido
duoDate | data de vencimento
total | valor total do pedido
items | itens do pedido

**Item**
Atributo | Descrição
-------| --------
itemid | identificador do item
quantity | quantidade do item

# Nível 2

Apresente aqui o detalhamento do Nível 2 conforme detalhado na especificação com, no mínimo, as seguintes subseções:

## Diagrama do Nível 2

Apresente um diagrama conforme o modelo a seguir:

> ![Modelo de diagrama no nível 2](images/diagrama-subcomponentes.png)

### Detalhamento da interação de componentes

O detalhamento deve seguir um formato de acordo com o exemplo a seguir:

* O componente `Entrega Pedido Compra` assina no barramento mensagens de tópico "`pedido/+/entrega`" através da interface `Solicita Entrega`.
  * Ao receber uma mensagem de tópico "`pedido/+/entrega`", dispara o início da entrega de um conjunto de produtos.
* Os componentes `Solicita Estoque` e `Solicita Compra` se comunicam com componentes externos pelo barramento:
  * Para consultar o estoque, o componente `Solicita Estoque` publica no barramento uma mensagem de tópico "`produto/<id>/estoque/consulta`" através da interface `Consulta Estoque` e assina mensagens de tópico "`produto/<id>/estoque/status`" através da interface `Posição Estoque` que retorna a disponibilidade do produto.

Para cada componente será apresentado um documento conforme o modelo a seguir:

## Componente `<Nome do Componente>`

> <Resumo do papel do componente e serviços que ele oferece.>

![Componente](images/diagrama-componente.png)

**Interfaces**
> * Listagem das interfaces do componente.

As interfaces listadas são detalhadas a seguir:

## Detalhamento das Interfaces

### Interface `<nome da interface>`

> ![Diagrama da Interface](images/diagrama-interface-itableproducer.png)

> <Resumo do papel da interface.>

Método | Objetivo
-------| --------
`<id do método>` | `<objetivo do método e descrição dos parâmetros>`

## Exemplos:

### Interface `ITableProducer`

![Diagrama da Interface](images/diagrama-interface-itableproducer.png)

Interface provida por qualquer fonte de dados que os forneça na forma de uma tabela.

Método | Objetivo
-------| --------
`requestAttributes` | Retorna um vetor com o nome de todos os atributos (colunas) da tabela.
`requestInstances` | Retorna uma matriz em que cada linha representa uma instância e cada coluna o valor do respectivo atributo (a ordem dos atributos é a mesma daquela fornecida por `requestAttributes`.

### Interface `IDataSetProperties`

![Diagrama da Interface](images/diagrama-interface-idatasetproperties.png)

Define o recurso (usualmente o caminho para um arquivo em disco) que é a fonte de dados.

Método | Objetivo
-------| --------
`getDataSource` | Retorna o caminho da fonte de dados.
`setDataSource` | Define o caminho da fonte de dados, informado através do parâmetro `dataSource`.

# Multiplas Interfaces

> Escreva um texto detalhando como seus componentes  podem ser preparados para que seja possível trocar de interface apenas trocando o componente View e mantendo o Model e Controller.
>
> É recomendado a inserção de, pelo menos, um diagrama que deve ser descrito no texto. O formato do diagrama é livre e deve ilustrar a arquitetura proposta.