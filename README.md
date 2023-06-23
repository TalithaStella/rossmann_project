![](https://imgur.com/KRHa0qu.png)

## Atenção: 
- Os dados usados para esse projeto foram coletados na plataforma Kaggle. 
- O problema apresentado é fictício, criado com o intuito de aprender Ciência de Dados
- O projeto foi orientado pela Comunidade DS


> **Resumo**: Projeto de Ciência de dados utilizando Machine Learning - Regression (Time Series) para prever as vendas das próximas 6 semanas de uma loja. O projeto end-to-end > foi realizado com método CRISP-DM e passou pelas seguintes etapas: ETL, Estatistica Descritiva, Feature Engineering, Preparação dos dados 
> (Enconding, rescalling e transformação), Feature Selection, Treinamento de Machine Learning, Hiperparameter fine Tuning, Tradução e interpretação do erro, deploy do modelo em produção e criação de um BOT que mostra os resultados da previsão pelo Telegram.

### Índice
* [1. Problema de negócio](#1-problema-de-negócio)
* [2. Planejamento](#2-planejamento)
* [3. Premissas](#3-premissas)
* [4. Análise Exploratória dos Dados](#4-análise-exploratória-dos-dados)
* [5. Machine Learning](#5-machine-learning)
* [6. Resultado da Modelagem para o Negócio](#6-resultado-da-modelagem-para-o-negócio)
* [7. Entrega e Conclusão](#7-entrega-e-conclusão)
* [8. Próximos passos](#8-próximos-passos)

<hr>


## 1 Problema de negócio
Rossmann é uma das maiores redes de drogarias da Europa, com cerca de 56.200 funcionários e mais de 4.000 lojas. 

A Administração das lojas esta avaliando a necessidade de realizar uma reforma nas lojas, mas para saber se o orçamento seria viável o CFO pensou na possibilidade de realizar uma previsão das próximas 6 semanas de cada loja. Sendo assim após uma reunião com os gerentes de loja foi solicitado todos os dados de vendas para que o time de Ciência de dados pudesse realizar a previsão para as próximas 6 semanas. 

O modelo adotado para solução desde problema foi: Machine Learning Regression - Time Series. 
<br>

## 2. Planejamento

A construção do projeto foi realizada com a metodologia CRISP-DM, isto é, um método cíclico de solução de problemas de Ciência de Dados. O objetivo é entregar valor o mais rápido possível para o negócio e melhorar continuamente a solução. Para saber mais leia em [Medium](https://medium.com/comunidadeds/voc%C3%AA-tem-os-dados-tem-o-problema-de-neg%C3%B3cio-mas-e-agora-o-que-fazer-bf3b2d06482)

<div align="center">
<img src="https://imgur.com/BkJdxdi.png" />
</div>


**Principais Ferramentas utilizadas:** 
- Python (pandas, math, inflection, numpy, seaborn, matplotlib, tabulate), 
- Machine Learning (XGBoost, random, scipy, sklearn: Random Forest, LinearRegression, Lasso, boruta) 
- Estatística (Skleanin.metrics, RobustScaler, MinMaxScaler, LabelEncoder) 
- API (pickle, request, Flask, json)
- Render, Telegram, VSCode, Jupyter Notebook


## 3. Premissas 

A base de dados contém as seguintes colunas: 

<div align="center">
<img src="https://imgur.com/lQjw59J.png" />
</div>

### As premissas assumidas foram: 
- **sales**: Foi excluído os dias em que as lojas estiveram fechadas ou não tiveram vendas
- **competition_distance**: Quando ausente foi assumido que o competidos estava tão longe que não poderia impactar nas vendas. Sendo assim os valores faltantes foraam substituidos por um muito acima ao valor máximo. 
- **competition_open_since_month**: Quando ausente e se houver algum valor para na mesma linha de competition_distance foi utilizado a mesma data da venda, para poder ser utilizado na etapa de feature engineering
- **competition_open_since_year**: A mesma lógica acima foi aplicado para esta variável.
- **promo2_since_week**: Quando ausente foi considerado que a loja não estava participando de promoções
- **promo2_since_year**: Foi assumido a mesma lógica acima para esta variável.

<br>

## 4. Análise Exploratória dos Dados

Para conseguir visualizar os fatores que mais impactam neste modelo de negócio fiz um mapa mental para explorar as hipóteses viáveis de serem estudadas com as informações que temos disponíveis. 
#### Mapa mental de Hipóteses: 
<div align="center">
<img src="https://imgur.com/DakPAEU.png" />
</div><br>

Desta forma foi possível analisar 11 hipóteses, dentre elas obtivemos os seguintes insights:  

1. Lojas com maior sortimentos deveriam vender mais.
- Falsa: Lojas com MAIOR SORTIMENTO vendem MENOS.
<div align="center">
<img src="https://imgur.com/98zh5qx.png" />
</div><br>

2. Lojas com competidores mais próximos deveriam vender menos.
- Falsa: Lojas com COMPETIDORES MAIS PROXIMOS vendem MAIS.
<div align="center">
<img src="https://imgur.com/GZUbLNX.png" />
</div><br>

3. Lojas abertas durante o feriado de Natal (azul) deveriam vender mais.
- Falsa: Lojas abertas durante o feriado do Natal vendem menos.
<div align="center">
<img src="https://imgur.com/QaJtMYA.png" />
</div><br>

4. Lojas deveriam vender mais depois do dia 10 de cada mês.
- Verdadeira: Lojas vendem mais depois do dia 10 de cada mes.
<div align="center">
<img src="https://imgur.com/X0FGWOi.png" />
</div><br>

## 5. Machine Learning

Para realizar a regressão foi utilizado 5 algoritmos diferentes: Average Model, Regressão Linear, Lasso, Random Forest e XGBoost. Sendo um problema de TimeSeries também foi utilizada a técnica de validação cruzada TimeSeries Split para separar os dados de treinamento e os dados de validação dos modelos (80% e 20%, respectivamente). 

> Observações: 
>
> MAE → A soma da diferença entre o valor real e o valor da predição, dividido pelo numero de valor predito (média do error).
> 
> MAPE → É a porcentagem de error do MAE. Mostra o quão longe a predição está do valor real, na média, em porcentagem.
> 
> RMSE (Root Mean Square Error)→ Muito usado para calcular a performance do algoritmo Machine Learning, pois atribui peso, ou seja, atribui maior peso a erros maiores. Sendo rígido aos outliers.

### Avaliação da performance antes do cross-Validation

<div align="center">
<img src="https://imgur.com/kQJBuSU.png" />
</div><br>

### Avaliação da performance após o cross-Validation

<div align="center">
<img src="https://imgur.com/RWIJLW0.png" />
</div><br>

Apesar de não ter apresentado os melhores parâmetros durante essa primeira fase de teste, decidi seguir com o algoritmo XGBoost para fazer a previsão de vendas dos dados. O motivo é devido a capacidade de armazenamento necessária ser muito menor, ser mais rápido para treinar e ajustar comparado ao Random Forest, acarretando um custo mais vantajoso para empresa mantê-lo em funcionamento. De qualquer forma, podemos esperar uma performance melhor depois da etapa de Fine Tunning.


### Final Performance - Hyperparameter Fine Tunning
<div align="center">
<img src="https://imgur.com/lqpYN36.png" />
</div><br>
<br>

## 6. Resultado da Modelagem para o Negócio

Na tabela abaixo podemos ver uma amostra da resposta de predição do nosso modelo. Em 'predictions' mostra o valor absoluto esperado para cada loja. As colunas de "worst_scenario" e "best_scenario" dão uma ideia do valor a esperar se as vendas forem no pior ou melhor cenário, respectivamente. Para facilitar a interpretação, a coluna MAE mostra essa variação (margem de erro) em valor absoluto, e o MAPE representa a mesma variação em porcentagem. 
Sendo assim, a loja 316 tem o valor de 364.869,28 R$ previsto. No pior cenário esta loja fará 364.134,50 R$ e no melhor 365.584,07 R$. Essa margem de erro representa 724,79 R$ ou 7%. 

Predições totais, resultado no pior cenário, melhor cenário, variação em erro absoluto, variação em porcentagem:
<div align="center">
<img src="https://imgur.com/7GzO65n.png" />
</div><br>

Cenários considerando valor total de todas as lojas (soma total):
<div align="center">
<img src="https://imgur.com/IGyu4oY.png" />
</div><br>


Para visualizar melhor qual é a variação geral dos erros das lojas plotei o gráfico abaixo. É importante observar que a maioria das lojas apresentaram menos de 20% de erro, mas algumas chegaram a mais de 50%. Isso mostra que no próximo ciclo do CRISP-DM temos que avaliar possíveis mudanças para essas lojas mais complexas.

Variação dos erros das diferentes lojas:
<div align="center">
<img src="https://imgur.com/elMk4Fc.png" />
</div><br>

Os gráficos abaixo mostram a performance do modelo. No gráfico 1 o valor de predição, ao ser comparado com o valor real das vendas, se mostra vem fiel ao que realmente foi. O gráfico 2 mostra se o modelo subestimou ou superestimou o valor das vendas. Os gráficos 3 e 4 são para fins estatísticos de Machine Learning, e mostram que os erros estão satisfatório. 

Avaliação da performance do modelo em comparação com os valores reais:
<div align="center">
<img src="https://imgur.com/UObCKkt.png" />
</div><br>

<br>

## 7. Entrega e Conclusão

O modelo foi implementado em produção em Cloud por meio da plataforma Render. A fim de facilitar o acesso dessas informações de maneira prática, criei um BOT no Telegram que, ao solicitar o número da loja desejada, o BOT retorna o valor de previsão das próximas 6 semanas. 
A imagem abaixo mostra como o modelo funciona em relação à interação entre a Cloud, o projeto e o BOT Telegram.

<div align="center">
<img src="https://imgur.com/U8y0dxU.png" />
</div><br>

Vídeo do BOT Telegram: 

<div align="center">
<img src="#" />
</div><br>


**Como funciona:** é necessário entrar somente um valor de loja após uma barra, por exemplo: /22.
Se a loja não existir, ou der entrada em caracteres não reconhecido o BOT retorna avisando o equivoco.

Concluindo, acredito que o modelo apresentou resultados satisfatórios em produção para o primeiro ciclo do CRISP-DM, é evidente a necessidade de melhorias para os próximos ciclos, contudo, com essa performance inicial seria possível se planejar para a reforma proposta pelo CFO.
<br>

## 8. Próximos passos

Se tratando de um projeto realizado pelo método CRISP-DM, algumas considerações são importantes para o próximo ciclo: 

- Reavaliar/Criar algumas features a fim de melhorar a acurácia do modelo.
- Trabalhar com NA de maneira diferente
- Avaliar se outro modelo apresenta performance melhor que o XGBoost depois do Fine Tunning
- Testar algum modelo mais complexo para ver se diminui a dificuldade de previsão de algumas lojas (erro > 50%)


