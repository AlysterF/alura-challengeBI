# Desafio 3: AluraStore Dashboard Financeiro 📈🤑

[Acessar o Dashboard](https://app.powerbi.com/view?r=eyJrIjoiYjk1M2Q5OTgtYjhiNy00OGY0LTk1MjAtY2M5YmI0ZDEzNmIwIiwidCI6IjA3NjFiYTk4LTk2NmUtNDBlZi1hZTMzLTVkZTMzZTBlNjE5NCJ9)

<br>

### Índice

[Estudo de caso](#businesscase)

[Base de dados](#database)

[Solução](#solution)

[Contato](#contact)


<br>
<a name="businesscase"/>

### Estudo de caso

Desenvolveremos um dashboard tático da área financeira de uma empresa,  no qual vamos criar duas páginas, sendo uma delas exibindo um overview  de toda a área financeira e a outra página  realizando uma análise de  cenários.
Teremos duas etiquetas indicando o que precisaremos pensar, sendo elas:
1 - Overview financeiro.
2 - Análise de cenário.


<br>

### Requisitos

<ul>
    <li>Calcular e exibir a receita</li>
    <li>Calcular e exibir os custos</li>
    <li>Calcular e exibir a despesa</li>
    <li>Calcular e exibir o lucro</li>
    <li>Analisar e exibir mensalmente as métricas</li>
    <li>Permitir filtrar por ano</li>
    <li>Criar uma análise de cenário</li>
</ul>



<br>
<a name="database"/>

### Base de Dados

Foram disponibilizadas quatro bases de dados em SQL para esse desafio, sendo elas:

<ul>
    <li>Notas Fiscais</li>
    <li>Pedidos</li>
    <li>Produtos</li>
    <li>Vendedores</li>
</ul>





A <b>Tabela Notas Fiscais</b> possui:

<ul>
    <li>id_nota</li>
   	<li>numero_nota</li>
    <li>id_pedido</li>
    <li>id_vendedor</li>
    <li>valor_venda</li>
    <li>frete</li>
    <li>imposto</li>
</ul>





A <b>Tabela Pedidos</b> possui:

<ul>
    <li>id_pedido</li>
	<li>id_produto</li>
    <li>quantidade</li>
    <li>data_compra</li>
</ul>





A <b>Tabela Produtos</b> possui:

<ul>
    <li>id_produto</li>
	<li>categoria_produto</li>
    <li>preco</li>
    <li>custos</li>
</ul>





A <b>Tabela Vendedores</b> possui:

<ul>
    <li>id_vendedor</li>
	<li>nome_vendedor</li>
</ul>







### 

<a name="solution"/>

<br>

### Solução

<br>

Utilizei o UniController para simular um servidor MySQL e o HeidiSQL para poder restaurar as tabelas e analisar os dados. Para criação do dashboard, utilizei o Power BI.



#### Passo 1: Conexão e tratamento dos dados

O PowerBI possui uma conexão nativa com o MySQL, assim pude fazer a conexão diretamente com a base local e carregar as tabelas do <i>schema</i> Financeiro.

Após essa importação, analisei e fiz algumas alterações nos dados.



<b>Tabelas pedido e notas fiscais</b>

A planilha pedido apresenta o id, o produto, a quantidade e a data em que a compra foi realizada. Enquanto a planilha de notas fiscais traz mais detalhes sobre o número da nota, o vendedor associado ao pedido e valores de venda, frete e impostos, além de trazer também o id do pedido.

Visto que o id_produto estava presente em ambas as tabelas, decidi juntar as tabelas em uma só utilizando o <b>Mesclar Consultas como Nova</b>, que além de mesclar as tabelas, <u>gera uma nova tabela resultante e mantém as tabelas originais intactas</u>.

Após a mescla, <b>removi as colunas de valor_venda e frete</b>, pois todos os <b>valores estavam incorretos</b>, alguns como se os valores estivessem multiplicados por 100 e outros com outras falhas. Visto que os valores de venda eram resultantes da multiplicação da quantidade do pedido pelo preço do produto, decidi realizar o <b>cálculo dos valores corretos através de medidas</b>, mas explicarei melhor sobre isso posteriormente.



<b>Tabela vendedores</b>

A planilha vendedores apresentou diversos problemas, veja os dados originais:

![image-20210923184120370](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210923184120370.png)



Perceba que não padrão algum quanto ao nome do vendedor, há nomes com todas as letras minúsculas, todas as letras maiúsculas, separados por <i>underscore</i>, entre outros erros.

As transformações para corrigir os problemas foram as seguintes:

<ul>
	<li>Substituição do <i>underscore</i> por espaço em branco com Substituir Valores</li>
    <li>Colocação das primeiras letras em maiúscula e as demais minúsculas para todos os nomes</li>
    <li>Remoção do excesso de letras A no sobrenome da Aline Silva</li>
    <li>Limpeza do texto para garantir que não há espaços extras</li>
    <li>Remoção da coluna de referência as notas fiscais</li>
    <li>Remoção do ID 6 para evitar a duplicidade do vendedor Joaquim Adalto*</li>
</ul>

*A remoção do id 6 consequentemente resulta na necessidade de substituir o id 6 por 3 na tabela que gerei anteriormente (pedidos + notas fiscais), para garantir que o relacionamento entre as tabelas funcione corretamente.



<b>Tabela Produto</b>

Na tabela produto realizei as seguintes alterações:

<ul>
    <li>Substituir o <i>underscore</i> por espaço em branco</li>
    <li>Colocar a primeira letra das palavras como maiúscula</li>
    <li>Dividir o preço e  o custo por 100, pois o erro nesses campos era a ausência do separador decimal</li>
    <li>Exclusão das colunas originais de preço e custo</li>
</ul>



<b>Tabela Calendário</b>

A tabela calendário não é uma tabela gerada automaticamente nem fazia parte da base de dados fornecida, portanto, teve de ser criada do zero.

Utilizei a função <b>List.Dates</b> da Linguagem M para criar um range de datas pré-determinado.

Considerando um contexto de negócios em que a base poderá ter um update com a adição de novos dados, decidi deixar a função <b>List.Dates</b> um pouco mais dinâmica, detectando automaticamente a menor e a maior data de compra disponível na base. Para isso, utilizei as funções <b>List.Min</b>, <b>List.Max</b> e para calcular a diferença de dias entre as datas, a função <b>Duration.Days</b>.

O código que desenvolvi na linguagem M para criar a tabela calendário foi:

````
MinDate = List.Min(Table.Column(fPedido,"data_compra")),
MaxDate = List.Max(Table.Column(fPedido,"data_compra")),
DateDiff = Duration.Days(MaxDate - MinDate) + 1,
Fonte = List.Dates(MinDate, DateDiff, #duration(1, 0, 0, 0))
````

Perceba que no cálculo da variável DateDiff, que determina o intervalo de dias entre a maior e a menor data, está com a soma de 1. Isso ocorre, pois o cálculo da diferença sempre resulta em um valor a menos, pois não considera que o primeiro dia faz parte da conta. É importantíssimo somar +1 para que seus dados sejam sincronizados corretamente com a tabela calendário.

 

A partir da criação da tabela com as datas desejadas, inseri novas colunas para gerar:

<ul>
    <li>Ano</li>
    <li>Mês</li>
    <li>Nome do Mês</li>
    <li>Dia do Mês</li>
</ul>



Ao finalizar todas os ajustes nas tabelas, criei os relacionamentos entre elas, para que os dados entre as mesmas fossem corretamente sincronizados:

![image-20210923234529808](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210923234529808.png)






#### Passo 2: Criação de medidas 



<b>Requisitos Principais</b>

Para alcançar os requisitos do projeto, criei algumas medidas:



<ul>
    <li><b>Custo Total</b></li>
    SUMX(fPedido, fPedido[quantidade] * RELATED(dProduto[custo_produto]))
    <li><b>Imposto Total</b></li>
	SUM(fPedido[imposto])
    <li><b>Receita Bruta</b></li>
    SUMX(fPedido, fPedido[quantidade] * RELATED(dProduto[preco_produto]))
    <li><b>Receita Líquida</b></li>
    [Receita Bruta] - [Imposto Total]
    <li><b>Frete Total</b></li>
    [Receita Bruta]*0.1
    <li><b>Despesa Total</b></li>
    [Imposto Total] + [Frete Total]
    <li><b>Lucro Bruto</b></li>
    [Receita Bruta] - [Custo Total]
    <li><b>Lucro Líquido</b></li>
    [Receita Líquida] - [Frete Total] - [Custo Total]
</ul>



Vale ressaltar que é aqui que entram as medias que cobrem a ausência dos valores de vendas e frete que foram removidos na tratativa de dados.

Utilizando SUMX pude calcular a receita, que é mesmo que o valor de venda, e utilizar essa nova medida para calcular o frete, que é 10% do valor de venda.



<b>Métricas variáveis para o simulador de cenários</b>

Para a criação dessas métricas, foi necessário criar um parâmetro para cada possível variação. Sendo assim, inseri, através da aba modelagem, parâmetros que variam de -100% a 100%, com um intervalo de 5% para Custo, Preço, Demanda (quantidade vendida) e Imposto.

Após a criação desses parâmetros, o PowerBI cria medidas que podem ser usadas para integrar aos cálculos de novas métricas. Sendo assim, criei as seguintes métricas:

 <ul>
    <li><b>Cenário Custo Total</b></li>
    SUMX(fPedido, (fPedido[quantidade] * (1+'Cenário Demanda'[Demanda Valor])) * (RELATED(dProduto[custo_produto]) * (1+'Cenário Custo de Produção'[Custo de Produção Valor])))
    <li><b>Cenário Imposto Total</b></li>
	SUM(fPedido[imposto]) * (1+'Cenário Imposto'[Cenário Imposto Valor])
    <li><b>Cenário Receita Bruta</b></li>
    SUMX(fPedido, (fPedido[quantidade] * (1+'Cenário Demanda'[Demanda Valor])) * (RELATED(dProduto[preco_produto]) * (1+'Cenário Preço do Produto'[Preço do Produto Valor])))
    <li><b>Cenário Receita Líquida</b></li>
    [Cenário Receita Bruta] - [Cenário Imposto Total]
    <li><b>Cenário Despesa Total</b></li>
    [Cenário Imposto Total] + [Frete Total]
    <li><b>Cenário Lucro Bruto</b></li>
    [Cenário Receita Bruta] - [Cenário Custo Total]
    <li><b>Cenário Lucro Líquido</b></li>
    [Cenário Receita Líquida] - [Cenário Custo Total] - [Frete Total]
</ul>



E para calcular um % de variação comparando as métricas do cenário com as métricas originais, criei as seguintes medidas:

 <ul>
    <li><b>Variação do Custo</b></li>
    [Cenário Custo Total]/[Custo Total]-1
    <li><b>Variação do Lucro</b></li>
	[Cenário Lucro Líquido]/[Lucro Líquido]-1
    <li><b>Variação da Receita</b></li>
    [Cenário Receita Líquida]/[Receita Líquida]-1
     <li><b>Variação de Despesa</b></li>
     [Cenário Despesa Total]/[Despesa Total]-1
</ul>



Criadas todas essas medidas, chegou a hora da parte mais sensacioshow de todas, criar o dash!



#### Passo 3: O Dashboard

Você pode acessar o dashboard interativo completo clicando <b> [AQUI](https://app.powerbi.com/view?r=eyJrIjoiYjk1M2Q5OTgtYjhiNy00OGY0LTk1MjAtY2M5YmI0ZDEzNmIwIiwidCI6IjA3NjFiYTk4LTk2NmUtNDBlZi1hZTMzLTVkZTMzZTBlNjE5NCJ9)</b>



<b>O painel inicial</b> traz os cards abaixo:

<ul>
    <li>Receita Líquida</li>
    <li>Lucro Líquido</li>
    <li>Custo Total</li>
    <li>Despesa Total</li>
</ul>

Além de trazer:

- Um gráfico de colunas e linha, para mostrar a receita líquida e o lucro por mês no mesmo gráfico.
- Um gráfico de área para demonstrar o valor de despesa por mês
- Um gráfico de barras apresentando os 5 produtos com maior custo
- Um gráfico de linha/série temporal para exibir o custo por mês



Por fim, incluí um filtro para que os dados possam ser exibidos de acordo com o(s) ano(s) selecionados.

![image-20210924000901635](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210924000901635.png)



Já o <b>painel do simulador</b> traz os mesmos cards do painel anterior, mas considerando os valores alterados de acordo com a variação dos parâmetros. Além de manter o filtro do ano, o gráfico de colunas e linha para apresentar os valores de receita e lucro por mês com a variação do cenário e o gráfico de linha/série temporal para mostrar o valor do custo por mês com variação do cenário.




![image-20210924001213230](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210924001213230.png)



<br>

<a name="contact"/>

### Contato

Sinta-se a vontade para me contatar:

[<img src="https://img.shields.io/badge/linkedin-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white" />](https://www.linkedin.com/in/alysterfernandes/)

[<img src="https://img.shields.io/badge/medium-%2312100E.svg?&style=for-the-badge&logo=medium&logoColor=white" />](https://medium.com/@alyster)
