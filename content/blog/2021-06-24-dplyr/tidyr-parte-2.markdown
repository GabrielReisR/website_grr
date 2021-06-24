---
title: 'Organizando com tidyr - Parte 2: valores missing'
author: "Gabriel R. R."
date: "8/2/2020"
draft: false
images:
series:
tags:
categories:
  - tutorial
layout: single
---

# Considerações iniciais
Post no Medium sobre isso aqui: https://medium.com/psicodata/valores-missing-parte-2-d2e0b832ce14

Esse é um documento feito para ensinar e para reforçar conteúdos de manipulação de dados usando _tidyr_. Qualquer comentário, erro ou sugestão, é só falar comigo entrando em contato através de qualquer uma das opções listadas em *Contato*.

O script em R está disponível aqui: https://github.com/GabrielReisR/R/blob/master/estrutura%20de%20dados/tidyr-parte-2.R

Essa publicação possui uma primeira parte em *Organizando com tidyr - Parte 1: dados wide e long*: https://rpubs.com/reisrgabriel/tidyrPt1

## Organizando bancos - colunas e missings
Às vezes, os dados estão desorganizados, o que pode dificultar as análises.

Um banco bem organizado é um no qual:

1. Cada célula é um valor único.
1. Cada coluna é uma variável.
1. Cada linha é uma observação.

Dados desorganizados, *untidy data*, quebram uma ou mais dessas regras acima.

Outros aspectos de bancos de dados desorganizados incluem:

* Nomes estranhos de coluna, o que dificulta parte da análise e leitura do código.
* Valores incompletos, também chamados de dados *missing*.

## Sobre o _tidyr_

O _tidyr_ é um pacote criado pelo time do tidyverse com a função de organização de um banco de dados.

**Ele existe para que as três regras acima sejam cumpridas**.

Para maiores informações sobre o tidyr: 

* Documentação: https://cran.r-project.org/web/packages/tidyr/tidyr.pdf
* Página no tidyverse: https://tidyr.tidyverse.org/index.html
* Cheatsheet (folha de códigos): https://github.com/rstudio/cheatsheets/blob/master/data-import.pdf

## Banco a ser usado

Para explicar os conceitos sobre limpeza de missings, pegaremos os dados de bibliotecas públicas ao redor do mundo: https://github.com/GabrielReisR/R/tree/master/estrutura%20de%20dados/dados/libraries.csv

Antes de começar, vou fazer uma mudança  no nome com o pacote *dplyr*.


```r
biblios_original <- read.csv("https://raw.githubusercontent.com/GabrielReisR/R/master/estrutura%20de%20dados/dados/libraries.csv")

library(dplyr)
biblios_original <- biblios_original %>%
  rename(id = X)
```


# Limpando nomes das colunas
Vamos utilizar o pacote *janitor* para limpar o nome das colunas.

Vamos ver antes como estão as colunas:


```r
names(biblios_original)
```

```
## [1] "id"                           "Country"                     
## [3] "Region"                       "Expenditures.....US.Dollars."
## [5] "Total.Libraries"              "Total.Librarians"            
## [7] "Total.Volumes"                "Total.Users"
```

É preciso uniformizar os nomes para facilitar a manipulação do banco. Vamos ler e utilizar *janitor*, especificamente a função `clean_names()`.

Abaixo, todas as possibilidades dessa função.


```r
library(janitor)

biblios_snake <- biblios_original %>% # note o uso do pipe
  clean_names() # se n?o dermos nenhum argumento, ent?o 'case = snake'

names(biblios_snake)
```

```
## [1] "id"                      "country"                
## [3] "region"                  "expenditures_us_dollars"
## [5] "total_libraries"         "total_librarians"       
## [7] "total_volumes"           "total_users"
```

```r
# lowerCamel
biblios_lowerCamel <- biblios_original %>% # note o uso do pipe
  clean_names(case = "lower_camel")

names(biblios_lowerCamel)
```

```
## [1] "id"                    "country"               "region"               
## [4] "expendituresUsDollars" "totalLibraries"        "totalLibrarians"      
## [7] "totalVolumes"          "totalUsers"
```

```r
# UpperCamel
biblios_UpperCamel <- biblios_original %>% # note o uso do pipe
  clean_names(case = "upper_camel")

names(biblios_UpperCamel)
```

```
## [1] "Id"                    "Country"               "Region"               
## [4] "ExpendituresUsDollars" "TotalLibraries"        "TotalLibrarians"      
## [7] "TotalVolumes"          "TotalUsers"
```

```r
# screaming_snake
biblios_SCREAMING_SNAKE <- biblios_original %>% # note o uso do pipe
  clean_names(case = "screaming_snake")

names(biblios_SCREAMING_SNAKE)
```

```
## [1] "ID"                      "COUNTRY"                
## [3] "REGION"                  "EXPENDITURES_US_DOLLARS"
## [5] "TOTAL_LIBRARIES"         "TOTAL_LIBRARIANS"       
## [7] "TOTAL_VOLUMES"           "TOTAL_USERS"
```

```r
# lowerUPPER
biblios_lowerUPPER <- biblios_original %>% # note o uso do pipe
  clean_names(case = "lower_upper")

names(biblios_lowerUPPER)
```

```
## [1] "id"                    "country"               "region"               
## [4] "expendituresUSdollars" "totalLIBRARIES"        "totalLIBRARIANS"      
## [7] "totalVOLUMES"          "totalUSERS"
```

```r
# UPPERlower
biblios_UPPERlower <- biblios_original %>% # note o uso do pipe
  clean_names(case = "upper_lower")

names(biblios_UPPERlower)
```

```
## [1] "ID"                    "COUNTRY"               "REGION"               
## [4] "EXPENDITURESusDOLLARS" "TOTALlibraries"        "TOTALlibrarians"      
## [7] "TOTALvolumes"          "TOTALusers"
```

```r
biblios <- biblios_snake # basta dar a biblios o valor de biblios_snake
names(biblios_snake)
```

```
## [1] "id"                      "country"                
## [3] "region"                  "expenditures_us_dollars"
## [5] "total_libraries"         "total_librarians"       
## [7] "total_volumes"           "total_users"
```

# Limpando missings

Há diversas formas de lidar com missings. Aqui, vamos ver apenas quatro dessas formas:

* `drop_na()`: escolhendo apenas as linhas sem nenhum missing; pacote *tidyr*.
* `fill_na()`: escolhendo preencher missing com valores do dataset; pacote *tidyr*.
* `replace_na()`: escolhendo valores específicos ou de tendência central (como média, mediana, etc.) para armazenar em missings; pacote *tidyr*.
* `imputar()`: uma função que vamos criar para imputação com regressão.

Em primeiro lugar, vamos carregar nosso banco de dados que já vimos, agora com o nome de suas colunas em português.

Novamente: esse é um banco sobre bibliotecas públicas de diversos países, junto com o dinheiro (em dólar) de quanto cada país gastou com suas bibliotecas, a quantidade de pessoas que trabalhavam nas bibliotecas, a quantidade de usuários e de livros dessas bibliotecas.

## drop_na()
A função `drop_na()` elimina todas as linhas que contém missings no dataset.


```r
library(Amelia) # para visualizar missings com 'missmap()'
library(tidyr)

# lendo os dados
url <- "https://raw.githubusercontent.com/GabrielReisR/R/master/estrutura%20de%20dados/dados/biblios.csv"
data <- read.csv(url)[,- 1]

# visualizando dados
head(data)
```

```
##   id           pais  regiao  gastos total_biblios total_trabalham total_volumes
## 1  1    Afghanistan    Asia      NA           150              NA        577300
## 2  2        Albania  Europe  134036          2107              32      12398795
## 3  3        Algeria  Africa  641176           760              56       4363718
## 4  4 American Samoa Oceania      NA            32              NA        170009
## 5  5        Andorra  Europe 5777651            39               6        222869
## 6  6         Angola  Africa      NA            62              NA        392870
##   total_usuarios
## 1          48133
## 2         581298
## 3           1430
## 4             NA
## 5          16596
## 6             NA
```

```r
# visualizando missings com o pacote Amelia
missmap(data)
```

<img src="/blog/2021-06-24-dplyr/tidyr-parte-2_files/figure-html/Limpando missings dataset com drop_na-1.png" width="672" />

Show! Entendemos então que nossas três variáveis com mais missings são:

* *gastos*: o quanto um país gastou com bibliotecas públicas, 
* *total_trabalham*: o número total de pessoas que eram bibliotecárias,
* *total_usuarios*: o número total de pessoas que usavam as bibliotecas.

Logo no início podemos fazer algo bem radical e excluir todas as linhas que contenham qualquer tipo de missing (*listwise deletion*). Fazemos isso usando a função `drop_na()` do pacote *tidyr*.


```r
data <- tidyr::drop_na(data) # excluindo com drop_na()

head(data) # primeiros casos
```

```
##   id      pais  regiao     gastos total_biblios total_trabalham total_volumes
## 1  2   Albania  Europe     134036          2107              32      12398795
## 2  3   Algeria  Africa     641176           760              56       4363718
## 3  5   Andorra  Europe    5777651            39               6        222869
## 4 10   Armenia    Asia     133148          2555            1430      30935507
## 5 12 Australia Oceania 1927384739         11052            9021     128150151
## 6 13   Austria  Europe  182404250          2695            3486      71909504
##   total_usuarios
## 1         581298
## 2           1430
## 3          16596
## 4       13615227
## 5       14477916
## 6        1559109
```

```r
nrow(data) # quantas linhas?
```

```
## [1] 140
```

Agora não temos mais nenhum missing em nenhum lugar. Entretanto, temos 79 casos a menos.

Digamos que ao invés de excluirmos todas as linhas que tivesse ao menos um missing, quiséssemos excluir qualquer linha que tenha missing na coluna *total_usuarios.* 

Para isso, bastaria mudar o código só um pouquinho para `tidyr::drop_na(data, total_usuarios)`

Quando especificamos uma coluna como argumento (`drop_na(banco_de_dados, coluna)`), todas as linhas que contém missing nessa coluna são eliminados.


```r
data <- read.csv(url)[,- 1] # relendo o banco inicial

data <- tidyr::drop_na(data, total_usuarios)

head(data) # mostra os primeiros casos
```

```
##   id        pais        regiao  gastos total_biblios total_trabalham
## 1  1 Afghanistan          Asia      NA           150              NA
## 2  2     Albania        Europe  134036          2107              32
## 3  3     Algeria        Africa  641176           760              56
## 4  5     Andorra        Europe 5777651            39               6
## 5  9   Argentina Latin America      NA          2537             658
## 6 10     Armenia          Asia  133148          2555            1430
##   total_volumes total_usuarios
## 1        577300          48133
## 2      12398795         581298
## 3       4363718           1430
## 4        222869          16596
## 5      28770630         787788
## 6      30935507       13615227
```

```r
nrow(data) # quantas linhas?
```

```
## [1] 180
```

Filtrando apenas pela coluna *total_usuarios*, o nosso banco fica com 180 casos.

## fill()

E se não quisermos eliminar colunas, mas sim preencher os valores?

Uma substituição possível de ser usada é a com a função `fill()` do pacote *tidyr*.

A solução provida por essa função é a de pegar os valores que não estão missings mais próximos para preencher os valores missings das variáveis. Não é uma solução muito adequada porque o critério é muito aleatório e pode não ser representativo daquele caso.


```r
data <- read.csv(url)[,- 1] # relendo o banco inicial

library(tidyr)
data <- data %>%
  fill(gastos,
       total_trabalham,
       total_usuarios,
       .direction = "up") # preenche de baixo PARA CIMA ('up'))

head(data) # mostra os primeiros casos
```

```
##   id           pais  regiao  gastos total_biblios total_trabalham total_volumes
## 1  1    Afghanistan    Asia  134036           150              32        577300
## 2  2        Albania  Europe  134036          2107              32      12398795
## 3  3        Algeria  Africa  641176           760              56       4363718
## 4  4 American Samoa Oceania 5777651            32               6        170009
## 5  5        Andorra  Europe 5777651            39               6        222869
## 6  6         Angola  Africa  133148            62             658        392870
##   total_usuarios
## 1          48133
## 2         581298
## 3           1430
## 4          16596
## 5          16596
## 6         787788
```

```r
nrow(data) # quantas linhas?
```

```
## [1] 219
```

Com `.direction = "up"`, os valores completos passaram para cima.

Por exemplo, em *gastos*, na primeira linha, Afghanistan recebeu o valor de *gastos* de Albania.

Agora vamos ver de cima PARA BAIXO. Para isso, precisamos especificar nosso parâmetro como `.direction = "down"`


```r
data <- read.csv(url)[,- 1] # relendo o banco inicial

library(tidyr)
data <- data %>%
  fill(gastos,
       total_trabalham,
       total_usuarios,
       .direction = "down") # preenche de baixo PARA CIMA ('up'))

head(data) # mostra os primeiros casos
```

```
##   id           pais  regiao  gastos total_biblios total_trabalham total_volumes
## 1  1    Afghanistan    Asia      NA           150              NA        577300
## 2  2        Albania  Europe  134036          2107              32      12398795
## 3  3        Algeria  Africa  641176           760              56       4363718
## 4  4 American Samoa Oceania  641176            32              56        170009
## 5  5        Andorra  Europe 5777651            39               6        222869
## 6  6         Angola  Africa 5777651            62               6        392870
##   total_usuarios
## 1          48133
## 2         581298
## 3           1430
## 4           1430
## 5          16596
## 6          16596
```

```r
nrow(data) # quantas linhas?
```

```
## [1] 219
```

Note que na primeira linha da coluna *gastos* é `NA`: isso porque não existe nenhum valor acima dela para a substituir.

Poderíamos também especificar outros dois valores em `.direction`:

* `.direction = "updown"`: primeiro vai para cima, depois para baixo, e assim por diante.
* `.direction = "downup"`: primeiro vai para baixo, depois para cima, e assim por diante.

## replace_na()
A última função do **tidyr** para tratar de missings se chama `replace_na()`. Ela recebe uma coluna ou um dataset e substitui por um valor que informamos em uma lista.

Para usar replace_na() basta nós colocarmos a lista das nossas variáveis junto com a função OU o valor que queremos para essas variáveis.


```r
data <- read.csv(url)[,- 1] # relendo o banco inicial

library(tidyr) # lendo tidyr

data <- data %>%
  replace_na(list(
    gastos = mean(data$gastos, na.rm = T),
    # NAs se tornam média
    total_trabalham = floor(mean(data$total_trabalham, na.rm = T)),
    # NAs se tornam média
    total_usuarios = floor(mean(data$total_usuarios, na.rm = T)) 
    # NAs se tornam média
  ))
  
head(data)
```

```
##   id           pais  regiao    gastos total_biblios total_trabalham
## 1  1    Afghanistan    Asia 357736161           150            5002
## 2  2        Albania  Europe    134036          2107              32
## 3  3        Algeria  Africa    641176           760              56
## 4  4 American Samoa Oceania 357736161            32            5002
## 5  5        Andorra  Europe   5777651            39               6
## 6  6         Angola  Africa 357736161            62            5002
##   total_volumes total_usuarios
## 1        577300          48133
## 2      12398795         581298
## 3       4363718           1430
## 4        170009        7426165
## 5        222869          16596
## 6        392870        7426165
```


Perceba que aqui é um pouco mais complicado. Isso porque precisamos:

* Definir nossa variável depois de abrir uma lista.
* Adicionar `=`e começar a especificar qual valor os missings dessa coluna receberão.
* Já que as duas últimas colunas da função contam o número de pessoas, adicionei na frente a função `floor()`, que arredonda esse valor para baixo.

Olhemos para o resultado agora. Basta olhar para *total_trabalham* para vermos que a média talvez não seja a melhor representação de tendência central dessa variável. O valor novo para as linhas que continham missing (1, 4 e 6) passou a ser 5002, o que não parece ser a realidade desses países.

Em gastos, o mesmo é percebido. Por exemplo, na linha 4, o país American Samoa tinha um missing em gastos que foi substituído pela média dessa coluna. O novo valor em dólares de *gastos* com as 32 bibliotecas desse país passou a ser de R$357.736.161. Um valor absurdamente maior do que o provável valor real.

Vimos o problema de se substituir pela média. Ela é facilmente influenciada por valores extremos.

Uma solução poderia ser substituir pela mediana. A função seria basicamente a mesma e o resultado bem diferente:


```r
data <- read.csv(url)[,- 1] # relendo o banco inicial

library(tidyr) # lendo tidyr

data <- data %>%
  replace_na(list(
    gastos = median(data$gastos, na.rm = T),
    # NAs se tornam média
    total_trabalham = floor(median(data$total_trabalham, na.rm = T)),
    # NAs se tornam média
    total_usuarios = floor(median(data$total_usuarios, na.rm = T)) 
    # NAs se tornam média
  ))

head(data)
```

```
##   id           pais  regiao  gastos total_biblios total_trabalham total_volumes
## 1  1    Afghanistan    Asia 1810863           150             175        577300
## 2  2        Albania  Europe  134036          2107              32      12398795
## 3  3        Algeria  Africa  641176           760              56       4363718
## 4  4 American Samoa Oceania 1810863            32             175        170009
## 5  5        Andorra  Europe 5777651            39               6        222869
## 6  6         Angola  Africa 1810863            62             175        392870
##   total_usuarios
## 1          48133
## 2         581298
## 3           1430
## 4         316202
## 5          16596
## 6         316202
```

O novo valor para os missings de *total_trabalham* agora é 175. O valor novo para as linhas que continham missing em gastos passou a ser R$1.810.863, o que parece estar mais adequado à realidade dos países que estamos vendo aqui. Já os missings de *total_usuarios* receberam o valor de 316.202, também mais condizente com os dados que estamos explorando.

Uma simples mudança da média para a mediana parece ter feito uma grande diferença aqui.

# Imputação por regressão

Podemos personalizar ainda mais os valores de missing. Vamos fazer agora com que cada linha receba um valor específico nos missings baseado nos valores de suas outras variáveis. 

Podemos fazer isso utilizando-se da regressão linear, um método que busca estimar os valores de y a partir de variáveis x.

Aqui, vamos estimar o valor de *gastos*, *total_trabalham* e *total_usarios* usando as outras variáveis numéricas do nosso banco. Quais são essas?

**Em todos os casos, possuímos dois valores numéricos que não se alteram: total_biblios e total_volumes.**

Fazendo uma rápida regressão linear (tópico para um próximo post no [PsicoData](https://medium.com/psicodata)) para cada coluna temos que em:

* gastos: conseguimos 37,83% de ajuste do nosso modelo,
* total_trabalham: conseguimos 65,01% de ajuste do nosso modelo,
* total_usuarios: conseguimos 16,45% de ajuste do nosso modelo.

Para fazer uma imputação simples com regressão, não vamos utilizar nenhum pacote, vamos criar a nossa própria função (o que aprendi a fazer com [esse ótimo capítulo sobre missings](http://www.stat.columbia.edu/~gelman/arm/missing.pdf)).


```r
# modelos de regressão
regressao_gastos <- lm(gastos ~ total_biblios + total_volumes - 1,
                       data = data)
regressao_trabalham <- lm(total_trabalham ~ total_biblios + total_volumes - 1,
                          data = data)
regressao_usuarios <- lm(total_usuarios ~ total_biblios + total_volumes - 1,
                         data = data)

# criando predição dos valores para cada coluna
gastos_pred <- predict(regressao_gastos, data) # coluna gastos
trabalham_pred <- predict(regressao_trabalham, data) # coluna gastos
usuarios_pred <- predict(regressao_usuarios, data) # coluna gastos

# criando a função
imputar <- function(coluna, pred){
  ifelse(is.na(coluna), pred, coluna)
}
```


* Criamos o modelo para cada coluna com a função `lm()`
  * Aqui, a primeira variável é a ser predita, e as outras são os valores que vamos utilizar para predizer a variável. No primeiro caso, quando escrevemos `gastos ~ total_biblios + total_volumes - 1` estamos querendo dizer que os valores de gastos serão preditos pelos valores das colunas total_biblios e total_volumes. O `- 1` significa que nós estamos retirando desse cálculo o valor do intercepto, que nesses casos eram bem significativos.
* Predizemos com esse modelo os valores de cada linha usando a função `predict()`
* Criamos a função `imputar()`
  * Essa função vai receber uma coluna, depois vai verificar se o valor em que ela está dessa coluna é um missing. A partir disso, se o valor for missing, o valor a ser imputado será o da fórmula que passamos para ela, se não o valor continuará igual.

Agora vamos usar tudo isso que fizemos para imputar esses valores no nosso banco de dados!


```r
# imputando valores
data$gastos <- imputar(data$gastos, gastos_pred)
data$total_trabalham <- floor(imputar(data$total_trabalham, trabalham_pred))
data$total_usuarios <- floor(imputar(data$total_usuarios, usuarios_pred))

head(data)
```

```
##   id           pais  regiao  gastos total_biblios total_trabalham total_volumes
## 1  1    Afghanistan    Asia 1810863           150             175        577300
## 2  2        Albania  Europe  134036          2107              32      12398795
## 3  3        Algeria  Africa  641176           760              56       4363718
## 4  4 American Samoa Oceania 1810863            32             175        170009
## 5  5        Andorra  Europe 5777651            39               6        222869
## 6  6         Angola  Africa 1810863            62             175        392870
##   total_usuarios
## 1          48133
## 2         581298
## 3           1430
## 4         316202
## 5          16596
## 6         316202
```

Por enquanto é isso! :)

---

# Mais informações
Organizando com tidyr - Parte 1: dados wide e long: https://rpubs.com/reisrgabriel/tidyrPt1

Manipulando com dplyr - Parte 1: select() e mutate(): https://rpubs.com/reisrgabriel/dplyrPt1

Manipulando com dplyr - Parte 2: bind() e join(): https://rpubs.com/reisrgabriel/dplyrPt2

Importação de dados e diagnósticos iniciais: https://rpubs.com/reisrgabriel/importdiagn

Para maiores informações sobre o tidyr: https://tidyr.tidyverse.org/ ou https://cran.r-project.org/web/packages/tidyr/tidyr.pdf
