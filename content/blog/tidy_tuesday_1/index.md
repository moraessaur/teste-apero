---
title: "Data viz: custo de malhas urbanas"
excerpt: |
  Passo-a-passo da customização de um gráfico no ggplot2.
author:
  - name: Lucas Moraes. 
    url: https://lucasmoraes.org
date: 01-05-2021
categories:
  - Data viz
  - ggplot2
  - Tidy tuesday
---




Neste post, vou usar dados do Tidy Tuesday referente à semana de 05/01/2021 para demonstrar o passo-a-passo da criação de um plot. O objetivo desse post é apenas demonstrar algumas ferramentas de formatação do `ggplot2` e de alguns pacotes acessórios para customizar um gráfico, sem nenhum tipo de objetivo relacionado à análise de dados ou estatística.

A partir dos dados da tabela, vou mostrar como cheguei no gráfico abaixo:


```
## 
## 	Downloading file 1 of 1: `transit_cost.csv`
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-1-1.png" width="672" />

# Formatação dos dados

Os dados utilizados para gerar esse gráfico se referem à custos referentes à projetos de infraestrutura de trânsito. A tabela contendo os dados pode ser lida diretamente usando a função `tt_load` do pacote `tidytuesday`, incluindo como input da função a data referente à inclusão do *dataset*:



```r
# carregando todos pacotes utilizados nessa análise
library(tidyverse) 
library(scales) # utilizado para configurar a formatação dos eixos do gráfico

tuesdata <- tidytuesdayR::tt_load('2021-01-05') # vou baixar os dados referentes à essa semana
```

```
## --- Compiling #TidyTuesday Information for 2021-01-05 ----
```

```
## --- There is 1 file available ---
```

```
## --- Starting Download ---
```

```
## 
## 	Downloading file 1 of 1: `transit_cost.csv`
```

```
## --- Download complete ---
```

```r
transit_cost <- tuesdata$transit_cost # leitura da tabela em si
```
Agora, vou dar uma olhada na estrutura da tabela e escolher as variáveis que vou utilizar no plot:


```r
glimpse(transit_cost)
```

```
## Rows: 544
## Columns: 20
## $ e                <dbl> 7136, 7137, 7138, 7139, 7144, 7145, 7146, 7147, 7152,…
## $ country          <chr> "CA", "CA", "CA", "CA", "CA", "NL", "CA", "US", "US",…
## $ city             <chr> "Vancouver", "Toronto", "Toronto", "Toronto", "Toront…
## $ line             <chr> "Broadway", "Vaughan", "Scarborough", "Ontario", "Yon…
## $ start_year       <chr> "2020", "2009", "2020", "2020", "2020", "2003", "2020…
## $ end_year         <chr> "2025", "2017", "2030", "2030", "2030", "2018", "2026…
## $ rr               <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ length           <dbl> 5.7, 8.6, 7.8, 15.5, 7.4, 9.7, 5.8, 5.1, 4.2, 4.2, 6.…
## $ tunnel_per       <chr> "87.72%", "100.00%", "100.00%", "57.00%", "100.00%", …
## $ tunnel           <dbl> 5.0, 8.6, 7.8, 8.8, 7.4, 7.1, 5.8, 5.1, 4.2, 4.2, 6.3…
## $ stations         <dbl> 6, 6, 3, 15, 6, 8, 5, 2, 2, 2, 3, 3, 4, 7, 13, 4, 4, …
## $ source1          <chr> "Plan", "Media", "Wiki", "Plan", "Plan", "Wiki", "Med…
## $ cost             <dbl> 2830, 3200, 5500, 8573, 5600, 3100, 4500, 1756, 3600,…
## $ currency         <chr> "CAD", "CAD", "CAD", "CAD", "CAD", "EUR", "CAD", "USD…
## $ year             <dbl> 2018, 2013, 2018, 2019, 2020, 2009, 2018, 2012, 2023,…
## $ ppp_rate         <dbl> 0.840, 0.810, 0.840, 0.840, 0.840, 1.300, 0.840, 1.00…
## $ real_cost        <chr> "2377.2", "2592", "4620", "7201.32", "4704", "4030", …
## $ cost_km_millions <dbl> 417.05263, 301.39535, 592.30769, 464.60129, 635.67568…
## $ source2          <chr> "Media", "Media", "Media", "Plan", "Media", "Media", …
## $ reference        <chr> "https://www.translink.ca/Plans-and-Projects/Rapid-Tr…
```
Vou plotar o custo real da construção das malhas (`real_cost`) em função do comprimento delas (`length`), unidades respectivamente em dólares e km. Cada observação representa uma malha de uma determinada cidade em um determinado país.

Antes de fazer isso, entretanto, tenho que alterar o tipo da coluna `real_cost`, que está como *string*. Vou passar para a coluna para numérico:


```r
transit_cost <- 
  transit_cost %>% 
    mutate(real_cost=as.numeric(real_cost))
```

```
## Warning in mask$eval_all_mutate(quo): NAs introduced by coercion
```
Alguns valores são substituídos por `NA`. São entradas que contém caracteres além de números. Entradas que contém apenas algarismos (e "." ou ",") são convertidos corretamente. 

Em seguida vou remover as entradas `NA` dessas duas colunas, na tabela:


```r
transit_cost <- 
  transit_cost %>% 
    filter(!is.na(length), !is.na(real_cost))
```


Agora posso plotar os dados!

# Gráfico de pontos, escala e reta de regressão

Indo direto ao ponto, quero plotar um gráfico de pontos usando essas duas variáveis: 


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point()
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-6-1.png" width="672" />
Os outliers e as diferenças em ordens de grandeza das unidades distorcem bem a escala. Isso pode ser corrigido convertendo a escala para log:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point() +
  scale_y_log10() + # eixo y em log
  scale_x_log10() # eixo x em log
```

```
## Warning: Transformation introduced infinite values in continuous x-axis
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-7-1.png" width="672" />
Bem melhor! Existe um problema referente aos pontos que foram computados como `\(-Inf\)` e, por isso, vão sempre tender a 0 no eixo x. Isso ocorreu por causa da conversão para log. Isso poderia ser remediado adicionando um incremento irrisório nos valores apenas para que eles se diferenciassem de 0, mas aqui, em nome da objetividade, vou retirá-los:


```r
transit_cost <- # retirando os infinitos negativos da coluna real_cost
  transit_cost %>% filter(real_cost > 0)

transit_cost %>% ggplot(aes(y=length,x=real_cost)) + # plotando novamente
  geom_point() +
  scale_y_log10() + # eixo y em log
  scale_x_log10() # eixo x em log
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-8-1.png" width="672" />
As cores sólidas nos pontos atrapalham a visualização de pontos sobrepostos. Adicionar um efeito de transparência resolve esse problema:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5) + #adicionando transparência aos pontos
  scale_y_log10() + 
  scale_x_log10() 
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Em seguida, vou adicionar uma reta de regressão no gráfico. Isso pode ser feito usando a função `geom_smooth`, especificando o argumento `method` como `lm` (método linear):


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5) +
  geom_smooth(method="lm") +
  scale_y_log10() + 
  scale_x_log10() 
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-10-1.png" width="672" />
Não quero uma linha sólida e também não quero que o erro padrão esteja presente no gráfico. Vou alterar isso incluindo os argumentos `lty` (de *linetype*) e `se` (de *standard error*). Também quero alterar a cor da reta, o que é feito intuitivamente usando o argumento `color`:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5) +
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) + # alterando propriedades da reta
  scale_y_log10() + 
  scale_x_log10() 
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-11-1.png" width="672" />
# Mexendo nas cores

Vou alterar agora o esquema de cores. Quero deixar o plano de fundo escuro e os pontos claros. A maior parte dos comandos que alteram esses atributos estão vinculadas à função `theme`, que além de alterar as cores, também pode ser usada para alterar as fontes e marcações dos eixos.

Abaixo, altero a cor do grid:



```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5) +
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) + 
  scale_y_log10() + 
  scale_x_log10() +
  theme(panel.background = element_rect(fill = "#343536")) # alterando a cor do grid
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-12-1.png" width="672" />
Obviamente, não posso manter os pontos pretos com essa cor de fundo. Vou inverter a cor deles então, deixando-os, brancos:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + # alterando a cor dos pontos
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10() +
  theme(panel.background = element_rect(fill = "#343536"))
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-13-1.png" width="672" />

Eu quero **todo** gráfico escuro, não apenas o grid. Então vou incluir mais um argumento na função `theme` para dar conta disso:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + 
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10() +
  theme(panel.background = element_rect(fill = "#343536"),
        rect = element_rect(fill = "#343536", color = NA)) # cor na imagem toda
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-14-1.png" width="672" />
Ainda existe uma linha branca envolvendo todo plot, que está bem feia. De novo, vou mexer na função `theme`, dessa vez incluindo o argumento `plot.background`, definindo ele como `element_rect(color=NA)`, o que significa que a margem não vai ter cor nenhuma:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + 
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10() +
  theme(panel.background = element_rect(fill = "#343536"),
        rect = element_rect(fill = "#343536", color = NA),
        plot.background = element_rect(color = NA)) # tirando a cor da borda 
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-15-1.png" width="672" />
As fontes do gráfico precisam de cores melhores agora. Os texto do gráfico (título, subtítulo e título dos eixos) e o texto das unidades dos eixos em si, são elementos diferentes. Vou definir ambos como branco, usando dois argumentos, `text` e `axis_text`:   


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + 
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10() +
  theme(panel.background = element_rect(fill = "#343536"),
        rect = element_rect(fill = "#343536", color = NA),
        plot.background = element_rect(color = NA),
        text = element_text( color = "white"), # cor do texto do gráfico em branco
        axis.text = element_text( color = "white")) # cor do conteúdo dos eixos em branco
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-16-1.png" width="672" />
# Formatando o texto

Agora vou mexer na formatação do texto dos eixos, saindo da função `theme`. Primeiro, vou adicionar a notação de grandeza como "." no eixo x. Isso pode ser feito incluindo o argumento `labels = comma_format(big.mark = ".")` dentro da função `scale_x_log10`. Esse argumento não faz parte do `ggplot2`, ele vem do pacote `scales`:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + 
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10(labels = comma_format(big.mark = ".")) + # notação decimal no eixo
  theme(panel.background = element_rect(fill = "#343536"),
        rect = element_rect(fill = "#343536", color = NA),
        plot.background = element_rect(color = NA),
        text = element_text( color = "white"),
        axis.text = element_text( color = "white")) 
```

```
## Warning in prettyNum(.Internal(format(x, trim, digits, nsmall, width, 3L, :
## 'big.mark' and 'decimal.mark' are both '.', which could be confusing
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Agora, para finalizar, vou adicionar um título, subtítulo e renomear o eixo y e eixo x:


```r
transit_cost %>% ggplot(aes(y=length,x=real_cost)) +
  geom_point(alpha=0.5, color = "white") + 
  geom_smooth(method="lm",lty=2,color="black",se = FALSE) +
  scale_y_log10() + 
  scale_x_log10(labels = comma_format(big.mark = ".")) + # notação decimal no eixo
  theme(panel.background = element_rect(fill = "#343536"),
        rect = element_rect(fill = "#343536", color = NA),
        plot.background = element_rect(color = NA),
        text = element_text( color = "white"),
        axis.text = element_text( color = "white"))  +
  ylab("Comprimento (km)") + # renomeando eixo y
  xlab("Custo real (USD)") + # renomeando eixo x
  ggtitle("Custo por comprimento de malhas urbanas.", # título e subtítulo
          subtitle = "Dados extraídos do tidy tuesday de 05/01/2020 e utilizado como\nmodelo para um exercício de customização de gráficos no ggplot2.\n\nCada ponto representa uma malha construída em alguma cidade no mundo.\n")
```

```
## Warning in prettyNum(.Internal(format(x, trim, digits, nsmall, width, 3L, :
## 'big.mark' and 'decimal.mark' are both '.', which could be confusing
```

<img src="/blog/2021-01-07-tidytuesday20210105/tidytuesday20210105_files/figure-html/unnamed-chunk-18-1.png" width="672" />

E pronto! Está aí o gráfico. Lembrando, cada ponto representa uma malha em alguma localidade de alguma cidade de algum país. Então cada país está representado diversas vezes no gráfico, de acordo com a localidade da malha. Entretando, meu foco aqui não foi limpar os dados para análise, apenas utilizá-los para gerar o gráfico customizado.







