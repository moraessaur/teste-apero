---
title: "Data viz: eixo y duplo!"
excerpt: |
  Post de leitura rápida, mostrando como construir um plot com eixo y duplo, com escalas diferentes.
author:
  - name: Lucas Moraes.
    url: https://lucasmoraes.org
date: 01-12-2021
categories:
  - Data viz
  - ggplot
  - Leitura rápida
output:
  distill::distill_article:
    toc: true
    self_contained: false
---
<link href="{{< blogdown/postref >}}index_files/pagedtable/css/pagedtable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/pagedtable/js/pagedtable.js"></script>
<link href="{{< blogdown/postref >}}index_files/pagedtable/css/pagedtable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/pagedtable/js/pagedtable.js"></script>
<link href="{{< blogdown/postref >}}index_files/pagedtable/css/pagedtable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/pagedtable/js/pagedtable.js"></script>
<link href="{{< blogdown/postref >}}index_files/pagedtable/css/pagedtable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/pagedtable/js/pagedtable.js"></script>

Esse vai ser um post bem rápido de data viz, explicando como construir um gráfico com dois eixos y, que tem escalas/unidades distintas.

Em geral não é uma boa ideia construir gráficos desse tipo, mas para alguns casos eles podem ser úteis.

Aqui, uso dados de desmatamento do Prodes e da precipitação média anual, do BDMEP, para o município de Porto Velho (AM). Essa escolha foi arbitrária e o objetivo aqui não é analisar os dados, apenas plotar!

# Os dados

Extraí os dados usando a [Base dos Dados](https://basedosdados.org/dataset/67aac97e-2f13-4def-ae73-779a1bc0a723) (especificamente [daqui](https://basedosdados.org/dataset/b9528c5f-3b31-4383-9e60-51e34e6b9237) e [daqui](https://basedosdados.org/dataset/67aac97e-2f13-4def-ae73-779a1bc0a723)). A query que construí me gerou duas tabelas.

A primeira, contém a área desmatada no município, por ano:





```r
df_desmatado <- 
  read_csv("https://www.dropbox.com/s/qq9at6hsdyctlpo/df_desmatado.csv?dl=1")

rmarkdown::paged_table(df_desmatado)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["ano"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["desmatado"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"2000","2":"3888.3"},{"1":"2001","2":"4220.8"},{"1":"2002","2":"4633.8"},{"1":"2003","2":"5124.8"},{"1":"2004","2":"5885.7"},{"1":"2005","2":"6545.6"},{"1":"2006","2":"6908.5"},{"1":"2007","2":"7341.4"},{"1":"2008","2":"7555.7"},{"1":"2009","2":"7662.0"},{"1":"2010","2":"7797.0"},{"1":"2011","2":"8121.8"},{"1":"2012","2":"8317.6"},{"1":"2013","2":"8633.3"},{"1":"2014","2":"8858.2"},{"1":"2015","2":"9147.4"},{"1":"2016","2":"9456.7"},{"1":"2017","2":"9810.2"},{"1":"2018","2":"10198.9"},{"1":"2019","2":"10617.9"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

A segunda contém a precipitação média anual, para o município:


```r
df_precip_anual <- 
  read_csv("https://www.dropbox.com/s/t635jy650z0i72c/df_mean_precip_anual.csv?dl=1")

rmarkdown::paged_table(df_precip_anual)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["ano"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["precipitacao_media"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"2021","2":"NA"},{"1":"2017","2":"0.2908815"},{"1":"2012","2":"0.2840034"},{"1":"2013","2":"0.2556806"},{"1":"2009","2":"0.3039787"},{"1":"2011","2":"0.3610043"},{"1":"2018","2":"0.2929955"},{"1":"2007","2":"0.1841705"},{"1":"2008","2":"0.2597827"},{"1":"2010","2":"0.1603064"},{"1":"2015","2":"0.2501523"},{"1":"2016","2":"0.2529367"},{"1":"2014","2":"0.2643266"},{"1":"2019","2":"0.1986045"},{"1":"2020","2":"NA"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

Para começar a trabalhar na plotagem, fiz um join destas duas tabelas, já aproveitando para converter a coluna ano para fator:


```r
tabela <- 
  left_join(df_precip_anual,df_desmatado) %>% 
  mutate(ano=as.factor(ano)) %>% 
  na.omit()
```

```
## Joining, by = "ano"
```

```r
rmarkdown::paged_table(tabela)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["ano"],"name":[1],"type":["fct"],"align":["left"]},{"label":["precipitacao_media"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["desmatado"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"2017","2":"0.2908815","3":"9810.2"},{"1":"2012","2":"0.2840034","3":"8317.6"},{"1":"2013","2":"0.2556806","3":"8633.3"},{"1":"2009","2":"0.3039787","3":"7662.0"},{"1":"2011","2":"0.3610043","3":"8121.8"},{"1":"2018","2":"0.2929955","3":"10198.9"},{"1":"2007","2":"0.1841705","3":"7341.4"},{"1":"2008","2":"0.2597827","3":"7555.7"},{"1":"2010","2":"0.1603064","3":"7797.0"},{"1":"2015","2":"0.2501523","3":"9147.4"},{"1":"2016","2":"0.2529367","3":"9456.7"},{"1":"2014","2":"0.2643266","3":"8858.2"},{"1":"2019","2":"0.1986045","3":"10617.9"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

Nesta tabela existem as duas colunas, com escalas de valores e unidades diferentes.

Eu poderia simplesmente plotar ambas no mesmo gráfico, ignorando essa questão:


```r
tabela %>% # chamando a tabela
  ggplot(aes(x=ano,y=desmatado)) + # referenciando as variáveis
  geom_col(fill='#8492e3',color="black") + # barplot de valores de desmatamento
  geom_line(data=tabela, color='black',lty=2, size=1, # line plot dos valores de precipitação
            aes(x=ano,y=precipitacao_media,group=1), inherit.aes = FALSE) +
  geom_point(data=tabela, color='black', size = 2, # point plot dos valores de precipitação
            aes(x=ano,y=precipitacao_media,group=1), inherit.aes = FALSE) +
  ylab("Área desmatada (ha.)") +
  xlab('Ano') +
  theme_bw() # tema
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

Entretanto, temos dois problemas aqui: primeiro, praticamente não é possível visualizar a linha, que corresponde aos valores de precipitação. Isso acontece por causa da escala dos valores de desmatamento, que são maiores e norteiam o eixo y. Segundo, temos apenas um eixo y, referente ao desmatamento. Ainda, existe um terceiro problema não correlato com a análise, que é o fato do Brasil estar perdendo, há anos, a guerra contra o desmatamento da amazônia, o nosso maior bem.

O segredo para resolver os dois primeiros problemas está nos argumentos da função `scale_y_continuous`, especificamente, no argumento `sec.axis`. 

Este argumento duplica o eixo y:


```r
tabela %>% 
  ggplot(aes(x=ano,y=desmatado)) +
  geom_col(fill='#8492e3',color="black") +
  geom_line(data=tabela, color='black',lty=2, size=1,
            aes(x=ano,y=precipitacao_media,group=1), inherit.aes = FALSE) +
  geom_point(data=tabela, color='black', size = 2,
            aes(x=ano,y=precipitacao_media,group=1), inherit.aes = FALSE) +
  scale_y_continuous(sec.axis = sec_axis(~ .,name = "Precipitação média (mm)")) + # duplicando o eixo y e mudando seu nome
  ylab("Área desmatada (ha.)") +
  xlab('Ano') +
  theme_bw() 
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Embora tenhamos dois eixos y agora, ainda temos o problema da escala e dos valores, pois ele está literalmente duplicado (apenas o título foi alterado). Este problema pode ser resolvido multiplicando os valores da coluna de precipitação, de uma forma que permita que esta escale com a coluna do desmatado. Nesse caso, vou multiplicar todos valores de precipitação por 10.000, isso vai "puxar" os valores para cima no gráfico:


```r
tabela %>% 
  ggplot(aes(x=ano,y=desmatado)) +
  geom_col(fill='#8492e3',color="black") +
  geom_line(data=tabela, color='black',lty=2, size=1,
            aes(x=ano,y=precipitacao_media*10^4.5,group=1), inherit.aes = FALSE) + # multiplicando os valores de y
  geom_point(data=tabela, color='black', size = 2,
            aes(x=ano,y=precipitacao_media*10^4.5,group=1), inherit.aes = FALSE) + # multiplicando os valores de y
  scale_y_continuous(sec.axis = sec_axis(~ .,name = "Precipitação média (mm)")) +
   ylab("Área desmatada (ha.)") +
  xlab('Ano') +
  theme_bw() 
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />
<br>
Agora conseguimos enxergar os valores de precipitação de maneira mais clara. Como foram todos multiplicados pelo mesmo número, a escala deles não se alterou.

Por último, preciso igualar a escala do eixo duplicado, uma vez que os valores que constam no gráfico, são aqueles referentes ao desmatamento.

Para fazer isso simplesmente divido pelo **mesmo número** que usei para aumentar a escala dos valores (10.000), mas faço isso **dentro do argumento `sec.axis`**. No código abaixo, apenas inseri a expressão `* 10^-4.5` ao lado da duplicação do eixo:


```r
tabela %>% 
  ggplot(aes(x=ano,y=desmatado)) +
  geom_col(fill='#8492e3',color="black") +
  geom_line(data=tabela, color='black',lty=2, size=1,
            aes(x=ano,y=precipitacao_media*10^4.5,group=1), inherit.aes = FALSE) +
  geom_point(data=tabela, color='black', size = 2,
            aes(x=ano,y=precipitacao_media*10^4.5,group=1), inherit.aes = FALSE) +
  scale_y_continuous(sec.axis = sec_axis(~ . * 10^-4.5,name = "Precipitação média (mm)")) + # dividindo eixo
   ylab("Área desmatada (ha.)") +
  xlab('Ano') +
  theme_bw() 
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

Essa operação vai ajustar as transformações dos valores de precipitação com o valor da escala duplicada, consertando a escala!

Podemos chamar a tabela de novo apenas para ter certeza que os números fazem sentido:


```r
rmarkdown::paged_table(tabela)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["ano"],"name":[1],"type":["fct"],"align":["left"]},{"label":["precipitacao_media"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["desmatado"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"2017","2":"0.2908815","3":"9810.2"},{"1":"2012","2":"0.2840034","3":"8317.6"},{"1":"2013","2":"0.2556806","3":"8633.3"},{"1":"2009","2":"0.3039787","3":"7662.0"},{"1":"2011","2":"0.3610043","3":"8121.8"},{"1":"2018","2":"0.2929955","3":"10198.9"},{"1":"2007","2":"0.1841705","3":"7341.4"},{"1":"2008","2":"0.2597827","3":"7555.7"},{"1":"2010","2":"0.1603064","3":"7797.0"},{"1":"2015","2":"0.2501523","3":"9147.4"},{"1":"2016","2":"0.2529367","3":"9456.7"},{"1":"2014","2":"0.2643266","3":"8858.2"},{"1":"2019","2":"0.1986045","3":"10617.9"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

E é isso! O código que gera esses dados pode ser acessado no repo desse site, mas é mais fácil chegar nele [por esse repo aqui](https://github.com/moraessaur/double_axis_plots), que gera apenas o que foi analisado acima :) Nele, também estão contidas as queries de SQL utilizadas para extrair os dados da Base dos Dados.



