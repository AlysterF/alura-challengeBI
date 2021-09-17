# Desafio 2: AluraShop Dashboard de E-Commerce 🛍️

[Acessar o Dashboard](https://datastudio.google.com/reporting/a41b003f-1ea3-49e0-ac19-3c8e4d0eafd3)

<br>

### Índice

[Estudo de caso](#businesscase)

[Base de dados](#database)

[Solução](#solution)

[Contato](#contact)


<br>
<a name="businesscase"/>

### Estudo de caso

Agora a Alura Shop **investiu** em publicidade para se destacar no mercado, e a gerência da empresa tem dúvidas se o retorno dessa propaganda **surtiu efeito**.

A nossa missão é apoiar a gerência em suas **tomadas de decisão**, e elucidar as dúvidas. Para isso desenvolveremos um dashboard  estratégico de marketing com o objetivo de monitorar uma campanha de  publicidade paga durante o mês de julho de 2021. Apresentaremos **indicadores relevantes** para a validação estratégica do negócio.


<br>

### Requisitos

<ul>
    <li>Total de compras</li>
    <li>Total de valor convertido em compras</li>
    <li>Total de valor investido na campanha</li>
    <li>Calcular o custo por clique</li>
    <li>Exibir a jornada de compra</li>
    <li>Calcular a taxa de conversão</li>
    <li>Mostrar o ticket médio por dispositivo</li>
    <li>Mostrar o retorno do investimento em publicidade (ROAS) por idade e gênero</li>
    <li>Calcular o valor convertido em compras por dia</li>
    <li>Configurar o relatório para que atualize todo dia útil as 9 horas da manhã</li>
    <li>Exibir data e hora da atualização</li>
</ul>




<br>
<a name="database"/>

### Base de Dados

Foram disponibilizadas duas bases de dados em JSON para esse desafio, sendo elas:

<ul>
    <li>Tabelas Dispositivos</li>
    <li>Tabela Idade e Gênero</li>
</ul>






A <b>Tabela Dispositivos</b apresenta informações de acesso por dispositivo e possui:

<ul>
    <li>Dia</li>
   	<li>Plataforma Do Dispositivo</li>
    <li>Colocação</li>
    <li>Plataforma</li>
    <li>Alcance</li>
    <li>Impressões</li>
    <li>Quantia Gasta (Brl)</li>
    <li>Cliques Em Links</li>
    <li>Visualizações Por Página</li>
    <li>Compras No Website</li>
    <li>Compras No Facebook</li>
    <li>Adicionados Ao Carrinho</li>
    <li>Valor De Conversão</li>
    <li>Adicionado Ao Carrinho</li>
    <li>Checkouts Iniciados</li>
    <li>Valor De Conversão De Checkouts Iniciados</li>
    <li>Valor De Conversão De Compras</li>
    <li>Compras</li>
</ul>






A <b>Tabela Idade e Gênero</b> apresenta informações de pessoas que acessaram a página e realizaram ou não compras e possui:

<ul>
    <li>Idade</li>
	<li>Gênero</li>
    <li>Dia</li>
    <li>Field4</li>
    <li>Alcance</li>
    <li>Impressões</li>
    <li>Quantia Gasta (Brl)</li>
    <li>Cliques No Link</li>
    <li>Visualizações Por Página</li>
    <li>Compras No Website</li>
    <li>Compras No Facebook</li>
    <li>Adicionados Ao Carrinho</li>
    <li>Valor De Conversão</li>
    <li>Adicionado Ao Carrinho</li>
    <li>Checkouts Iniciados</li>
    <li>Valor De Conversão De Checkouts Iniciados</li>
    <li>Valor De Conversão De Compras</li>
    <li>Compras</li>
</ul>






### 

<a name="solution"/>

<br>

### Solução

<br>

Utilizei o Google Data Studio para realizar esse desafio, em conjunto com o Google Spreadsheets e Google Script.



#### Passo 1: Tratamento dos dados

As bases de dados foram disponibilizadas no formato JSON, como o Data Studio não disponibiliza um conector nativo e gratuito para JSON, fiz a importação dos arquivos diretamente em uma planilha do Spreadsheets.

O spreadsheets também não se conecta nativamente com arquivos JSON. Em uma breve pesquisa sobre a possibilidade dessa conexão, encontrei um artigo do Paul Gambill que passou pelo menos desafio em uma empresa. Nesse artigo, Paul menciona um script disponível no blog Fast Fedora, que gera uma nova fórmula no Google Spreadsheets para importar JSON a partir de um link.

Você pode ler o artigo [aqui](https://medium.com/@paulgambill/how-to-import-json-data-into-google-spreadsheets-in-less-than-5-minutes-a3fede1a014a) e mais sobre o projeto ImportJSON [aqui](https://blog.fastfedora.com/projects/import-json).

O script gera uma fórmula muito simples de ser utilizada:

````
ImportJSON(url; query; options)
````

Onde <b>URL</b> é o link de origem do arquivo JSON,  <b>query</b> é uma série de comandos caso você queira trazer os dados com alguma manipulação e <b>option</b> realiza algumas funções extras como a <i>noInherit</i> que desativa a herança de dados de hierarquias superiores e <i>noTruncate</i> para garantir que ser dados sejam exibidos por completo.



Após essa importação, conectei ambas tabelas no Data Studio

![image-20210917171648038](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210917171648038.png)



Na Tabela Dispositivos fiz alterações nos tipos de dados:

<ul>
    <li>Colocação: TEXTO</li>
    <li>Dia: DATA</li>
    <li>Plataforma: TEXTO</li>
    <li>Plataforma do Dispositivo: TEXTO</li>
    <li>Quantia Gasta (Brl): MOEDA BRL</li>
    <li>Os demais campos foram alterados para NÚMERO</li>
</ul>

Nessa tabela, percebi que havia redundância nos dados em algumas linhas. Haviam linhas em que a Plataforma do Dispositivo, a Colocação ou a Plataforma se apresentavam como All e traziam a total (soma) dos valores de outras linhas. Apliquei um <b>filtro</b> na coluna Plataforma, que é o nível mais baixo da hierarquia Plataforma do Dispositivo > Colocação > Plataforma. O filtro foi aplicado para remover as linhas que continham o texto All.



Na Tabela Idade e Gênero também houveram alterações nos tipos de dados:

<ul>
    <li>Dia: DATA</li>
    <li>Gênero: TEXTO</li>
    <li>Idade: TEXTO</li>
    <li>Quantia gasta (Brl): MOEDA BRL</li>
    <li>Os demais campos foram alterados para NÚMERO</li>
</ul>



#### Passo 2: Campos calculados e métricas

Para alcançar os requisitos do projeto, utilizei alguns campos calculados e agregações:



<ul>
    <li><b>Total de compras</b></li>
    Agregração de soma: SUM(compras)
    <li><b>Total de valor convertido em compras</b></li>
	Agregação de soma: SUM(Valor De Conversão De Compras)
    <li><b>Total de valor investido na campanha</b></li>
    Agregação de soma: SUM(Quantia Gasta (Brl))
    <li><b>Calcular o custo por clique</b></li>
    Campo calculado: SUM(Quantia Gasta (Brl))/SUM(Cliques Em Links))
    <li><b>Exibir a jornada de compra</b></li>
    Para exibir esses dados, foram utilizadas a agregação de soma para os campos Visualizações por Página, Adicionados ao Carrinho, Checkouts Iniciados e Compras.
    <li><b>Calcular a taxa de conversão</b></li>
    Campos calculado: SUM(Compras)/SUM(Cliques Em Links)
    <li><b>Mostrar o ticket médio por dispositivo</b></li>
    Campo calculado: SUM(Valor De Conversão De Compras)/SUM(Compras)
    <li><b>Mostrar o retorno do investimento em publicidade (ROAS) por idade e gênero</b></li>
    Campo calculado: SUM(Valor De Conversão De Compras)/SUM(Quantia Gasta (Brl))
    <li><b>Calcular o valor convertido em compras por dia</b></li>
    Agregação de soma: SUM(Valor De Conversão De Compras)
    <li><b>Configurar o relatório para que atualize todo dia útil as 9 horas da manhã</b></li>
    O Data Studio não permite um agendamento muito customizado para update dos dados, mas apresenta opções de atualização em períodos de 15 minutos, 4 horas ou 12 horas. Selecionei a opção para atualizar a cada 4 horas.
    <li><b>Exibir data e hora da atualização</b></li>
    Campo calculado: CURRENT_DATETIME("America/Sao_Paulo")
    O campo calculado retorna a data e hora atual no fuso horário de SP. Essa informação é atualizada cada vez que os dados também são atualizados, exibindo assim, a informação corretamente.
</ul>



#### Passo 3: O Dashboard

Você pode acessar o dashboard interativo completo clicando <b> [AQUI](https://datastudio.google.com/reporting/a41b003f-1ea3-49e0-ac19-3c8e4d0eafd3)</b>



<b>O painel Início</b> traz os cards abaixo:

<ul>
    <li>Compras</li>
    <li>Conversão em Compras</li>
    <li>Valor Investido</li>
    <li>Custo por Clique</li>
    <li>% de Conversão</li>
</ul>

Além de trazer um gráfico de funil para apresentar a jornada de compras, um gráfico de barras para exibir o ticket médio por dispositivo e, para uma métrica extra, um gráfico de linhas suavizado para exibir o alcance por dia por plataforma.

Nesse painel, vale ressaltar que o Data Studio não fornece um gráfico de funil nativo, portanto, utilizei um recurso de visualização gratuito disponível na comunidade do Data Studio: Funnel Chart criado por Ayima.



![image-20210917182614088](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210917182614088.png)



Já o <b>painel Clientes</b> traz os mesmos cards do painel anterior, mas algumas métricas mais voltadas ao perfil dos clientes da plataforma, sendo elas um gráfico de barras empilhadas para exibir o ROAS por idade e gênero, um gráfico de rosca para exibir a quantidade de compras por gênero e um gráfico de linhas/séries temporais para exibir o valor de conversão de compras por dia.

Eu tive dificuldade em remover do gráfico de barras empilhadas os campos do eixo X em que o valor do ROAS era 0/nulo. Criei um filtro para ser aplicado no visual, considerando excluir o ROAS que fosse 0 ou nulo. O filtro funcionou no gráfico de barras apenas com a dimensão idade, mas ao adicionar a dimensão gênero como uma dimensão detalhada, o filtro deixa de funcionar e o eixo é exibido por completo. Por isso, ainda é possível visualizar os campos de idade 25 e 65+, mesmo que não tenham valor algum.



![image-20210917182718831](C:\Users\alyst\AppData\Roaming\Typora\typora-user-images\image-20210917182718831.png)

<br>

<a name="contact"/>

### Contato

Sinta-se a vontade para me contatar:

[<img src="https://img.shields.io/badge/linkedin-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white" />](https://www.linkedin.com/in/alysterfernandes/)

[<img src="https://img.shields.io/badge/medium-%2312100E.svg?&style=for-the-badge&logo=medium&logoColor=white" />](https://medium.com/@alyster)
