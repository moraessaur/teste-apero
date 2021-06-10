---
title: "Regressão Linear Simples - Fish Market data"
excerpt: |
  Pequena regressão simples usando medidas de peixes de um mercado. Exploro a regressão, fatores que influenciam na qualidade dela e identifico a colinearidade de algumas variáveis.
author: Lucas Moraes
date: 11-09-2020
categories:
  - Regressão linear
  - Distância de Cook
  - vif
output:
  distill::distill_article:
    toc: true
    self_contained: false
---
<link href="{{< blogdown/postref >}}index_files/pagedtable/css/pagedtable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/pagedtable/js/pagedtable.js"></script>

<style>
body {
text-align: justify}
</style>



Para fazer essa análise, vou usar o dataset [`Fish`](https://www.kaggle.com/aungpyaeap/fish-market) quem contém diferentes medidas de peixes registrados em um mercado. Versões com código podem ser conferidas no meu [Kaggle](https://www.kaggle.com/moraessaur/fish-market-simple-linear-regression) (em inglês) ou nas minhas [Github pages](https://moraessaur.github.io/fish_market/).

Meu objetivo aqui vai ser escolher uma variável de resposta e a melhor variável que explique seu comportamento.

Primeiro vou dar uma olhada no dataset:


```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.3     ✓ purrr   0.3.4
## ✓ tibble  3.1.2     ✓ dplyr   1.0.5
## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
## ✓ readr   1.4.0     ✓ forcats 0.5.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```
## Loading required package: carData
```

```
## 
## Attaching package: 'car'
```

```
## The following object is masked from 'package:dplyr':
## 
##     recode
```

```
## The following object is masked from 'package:purrr':
## 
##     some
```

```
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   Species = col_character(),
##   Weight = col_double(),
##   Length1 = col_double(),
##   Length2 = col_double(),
##   Length3 = col_double(),
##   Height = col_double(),
##   Width = col_double()
## )
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Species"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Weight"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Length1"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["Length2"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Length3"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["Height"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["Width"],"name":[7],"type":["dbl"],"align":["right"]}],"data":[{"1":"Bream","2":"242.0","3":"23.2","4":"25.4","5":"30.0","6":"11.5200","7":"4.0200"},{"1":"Bream","2":"290.0","3":"24.0","4":"26.3","5":"31.2","6":"12.4800","7":"4.3056"},{"1":"Bream","2":"340.0","3":"23.9","4":"26.5","5":"31.1","6":"12.3778","7":"4.6961"},{"1":"Bream","2":"363.0","3":"26.3","4":"29.0","5":"33.5","6":"12.7300","7":"4.4555"},{"1":"Bream","2":"430.0","3":"26.5","4":"29.0","5":"34.0","6":"12.4440","7":"5.1340"},{"1":"Bream","2":"450.0","3":"26.8","4":"29.7","5":"34.7","6":"13.6024","7":"4.9274"},{"1":"Bream","2":"500.0","3":"26.8","4":"29.7","5":"34.5","6":"14.1795","7":"5.2785"},{"1":"Bream","2":"390.0","3":"27.6","4":"30.0","5":"35.0","6":"12.6700","7":"4.6900"},{"1":"Bream","2":"450.0","3":"27.6","4":"30.0","5":"35.1","6":"14.0049","7":"4.8438"},{"1":"Bream","2":"500.0","3":"28.5","4":"30.7","5":"36.2","6":"14.2266","7":"4.9594"},{"1":"Bream","2":"475.0","3":"28.4","4":"31.0","5":"36.2","6":"14.2628","7":"5.1042"},{"1":"Bream","2":"500.0","3":"28.7","4":"31.0","5":"36.2","6":"14.3714","7":"4.8146"},{"1":"Bream","2":"500.0","3":"29.1","4":"31.5","5":"36.4","6":"13.7592","7":"4.3680"},{"1":"Bream","2":"340.0","3":"29.5","4":"32.0","5":"37.3","6":"13.9129","7":"5.0728"},{"1":"Bream","2":"600.0","3":"29.4","4":"32.0","5":"37.2","6":"14.9544","7":"5.1708"},{"1":"Bream","2":"600.0","3":"29.4","4":"32.0","5":"37.2","6":"15.4380","7":"5.5800"},{"1":"Bream","2":"700.0","3":"30.4","4":"33.0","5":"38.3","6":"14.8604","7":"5.2854"},{"1":"Bream","2":"700.0","3":"30.4","4":"33.0","5":"38.5","6":"14.9380","7":"5.1975"},{"1":"Bream","2":"610.0","3":"30.9","4":"33.5","5":"38.6","6":"15.6330","7":"5.1338"},{"1":"Bream","2":"650.0","3":"31.0","4":"33.5","5":"38.7","6":"14.4738","7":"5.7276"},{"1":"Bream","2":"575.0","3":"31.3","4":"34.0","5":"39.5","6":"15.1285","7":"5.5695"},{"1":"Bream","2":"685.0","3":"31.4","4":"34.0","5":"39.2","6":"15.9936","7":"5.3704"},{"1":"Bream","2":"620.0","3":"31.5","4":"34.5","5":"39.7","6":"15.5227","7":"5.2801"},{"1":"Bream","2":"680.0","3":"31.8","4":"35.0","5":"40.6","6":"15.4686","7":"6.1306"},{"1":"Bream","2":"700.0","3":"31.9","4":"35.0","5":"40.5","6":"16.2405","7":"5.5890"},{"1":"Bream","2":"725.0","3":"31.8","4":"35.0","5":"40.9","6":"16.3600","7":"6.0532"},{"1":"Bream","2":"720.0","3":"32.0","4":"35.0","5":"40.6","6":"16.3618","7":"6.0900"},{"1":"Bream","2":"714.0","3":"32.7","4":"36.0","5":"41.5","6":"16.5170","7":"5.8515"},{"1":"Bream","2":"850.0","3":"32.8","4":"36.0","5":"41.6","6":"16.8896","7":"6.1984"},{"1":"Bream","2":"1000.0","3":"33.5","4":"37.0","5":"42.6","6":"18.9570","7":"6.6030"},{"1":"Bream","2":"920.0","3":"35.0","4":"38.5","5":"44.1","6":"18.0369","7":"6.3063"},{"1":"Bream","2":"955.0","3":"35.0","4":"38.5","5":"44.0","6":"18.0840","7":"6.2920"},{"1":"Bream","2":"925.0","3":"36.2","4":"39.5","5":"45.3","6":"18.7542","7":"6.7497"},{"1":"Bream","2":"975.0","3":"37.4","4":"41.0","5":"45.9","6":"18.6354","7":"6.7473"},{"1":"Bream","2":"950.0","3":"38.0","4":"41.0","5":"46.5","6":"17.6235","7":"6.3705"},{"1":"Roach","2":"40.0","3":"12.9","4":"14.1","5":"16.2","6":"4.1472","7":"2.2680"},{"1":"Roach","2":"69.0","3":"16.5","4":"18.2","5":"20.3","6":"5.2983","7":"2.8217"},{"1":"Roach","2":"78.0","3":"17.5","4":"18.8","5":"21.2","6":"5.5756","7":"2.9044"},{"1":"Roach","2":"87.0","3":"18.2","4":"19.8","5":"22.2","6":"5.6166","7":"3.1746"},{"1":"Roach","2":"120.0","3":"18.6","4":"20.0","5":"22.2","6":"6.2160","7":"3.5742"},{"1":"Roach","2":"0.0","3":"19.0","4":"20.5","5":"22.8","6":"6.4752","7":"3.3516"},{"1":"Roach","2":"110.0","3":"19.1","4":"20.8","5":"23.1","6":"6.1677","7":"3.3957"},{"1":"Roach","2":"120.0","3":"19.4","4":"21.0","5":"23.7","6":"6.1146","7":"3.2943"},{"1":"Roach","2":"150.0","3":"20.4","4":"22.0","5":"24.7","6":"5.8045","7":"3.7544"},{"1":"Roach","2":"145.0","3":"20.5","4":"22.0","5":"24.3","6":"6.6339","7":"3.5478"},{"1":"Roach","2":"160.0","3":"20.5","4":"22.5","5":"25.3","6":"7.0334","7":"3.8203"},{"1":"Roach","2":"140.0","3":"21.0","4":"22.5","5":"25.0","6":"6.5500","7":"3.3250"},{"1":"Roach","2":"160.0","3":"21.1","4":"22.5","5":"25.0","6":"6.4000","7":"3.8000"},{"1":"Roach","2":"169.0","3":"22.0","4":"24.0","5":"27.2","6":"7.5344","7":"3.8352"},{"1":"Roach","2":"161.0","3":"22.0","4":"23.4","5":"26.7","6":"6.9153","7":"3.6312"},{"1":"Roach","2":"200.0","3":"22.1","4":"23.5","5":"26.8","6":"7.3968","7":"4.1272"},{"1":"Roach","2":"180.0","3":"23.6","4":"25.2","5":"27.9","6":"7.0866","7":"3.9060"},{"1":"Roach","2":"290.0","3":"24.0","4":"26.0","5":"29.2","6":"8.8768","7":"4.4968"},{"1":"Roach","2":"272.0","3":"25.0","4":"27.0","5":"30.6","6":"8.5680","7":"4.7736"},{"1":"Roach","2":"390.0","3":"29.5","4":"31.7","5":"35.0","6":"9.4850","7":"5.3550"},{"1":"Whitefish","2":"270.0","3":"23.6","4":"26.0","5":"28.7","6":"8.3804","7":"4.2476"},{"1":"Whitefish","2":"270.0","3":"24.1","4":"26.5","5":"29.3","6":"8.1454","7":"4.2485"},{"1":"Whitefish","2":"306.0","3":"25.6","4":"28.0","5":"30.8","6":"8.7780","7":"4.6816"},{"1":"Whitefish","2":"540.0","3":"28.5","4":"31.0","5":"34.0","6":"10.7440","7":"6.5620"},{"1":"Whitefish","2":"800.0","3":"33.7","4":"36.4","5":"39.6","6":"11.7612","7":"6.5736"},{"1":"Whitefish","2":"1000.0","3":"37.3","4":"40.0","5":"43.5","6":"12.3540","7":"6.5250"},{"1":"Parkki","2":"55.0","3":"13.5","4":"14.7","5":"16.5","6":"6.8475","7":"2.3265"},{"1":"Parkki","2":"60.0","3":"14.3","4":"15.5","5":"17.4","6":"6.5772","7":"2.3142"},{"1":"Parkki","2":"90.0","3":"16.3","4":"17.7","5":"19.8","6":"7.4052","7":"2.6730"},{"1":"Parkki","2":"120.0","3":"17.5","4":"19.0","5":"21.3","6":"8.3922","7":"2.9181"},{"1":"Parkki","2":"150.0","3":"18.4","4":"20.0","5":"22.4","6":"8.8928","7":"3.2928"},{"1":"Parkki","2":"140.0","3":"19.0","4":"20.7","5":"23.2","6":"8.5376","7":"3.2944"},{"1":"Parkki","2":"170.0","3":"19.0","4":"20.7","5":"23.2","6":"9.3960","7":"3.4104"},{"1":"Parkki","2":"145.0","3":"19.8","4":"21.5","5":"24.1","6":"9.7364","7":"3.1571"},{"1":"Parkki","2":"200.0","3":"21.2","4":"23.0","5":"25.8","6":"10.3458","7":"3.6636"},{"1":"Parkki","2":"273.0","3":"23.0","4":"25.0","5":"28.0","6":"11.0880","7":"4.1440"},{"1":"Parkki","2":"300.0","3":"24.0","4":"26.0","5":"29.0","6":"11.3680","7":"4.2340"},{"1":"Perch","2":"5.9","3":"7.5","4":"8.4","5":"8.8","6":"2.1120","7":"1.4080"},{"1":"Perch","2":"32.0","3":"12.5","4":"13.7","5":"14.7","6":"3.5280","7":"1.9992"},{"1":"Perch","2":"40.0","3":"13.8","4":"15.0","5":"16.0","6":"3.8240","7":"2.4320"},{"1":"Perch","2":"51.5","3":"15.0","4":"16.2","5":"17.2","6":"4.5924","7":"2.6316"},{"1":"Perch","2":"70.0","3":"15.7","4":"17.4","5":"18.5","6":"4.5880","7":"2.9415"},{"1":"Perch","2":"100.0","3":"16.2","4":"18.0","5":"19.2","6":"5.2224","7":"3.3216"},{"1":"Perch","2":"78.0","3":"16.8","4":"18.7","5":"19.4","6":"5.1992","7":"3.1234"},{"1":"Perch","2":"80.0","3":"17.2","4":"19.0","5":"20.2","6":"5.6358","7":"3.0502"},{"1":"Perch","2":"85.0","3":"17.8","4":"19.6","5":"20.8","6":"5.1376","7":"3.0368"},{"1":"Perch","2":"85.0","3":"18.2","4":"20.0","5":"21.0","6":"5.0820","7":"2.7720"},{"1":"Perch","2":"110.0","3":"19.0","4":"21.0","5":"22.5","6":"5.6925","7":"3.5550"},{"1":"Perch","2":"115.0","3":"19.0","4":"21.0","5":"22.5","6":"5.9175","7":"3.3075"},{"1":"Perch","2":"125.0","3":"19.0","4":"21.0","5":"22.5","6":"5.6925","7":"3.6675"},{"1":"Perch","2":"130.0","3":"19.3","4":"21.3","5":"22.8","6":"6.3840","7":"3.5340"},{"1":"Perch","2":"120.0","3":"20.0","4":"22.0","5":"23.5","6":"6.1100","7":"3.4075"},{"1":"Perch","2":"120.0","3":"20.0","4":"22.0","5":"23.5","6":"5.6400","7":"3.5250"},{"1":"Perch","2":"130.0","3":"20.0","4":"22.0","5":"23.5","6":"6.1100","7":"3.5250"},{"1":"Perch","2":"135.0","3":"20.0","4":"22.0","5":"23.5","6":"5.8750","7":"3.5250"},{"1":"Perch","2":"110.0","3":"20.0","4":"22.0","5":"23.5","6":"5.5225","7":"3.9950"},{"1":"Perch","2":"130.0","3":"20.5","4":"22.5","5":"24.0","6":"5.8560","7":"3.6240"},{"1":"Perch","2":"150.0","3":"20.5","4":"22.5","5":"24.0","6":"6.7920","7":"3.6240"},{"1":"Perch","2":"145.0","3":"20.7","4":"22.7","5":"24.2","6":"5.9532","7":"3.6300"},{"1":"Perch","2":"150.0","3":"21.0","4":"23.0","5":"24.5","6":"5.2185","7":"3.6260"},{"1":"Perch","2":"170.0","3":"21.5","4":"23.5","5":"25.0","6":"6.2750","7":"3.7250"},{"1":"Perch","2":"225.0","3":"22.0","4":"24.0","5":"25.5","6":"7.2930","7":"3.7230"},{"1":"Perch","2":"145.0","3":"22.0","4":"24.0","5":"25.5","6":"6.3750","7":"3.8250"},{"1":"Perch","2":"188.0","3":"22.6","4":"24.6","5":"26.2","6":"6.7334","7":"4.1658"},{"1":"Perch","2":"180.0","3":"23.0","4":"25.0","5":"26.5","6":"6.4395","7":"3.6835"},{"1":"Perch","2":"197.0","3":"23.5","4":"25.6","5":"27.0","6":"6.5610","7":"4.2390"},{"1":"Perch","2":"218.0","3":"25.0","4":"26.5","5":"28.0","6":"7.1680","7":"4.1440"},{"1":"Perch","2":"300.0","3":"25.2","4":"27.3","5":"28.7","6":"8.3230","7":"5.1373"},{"1":"Perch","2":"260.0","3":"25.4","4":"27.5","5":"28.9","6":"7.1672","7":"4.3350"},{"1":"Perch","2":"265.0","3":"25.4","4":"27.5","5":"28.9","6":"7.0516","7":"4.3350"},{"1":"Perch","2":"250.0","3":"25.4","4":"27.5","5":"28.9","6":"7.2828","7":"4.5662"},{"1":"Perch","2":"250.0","3":"25.9","4":"28.0","5":"29.4","6":"7.8204","7":"4.2042"},{"1":"Perch","2":"300.0","3":"26.9","4":"28.7","5":"30.1","6":"7.5852","7":"4.6354"},{"1":"Perch","2":"320.0","3":"27.8","4":"30.0","5":"31.6","6":"7.6156","7":"4.7716"},{"1":"Perch","2":"514.0","3":"30.5","4":"32.8","5":"34.0","6":"10.0300","7":"6.0180"},{"1":"Perch","2":"556.0","3":"32.0","4":"34.5","5":"36.5","6":"10.2565","7":"6.3875"},{"1":"Perch","2":"840.0","3":"32.5","4":"35.0","5":"37.3","6":"11.4884","7":"7.7957"},{"1":"Perch","2":"685.0","3":"34.0","4":"36.5","5":"39.0","6":"10.8810","7":"6.8640"},{"1":"Perch","2":"700.0","3":"34.0","4":"36.0","5":"38.3","6":"10.6091","7":"6.7408"},{"1":"Perch","2":"700.0","3":"34.5","4":"37.0","5":"39.4","6":"10.8350","7":"6.2646"},{"1":"Perch","2":"690.0","3":"34.6","4":"37.0","5":"39.3","6":"10.5717","7":"6.3666"},{"1":"Perch","2":"900.0","3":"36.5","4":"39.0","5":"41.4","6":"11.1366","7":"7.4934"},{"1":"Perch","2":"650.0","3":"36.5","4":"39.0","5":"41.4","6":"11.1366","7":"6.0030"},{"1":"Perch","2":"820.0","3":"36.6","4":"39.0","5":"41.3","6":"12.4313","7":"7.3514"},{"1":"Perch","2":"850.0","3":"36.9","4":"40.0","5":"42.3","6":"11.9286","7":"7.1064"},{"1":"Perch","2":"900.0","3":"37.0","4":"40.0","5":"42.5","6":"11.7300","7":"7.2250"},{"1":"Perch","2":"1015.0","3":"37.0","4":"40.0","5":"42.4","6":"12.3808","7":"7.4624"},{"1":"Perch","2":"820.0","3":"37.1","4":"40.0","5":"42.5","6":"11.1350","7":"6.6300"},{"1":"Perch","2":"1100.0","3":"39.0","4":"42.0","5":"44.6","6":"12.8002","7":"6.8684"},{"1":"Perch","2":"1000.0","3":"39.8","4":"43.0","5":"45.2","6":"11.9328","7":"7.2772"},{"1":"Perch","2":"1100.0","3":"40.1","4":"43.0","5":"45.5","6":"12.5125","7":"7.4165"},{"1":"Perch","2":"1000.0","3":"40.2","4":"43.5","5":"46.0","6":"12.6040","7":"8.1420"},{"1":"Perch","2":"1000.0","3":"41.1","4":"44.0","5":"46.6","6":"12.4888","7":"7.5958"},{"1":"Pike","2":"200.0","3":"30.0","4":"32.3","5":"34.8","6":"5.5680","7":"3.3756"},{"1":"Pike","2":"300.0","3":"31.7","4":"34.0","5":"37.8","6":"5.7078","7":"4.1580"},{"1":"Pike","2":"300.0","3":"32.7","4":"35.0","5":"38.8","6":"5.9364","7":"4.3844"},{"1":"Pike","2":"300.0","3":"34.8","4":"37.3","5":"39.8","6":"6.2884","7":"4.0198"},{"1":"Pike","2":"430.0","3":"35.5","4":"38.0","5":"40.5","6":"7.2900","7":"4.5765"},{"1":"Pike","2":"345.0","3":"36.0","4":"38.5","5":"41.0","6":"6.3960","7":"3.9770"},{"1":"Pike","2":"456.0","3":"40.0","4":"42.5","5":"45.5","6":"7.2800","7":"4.3225"},{"1":"Pike","2":"510.0","3":"40.0","4":"42.5","5":"45.5","6":"6.8250","7":"4.4590"},{"1":"Pike","2":"540.0","3":"40.1","4":"43.0","5":"45.8","6":"7.7860","7":"5.1296"},{"1":"Pike","2":"500.0","3":"42.0","4":"45.0","5":"48.0","6":"6.9600","7":"4.8960"},{"1":"Pike","2":"567.0","3":"43.2","4":"46.0","5":"48.7","6":"7.7920","7":"4.8700"},{"1":"Pike","2":"770.0","3":"44.8","4":"48.0","5":"51.2","6":"7.6800","7":"5.3760"},{"1":"Pike","2":"950.0","3":"48.3","4":"51.7","5":"55.1","6":"8.9262","7":"6.1712"},{"1":"Pike","2":"1250.0","3":"52.0","4":"56.0","5":"59.7","6":"10.6863","7":"6.9849"},{"1":"Pike","2":"1600.0","3":"56.0","4":"60.0","5":"64.0","6":"9.6000","7":"6.1440"},{"1":"Pike","2":"1550.0","3":"56.0","4":"60.0","5":"64.0","6":"9.6000","7":"6.1440"},{"1":"Pike","2":"1650.0","3":"59.0","4":"63.4","5":"68.0","6":"10.8120","7":"7.4800"},{"1":"Smelt","2":"6.7","3":"9.3","4":"9.8","5":"10.8","6":"1.7388","7":"1.0476"},{"1":"Smelt","2":"7.5","3":"10.0","4":"10.5","5":"11.6","6":"1.9720","7":"1.1600"},{"1":"Smelt","2":"7.0","3":"10.1","4":"10.6","5":"11.6","6":"1.7284","7":"1.1484"},{"1":"Smelt","2":"9.7","3":"10.4","4":"11.0","5":"12.0","6":"2.1960","7":"1.3800"},{"1":"Smelt","2":"9.8","3":"10.7","4":"11.2","5":"12.4","6":"2.0832","7":"1.2772"},{"1":"Smelt","2":"8.7","3":"10.8","4":"11.3","5":"12.6","6":"1.9782","7":"1.2852"},{"1":"Smelt","2":"10.0","3":"11.3","4":"11.8","5":"13.1","6":"2.2139","7":"1.2838"},{"1":"Smelt","2":"9.9","3":"11.3","4":"11.8","5":"13.1","6":"2.2139","7":"1.1659"},{"1":"Smelt","2":"9.8","3":"11.4","4":"12.0","5":"13.2","6":"2.2044","7":"1.1484"},{"1":"Smelt","2":"12.2","3":"11.5","4":"12.2","5":"13.4","6":"2.0904","7":"1.3936"},{"1":"Smelt","2":"13.4","3":"11.7","4":"12.4","5":"13.5","6":"2.4300","7":"1.2690"},{"1":"Smelt","2":"12.2","3":"12.1","4":"13.0","5":"13.8","6":"2.2770","7":"1.2558"},{"1":"Smelt","2":"19.7","3":"13.2","4":"14.3","5":"15.2","6":"2.8728","7":"2.0672"},{"1":"Smelt","2":"19.9","3":"13.8","4":"15.0","5":"16.2","6":"2.9322","7":"1.8792"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
Os nomes de coluna não são muito intuitivos, então vou descrever cada um:

1. `Species`: nome da espécie mensurada.
2. `Weight`: peso do peixe em gramas.
3. `Length1`: comprimento vertical em cm.
4. `Length2`: comprimento diagonal em cm.
5. `Length3`: comprimento cruzado em cm.
6. `Height`: altura em cm.
7. `Width`: largura diagonal em cm.

Vou definir como minha variável de resposta o peso do peixe, esse é o valor que quero prever em função das demais variáveis explicativas. Para dar início à análise, entao, vou plotar a correlacao de minha varável de resposta com todas as demais variáveis explanatórias e calcular o `\(R^2\)` de cada caso.

Para plotar tudo de uma vez, alterei um pouco a tabela e criei um grid:


```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />
Dá para perceber que a correlação do peso com as demais medidas é positiva em todos casos, em alguns mais e outros menos, pela inclinação das retas de regressão (tracejadas azuis). Entretanto, fica difícil entender a robustez dos modelos (na forma de `\(R^2\)`) visualmente. Portanto, vou computar esses valores em uma tabela e ordenar em ordem decrescente:


```
## # A tibble: 5 x 2
##   variables r_squared
##   <chr>         <dbl>
## 1 Length3       0.852
## 2 Length2       0.844
## 3 Length1       0.839
## 4 Width         0.786
## 5 Height        0.525
```

Essa tabela me dá os valores de `\(R^2\)` para cada modelo simples de peso em relação a cada uma das outras variáveis independentemente. 

As três medidas de comprimento são as que melhor descrevem o comportamento do peso. A altura é a medida menos robusta e a largura fica no meio do caminho. Os valores altos dos modelos que levam em consideração os diferentes tipos de comprimento apontam para uma possível colinearidade entre essas variáveis. Isso significa que, em um modelo multivariado, essas medidas provavelmente seriam redundantes.

Vou desenvolver isso um pouco mais a frente, mas por hora, como vou fazer uma regressão simples, vou usar a variável `Length3` como explanatória, dado que ela resultou no maior valor de `\(R^2\)`.

Abaixo, os coeficientes do modelo:


```
## 
## Call:
## lm(formula = Weight ~ Length3, data = fish)
## 
## Coefficients:
## (Intercept)      Length3  
##     -490.40        28.46
```

Segundo esse modelo, existe um incremento de cerca de 28 gramas no peso de um peixe conforme a largura cruzada do peixe aumenta em uma unidade, ou seja, 1 centímetro.

Abaixo, vou plotar o gráfico da regressão, já atribuindo cores diferentes aos pontos para as diferentes espécies da amostra:



```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

Um fator interessante de se notar é a provável alta influência dos pontos associados à espécie Pike (em roxo), na inclinação e erro associado da reta, devido ao alto valor tanto de peso quanto de comprimento cruzado de alguns de seus pontos. Essa espécie provavelmente tem maiores valores, em média, de distância de cook para suas observações, o que vai influenciar no comportamento da reta. Podemos checar isso sumarizando o modelo e esses valores, por espécie:



```
## # A tibble: 7 x 2
##   Species   mean_cooksd
##   <chr>           <dbl>
## 1 Pike         0.0428  
## 2 Smelt        0.0116  
## 3 Whitefish    0.00550 
## 4 Perch        0.00528 
## 5 Bream        0.00325 
## 6 Roach        0.00150 
## 7 Parkki       0.000504
```

Como previsto, a espécie Pike de fato tem a maior média para os valores de distância de cook associados seus pontos, seguida pela espécie Smelt (verde escuro), que no gráfico tem uma concentração de pontos próximos de zero. Esses pontos, em ambas espécies, se distanciam bastante da reta de regressão, explicando o comportamento das médias de distância de cooks associadas a elas.

Vou checar mais a fundo a influência que estas espécies tem na reta e no valor de `\(R^2\)`.

Lembrando que o modelo tem `\(R^2\)` de 0.85, vou verificar qual valor que `\(R^2\)` assume retirando essas espécies dele:


```
## [1] 0.9109848
```

Retirando as espécies, a robustez do modelo aumenta significativamente. A inclinação da reta também se altera, conforme podemos ver na mudança do coeficiente associado à variável explanatória de comprimento cruzado:


```
## 
## Call:
## lm(formula = Weight ~ Length3, data = .)
## 
## Coefficients:
## (Intercept)      Length3  
##     -656.48        34.14
```

Para enxergar essa alteração melhor ainda, podemos sobrepor as retas de cada modelo:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />
Visualizando dessa forma fica mais claro o comportamento da nova reta em comparação com a reta associada ao primeiro modelo (tracejada vermelha). Essa inclinação indica uma correlação mais forte entre as variáveis na ausência das espécies Pike e Smelt, fato reforçado quando se calcula os índices de *pearson* para os dois casos:


```
## # A tibble: 2 x 2
##   df                    indice_pearson
##   <chr>                          <dbl>
## 1 df_original                    0.923
## 2 df_sem_especies_ruido          0.954
```

Finalmente, sem aprofundar muito, existe outra maneira prática de verificar o quanto os pontos das espécies podem estar influenciando na qualidade da regressão, que é plotando as curvas de regressão por espécie:


```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-1.png" width="672" />

A espécie Pike é a única com valores de peso maiores que 1500. Já a espécie Smelt tem **apenas** valores próximos de zero, reforçando um pouco da influência destas na qualidade da regressão. Essa informação é redundante com o gráfico onde os pontos foram pintados de acordo com as espécies de referência, mas é uma maneira distinta de enxergar essa questão.

Esse tipo de fator levanta a questão: quais espécies escolher para estabelecer um modelo geral? Uma maneira de fazer isso seria calculando o `\(R^2\)` associado ao modelo escolhido **por espécie**, mas também indicando o número de observações por espécie existe nos dados:


```
## # A tibble: 5 x 2
##   variables r_squared
##   <chr>         <dbl>
## 1 Length3       0.852
## 2 Length2       0.844
## 3 Length1       0.839
## 4 Width         0.786
## 5 Height        0.525
```

```
## # A tibble: 7 x 4
##   Species   variables r_squared     n
##   <chr>     <chr>         <dbl> <int>
## 1 Whitefish Length3       0.993     6
## 2 Pike      Length3       0.958    17
## 3 Parkki    Length3       0.943    11
## 4 Perch     Length3       0.921    56
## 5 Smelt     Length3       0.898    14
## 6 Bream     Length3       0.897    35
## 7 Roach     Length3       0.842    20
```

Quando analisadas em separado, a espécie Pike (que apresentava altos valores de distância cook, em média), não apresenta um `\(R^2\)` tão baixo. Mas é importante observar que o número de observações da maior parte das espécies da amostra é bem baixo (menor que 20). Apenas as espécies Perch e Bream tem mais de 20 observações. A espécie Whitefish, que contém o maior valor de `\(R^2\)` em seu modelo, contém apenas 6 observações. Se a ideia fossse utilizar um modelo generalista, seria razoável manter apenas espécies número alto de observações (20 talvez) ou aumentar o número de medidas das espécies subamostradas.

# Colinearidade de variáveis
***

Como observado, todas medidas de comprimento apresentaram altos valores de `\(R^2\)` quando correlacionados com o peso. Recapitulando a tabela:


```
## # A tibble: 5 x 2
##   variables r_squared
##   <chr>         <dbl>
## 1 Length3       0.852
## 2 Length2       0.844
## 3 Length1       0.839
## 4 Width         0.786
## 5 Height        0.525
```

Esse comportamento pode ser um indicativo de colinearidade, o que faria sentido dada a natureza similar dessas medidas, em termos de proporção. Isso pode ser conferido computando os fatores de inflação de variância (vif) para cada variável, em um modelo que leva em conta todas variáveis:


```
##    Length1    Length2    Length3     Height      Width 
## 1681.49649 2084.25783  422.46825   14.57009   12.27536
```

Os altos valores de VIF para as medidas de comprimento indicam que essas variáveis são, de fato, redundantes para o modelo, não fazendo diferença qual é utilizada: ambas tem boa perfomance para explicar os valores de peso.

Esse comportamento pode ser visualizado se plotarmos, por exemplo, o peso em função de dois comprimentos quaisquer. Infelizmente, o gráfico interativo não se dá muito bem com o `distill` (que uso para publicar esses posts), então apenas a imagens estática está disponível abaixo:

![](https://www.dropbox.com/s/gno8w8jvorggczk/regression_plane.png?dl=1)

Felizmente, o gráfico interativo na íntegra pode ser acessado nas [Github pages](https://moraessaur.github.io/fish_market/) ou [Kaggle](https://www.kaggle.com/moraessaur/fish-market-simple-linear-regression) dessa análise. O que ele mostra é que ambas variáveis de comprimento tem valores muito próximos em todas observações, por isso os pontos se comportam de maneira similar a uma reta, mesmo tendo três dimensões. A colinearidade das variáveis também é reforçada pela alta densidade dos valores de peso no plano: quando os valores de comprimento são altos, independente de qual está sendo levado em consideração, o valor do peso previsto é alto.

# Conclusões
***

No geral, a primeira conclusão que pode ser tirada da análise, é que o comprimento é a medida que melhor descreve o peso dos peixes, independente de qual é utilizado, uma vez que as medidas de comprimento são colineares.

Algumas observações, relativas a determinadas espécies de peixes, representam pontos aberrantes e comprometem a qualidade da regressão, como foi visto quando computando valores de distância de cook. Além disso, a quantidade de observações por espécie de peixe varia bastante, com diversas espécies com número baixo de observações, fator que deve ser levado em consideração na implementação do modelo.












