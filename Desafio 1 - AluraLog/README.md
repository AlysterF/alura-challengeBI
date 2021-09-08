# Desafio 1: AluraLog Dashboard de Logística 🚚



### Índice

[Estudo de caso](#businesscase)

[Base de dados](#database)

[Solução](#solution)

[Contato](#contact)



<a name="businesscase"/>

### Estudo de caso:

A pessoa que gerencia a área de logística da Alura Log, está enfrentando algumas mudanças em sua área por conta do aumento da demanda dos serviços de logística no período da pandemia. Ela quer manter a qualidade de seu serviço, mas para isso precisa acompanhar constantemente as métricas do seu departamento para tomar as melhores decisões. Quando nos contou isso, analisamos que para auxiliar nesse desafio precisaremos fazer um dashboard para logística. Para isso, vamos visualizar algumas métricas muito importantes para a área.



### Requisitos:

<ul>
  <li>Visualizar quantas entregas foram feitas no prazo;</li>
  <li>Visualizar quantas entregas foram feitas com atraso;</li>
  <li>Visualizar o número de veículos disponíveis;</li>
  <li>Exibir o índice de ocorrências por estado;</li>
  <li>Exibir o nível médio de estoque por ano;</li>
  <li>Calcular o Ship to Door (S2D) - tempo da ordem até a entrega em dias;</li>
  <li>Extra: Incluir outros insights relevantes.</li>
</ul>


<a name="database"/>

### Base de Dados

Foram disponibilizadas 4 bases de dados para esse desafio, sendo elas:

<ul>
    <li>Tabela de Estoque</li>
    <li>Tabela de Pedidos</li>
    <li>Tabela de Produtos</li>
    <li>Tabela de Veículos</li>
</ul>




A <b>Tabela de Estoque</b> apresenta <i>snapshots</i> do estoque em diversos momento do tempo e possui:

<ul>
    <li>ID Produto</li>
    <li>Data atualização</li>
    <li>Quantidade</li>
</ul>




A <b>Tabela de Pedidos</b> apresenta o registro de todos os pedidos ocorridos e possui:

<ul>
    <li>ID Pedido</li>
    <li>ID Produto</li>
    <li>Quantidade</li>
    <li>ID Veículo</li>
    <li>Status do pedido</li>
    <li>Data da compra</li>
    <li>Data da entrega</li>
    <li>Data previsão</li>
    <li>Latitude</li>
    <li>Longitude</li>
    <li>UF da entrega</li>
</ul>





A <b>Tabela de Produtos</b> apresenta todos os produtos registrados no sistema e possui:

<ul>
    <li>categoria_produto</li>
    <li>preço</li>
</ul>




A <b>Tabela de Veículos</b> apresenta os registros de disponibilidade atual dos veículos da AluraLog e possui:

<ul>
    <li>ID Veículos</li>
    <li>Tipo</li>
    <li>Status</li>
</ul>

### 

<a name="solution"/>


### Solução

#### Passo 1: Tratamento de dados

Utilizei o Tableau Prep Builder para efetuar os tratamentos de dados.

Foram importadas todas as tabelas disponíveis e inseridas uma etapa de limpeza para cada uma delas, além de uma etapa de saída para salvar um novo CSV com os dados limpos.

![image-20210907175616136](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210907175616136.png)



* Tabela de Estoque:

  * Tratamento de datas: Substituição do ponto por vazio e uso da / no lugar dos traços.

    * REPLACE(SPLIT([Data atualização],'-',2),".","") + "/" + SPLIT([Data atualização],'-',1) + "/" + SPLIT([Data atualização],'-',3)

  * Alteração do tipo de campo para data em formato americano

    

* Tabela pedidos:

  * Alteração do campo UF da entrega para a função de Estado/Província



* Tabela de Produtos:
  * Uso da função SPLIT para separar o ID do Nome da Categoria, que estavam junto por uso do traço.

    *  SPLIT( [categoria_produto], "-", 1) para o ID
    * SPLIT( [categoria_produto], "-", 2) para o Nome da Categoria 

  * Substituição dos _ por espaço vazio no Nome da Categoria

    * REGEXP_REPLACE([categoria_produto - Divisão 2],'_',' ')

  * Alteração do tipo de Preço para decimal

    

* Tabela de Veículos:

  * Renomeação do campo para ID Veiculo
  * Remoção de letras do ID
    * REGEXP_REPLACE([ID Veiculo], '[[:alpha:]]', '')
  * Alteração do tipo do campo para Inteiro



#### Passo 2: Relacionamentos



Foram criadas duas fontes de dados para o dashboard de logística.

A fonte <b>Pedidos</b> utiliza como base a tabela pedidos e se relaciona com as tabelas de produtos e veículos.

Os relacionamentos foram configurados da seguinte forma: 

![image-20210908142636876](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210908142636876.png)

Pedidos (N) <--- (1) Produtos

Pedidos (N) <--- (1) Veículos

Os relacionamentos são 1 para muitos partindo das tabelas de produtos e veículos, visto que são tabelas de dimensão e a tabela de Pedidos é a fato.



A fonte <b>Estoque</b> utiliza como base tabela estoque e se relaciona com a tabela de produtos apenas.

O relacionamento foi configurado da seguinte forma:



![image-20210908142957336](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210908142957336.png)

Estoque (N) <--- (1) Produtos

O relacionamento é 1 para muitos partindo da tabela de produtos, visto que a estoque é a fato e a produtos se trata da dimensão.



A <b>Dimensão Tempo</b> não foi criada, pois nesse dashboard a análise de estoque será fixa e não irá variar conforme filtros. Portanto, utilizarei o campo data de compra, da fonte Pedidos, como referência para as análises no dashboard.



#### Passo 3: O Dashboard

Você pode acessar o dashboard interativo completo clicando <b> [AQUI](https://public.tableau.com/views/Dashboard_AluraLog/PainelPrincipal?:language=pt-BR&publish=yes&:display_count=n&:origin=viz_share_link)</b>



<b>O painel principal</b> atende a todos os requisitos do projeto em uma única página, trazendo:

<ul>
    <li>Cards de disponibilidade de veículos</li>
    <li>Cards sobre os status das entregas</li>
    <li>Card do valor da receita</li>
    <li>Filtros de ano e mês</li>
    <li>Estoque médio por ano exibido em um gráfico de barras</li>
    <li>S2D Médio exibido em um gráfico de linhas</li>
    <li>Quantidade de pedidos realizados por estado exibido em um mapa</li>
</ul>



![image-20210908143721632](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210908143721632.png)





<b>O painel produtos</b> traz alguns outros insights interessantes. Os cards e filtros permanecem os mesmos, mas há o acréscimo de:

<ul>
    <li>Top 10 categorias mais vendidas exibidas em TreeMap</li>
    <li>Ticket Médio por Estado exibido em um mapa</li>
</ul>



![image-20210908143916567](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210908143916567.png)



<a name="contact"/>

### Contato

Sinta-se a vontade para me contatar:

[<img src="https://img.shields.io/badge/linkedin-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white" />](https://www.linkedin.com/in/alysterfernandes/)

[<img src="https://img.shields.io/badge/medium-%2312100E.svg?&style=for-the-badge&logo=medium&logoColor=white" />](https://medium.com/@alyster)