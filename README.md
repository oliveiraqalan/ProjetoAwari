README

O trabalho será apresentado seguindo a estrutura pela qual o projeto foi desenvolvido

1. Business Issue Understading

A) Para quem estou trabalhando?
R: Negócio de distribuição que opera com diferentes categorias de produtos e possui uma base de mais de 1500 clientes

B) O que essa pessoa precisa?
R: Conhecer melhor os diferentes perfis e hábitos de compras de sua base de clientes

C) O que é importante?
R: Identificar e agrupar os clientes com base no comportamento individual de cada um deles

D) Por que isso é importante?
R: Saber como os clientes se relacionam com o negócio ajuda a empresa a traçar objetivos específicos definir ações mais focadas e assertivas para cada grupo de cliente

E) Como isso gera valor?
R: Melhoria do engajamento dos clientes e incremento de receita

2. Data Understading

O dataset utilizado no projeto encontra-se no Kaggle e está disponível no diretório do projeto no arquivo 'superstore_dataset.csv';
O primeiro passo foi entender um pouco a estrutura do dataset, checando número de registros, quantidade de colunas e data type de cada coluna

3. Data Transformation and Cleasing

3.1 Fase I

Foi feita a transformação do data type de duas colunas de datas, 'Order Date' e 'Ship Date', de int64 para datetime64;
Foi checado se havia algum valor nulo nos registros, descobriu-se que a coluna 'Postal Code' tinha aproximadamente 80% dos registros com valores nulos, e dessa forma foi excluída, uma vez que não teria impacto direto no objetivo do projeto e tampouco possuia uma quantidade significativa de dados;
Não foram removidos valores outliers, dado que são informações relevantes de comportamento de compra de clientes, e podem aprensetar informações de sazonalidade e/ou ciclicidade no horizonte de tempo estudado

3.2 Fase II

Para realizar a clusterização foi escolhida a matrix RFV como variáveis de segmentação de clientes. Esse método avalia e pontua 3 indicadores para cada um dos clientes, sendo o primeiro deles a Recência (última compra realizada), Frequência (quantidade de pedidos num dado período) e Valor (total gasto pelo cliente no período), atribuindo assim, notas de 1 a 5 para cada indicador de cada cliente.
Assim, foi construído um novo dataset chamado de 'customers' somente com as colunas que seriam utilizadas, dessa forma foram mantidas as colunas 'Customer ID' e 'Value' do antigo dataset, e incorporadas as colunas 'Recency' e 'Frequency', por meio de uma manipulação simples

4. Data Exploration I

Com as primeiras análises podemos observar:
Sales - apresentava uma distribuição enviesada para direita, com boa parcela dos clientes (aprox 42%) com gasto de até 5000, um gasto máximo de 35668, e uma mediana de 7951;
Recency - distribuição também assimétrica para direita, com mais de 1200/1590 clientes com uma recência de até 100 dias, uma mediana de 87 dias e uma pequena parcelas de clientes que realizaram suas últimas compras há mais de 1000 dias;
Frequency - possui uma amplitude de 40, ou seja, na base temos clientes que realizaram de 1 a 41 compras no período

5. Data Modelling

Por se tratar de um problema de clusterização, que demanda a aplicação de uma abordagem de aprendizagem não supervisionada foi escolhido o algoritmo K-Means (biblioteca scikit-learn) para construção do modelo, que é utilizado para encontrar similaridades entre os dados não rotulados, de acordo com as variáveis pre-estabelecidas, e agrupá-los conforme número de clusters passado pelo argumento 'k'.
Após a escolha do algoritmo foi utilizado o 'Método Elbow' para achar o número de cluster 'k' que melhor se aplica aos dados, todavia, uma etapa anterior precisou ser executada, a de normalização dos dados, para trazer os valores das colunas dos indicadores RFV a uma escala comum, sem distorcer as diferenças nos intervalos de valores, para isso foi aplicado o 'StandardScaler'.
Foi feita uma comparação do gráfico do Método Elbow antes e depois da etapa de normalização, porém o resultado, apesar das nuances, foi muito próximo, trazendo um 'k' = 3, que foi aplicado ao parâmentro do modelo

6. Data Exploration II

Após executar o modelo tivemos como output a seguinte divisão dos clientes nos grupos: 49%-43%-8%;
Numa análise rápida percebe-se que, de forma geral, o 1º Cluster (Cluster 0) agrupa os clientes com melhores resultados em cada um dos indicadores, seguido pelo 2º Cluster (Cluster 1) e 3º Cluster (Cluter 2)

7. Model Validation

Para validação do modelo foram utilizados dois métodos, o primeiro foi o 'Silhouette Score', trazendo k=3 e k=2 com scores mais altos, com 0.59 e 0.58, respectivamente.

O segundo método foi o benchmark 'RFV Scores', que basicamente foi construído com base nos valores de cada um dos indicadores usando como base toda a população de clientes. Para cada indicador os dados foram fracionados em 5 intervalos iniciando pelo 20º percentil, dessa forma teríamos como avaliar de 1 a 5 o resultado de cada cliente em cada um dos três indicadores, o resultado final traria uma pontuação para cada cliente, exemplo: um determinado cliente com RFV Score de R5-F5-V5 indicaria que o mesmo fez um pedido recentemente, realizou uma quantidade de compras significativa e possui um desembolso total acima da média. Feita essa transformação dos valores originais para uma escala de 1 a 5 o próximo passo foi, assim como anteriormente, realizar a normalização dos dados e comparar, na sequência, o shape da curva do 'Método Elbow' da Matriz RFV Scores com o shape da curva do 'Método Elbow' aplicado na etapa 5 (modelagem).
A conclusão foi de que o melhor número de clusters ficava entre 2 e 3, não mais do que isso. Decidiu-se seguir com k=3 para efeitos de análise de resultados (etapa subsequente) após identificar que quando k centróides era igual a 3 conseguíamos mapear uma região de menor densidade de dados no dataset, aumentando assim o número de agrupamentos com características semelhantes, ainda que um deles tenha uma quantidade de clientes bem inferior aos demais. Para ilustrar isso foram utilizados os gráficos interativos 'stripplot' para analisar volume de dados de cada indicador para cada um dos clusters, 'scatterplot' para analisar posição dos centróides nas regiões dos clusters e, por fim, 'pca' para análise multivariada com número de componentes igual a 2 e 3, que retornavam o mesmo resultado.

8. Conclusion

Para finalizar foi aplicado um gráfico de linha para representar as grandes diferenças entre os clusters para cada indicador, essa parte teve uma inspiração em um projeto publicado no Medium e que está referenciado no notebook deste projeto.
Os grandes insights foram:
##Cluster 0 - Representa a maior parcela da base de clientes, possui a menor recência, com a maior frequência de compras, é o grupo de maior representatividade em geração de negócios (receitas) e melhor ticket médio;
##Cluster 1 - É um grupo de clientes que não compra há algum tempo, possui uma menor recorrência de compras, porém o segundo melhor ticket médio ainda que distante das cifras do cluster 1, é um cluster que precisa ser reativado pela empresa;
##Cluster 2 - Representa a menor parcela da base de clientes, com os piores números de engajamento em todos os KPIs, provavelmente são clientes que deixaram ou estão prestes a deixar de fazer negócios com a empresa

9. Reflexões e próximos passos

A partir do modelo criado conseguimos identificar 3 grupos distintos com base em 3 métricas de negócio muito utilizadas para acompanhar a performance de vendas e relacionamento com clientes. Em suma, foi combinado conhecimentos de gestão de vendas à resolução do problema para, não somente identificar esses agrupamentos, mas também como uma forma de suportar uma escolha mais assertiva de ações a serem endereçadas a cada um deles. Uma vez que conseguimos conhecer melhor nossa base de clientes (comportamento/perfil de compra de cada cluster)
é possível realizar um estudo para tentar prever a demanda da empresa para um determinado período, das partes (clusters) para o todo (empresa). Assim encerramos o projeto, com uma entrega que habilita uma próxima fase que pode gerar ainda mais valor para à direção da empresa, numa perspectiva mercadológica.
