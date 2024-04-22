![image](https://github.com/AlbertoFAraujo/R_anova_ensino/assets/105552990/c8475fb2-cf16-453e-a9c2-401efee3b604)

### Tecnologias utilizadas: 
| [<img align="center" alt="R_studio" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/02dff6df-07be-43dc-8b35-21d06eabf9e1">](https://posit.co/download/rstudio-desktop/) | 
|:---:|
| R Studio |
- **RStudio:** Ambiente integrado para desenvolvimento em R, oferecendo ferramentas para escrita, execução e depuração de código;

<hr>

### Objetivo: 

Através da Análise de Variância (ANOVA) analisar três métodos de ensino, identificando se há diferenças significativas ou não entre os métodos abordados. Apresentar os resultados de forma manual e posteriormente utilizando funções do R.

Base de dados: Sintética
<hr>

### Script R: 
**Enunciado:**

Um professor deseja testar três métodos de ensinos diferentes. Para isso, escolhe aleatoriamente três grupos de 5 alunos e cada grupo é ensinado por um método diferente. Determine se há diferença significativa entre os três métodos, com nível de significância de 5%

| Métodos  |    |  |    |    |    |
|----------|-----|-----|-----|-----|-----|
| Método 1 | 75  | 62  | 71  | 58  | 73  |
| Método 2 | 81  | 85  | 68  | 92  | 90  |
| Método 3 | 73  | 79  | 60  | 75  | 81  |

------------------------------------------------------------------------

### Resolução Manual

-   Número de observações (n) = 15

-   Número de tratamentos (c) = 3

1° Hipóteses:

-   H0: média_1 = média_2 = média_3

-   Ha: Ao menos uma das medidas são diferentes

```r
# Criação da tabela
metodo_1 <- list(75,62,71,58,73)
metodo_2 <- list(81,85,68,92,90)
metodo_3 <- list(73,79,60,75,81)

df <- data.frame(
  'Metodo_1'= unlist(metodo_1),
  'Metodo_2'= unlist(metodo_2),
  'Metodo_3'= unlist(metodo_3)
)
df <- t(df)
df
```
```r
# Média dos grupos
media_1 <- mean(df['Metodo_1',])
media_2 <- mean(df['Metodo_2',])
media_3 <- mean(df['Metodo_3',])

sprintf('A média do método 1 é: %.2f', media_1)
sprintf('A média do método 2 é: %.2f', media_2)
sprintf('A média do método 3 é: %.2f', media_3)
```

[1] "A média do método 1 é: 67.80"

[1] "A média do método 2 é: 83.20"

[1] "A média do método 3 é: 73.60"

```r
# Média Global
media_global <- mean(df)
sprintf('A média global é: %.2f', media_global)
```
[1] "A média global é: 74.87"

```r
# Variância Total
STQ <- 0

for (i in df) {
  STQ <- STQ + (i - media_global)**2
}
sprintf('A variância total é STQ: %.2f', STQ)
```
[1] "A variância total é STQ: 1457.73"

```r
nj = ncol(df) # Número de notas distintas
media_grupos <- c(media_1, media_2, media_3)

# Variação ENTRE grupo
SQE <- 0

for (i in media_grupos) {
  SQE <- SQE + nj * (i - media_global)**2
}

sprintf('A variância ENTRE os grupos é SQE: %.2f', SQE)

# Média ENTRE grupos
MQE <- SQE/(nrow(df)-1)
sprintf('A média ENTRE os grupos é MQE: %.2f', MQE)
```
[1] "A variância ENTRE os grupos é SQE: 604.93"

[1] "A média ENTRE os grupos é MQE: 302.47"

```r
# Variação DENTRO do grupo
SQD <- STQ - SQE # Variação total - variação entre grupos
sprintf('A variação DENTRO dos grupos é SQD: %.2f', SQD)

# Média DENTRO do grupo
MQD <- SQD/(length(df) - nrow(df))
sprintf('A média DENTRO dos grupos é MQD: %.2f', MQD)
```
[1] "A variação DENTRO dos grupos é SQD: 852.80"

[1] "A média DENTRO dos grupos é MQD: 71.07"

```r
# F observado
Fo <- MQE/MQD
sprintf('A Frequência observada é Fo: %.2f', Fo)
```
[1] "A Frequência observada é Fo: 4.26"

![clipboard-1933225919](https://github.com/AlbertoFAraujo/R_anova_ensino/assets/105552990/e0aab86b-a2f0-4cd7-977a-b638cbf4858a)

Com todos os valores determinados, podemos prosseguir com a criação da tabela auxiliar.

Para N sendo o número total de notas e k o número de tratamentos, temos:

Graus de liberdade entre os grupos: 3 - 1;

Graus de liberdade dentro dos grupos: 15 - 3 = 12

| FV     | SQ           | GL | MQ          | Fo     |
|--------|--------------|----|-------------|--------|
| Entre  | SQE = 604,93 | 2  | MQE = 302,46| Fo = 4,26|
| Dentro | SQD = 852,80 | 12 | MQD = 71,00 | -     |
| Total  | STQ = 1457,73| 14 | -          | -     |

Para calcular a Fc (Frequência calculada) utilizaremos a tabela abaixo:
![clipboard-3649464749](https://github.com/AlbertoFAraujo/R_anova_ensino/assets/105552990/e5c27d93-4b20-4859-b045-2fedb0b10a99)

Para Coluna 2 x Linha 12 (conforme os graus de liberdades determinados)

Fc = 3,88.

****
### Aplicação de TUKEY
Como o método de ANOVA informa se existe ou não diferença entre os tratamentos, mas não informa qual (is) tratamentos estão diferindo dos outros, utilizaremos então o método TUKEY para determinar essa relação entre os tratamentos.


Como Fo \> Fc, portanto **Rejeitamos a hipótese nula (Ho)**, ou seja, para o nível de significância de 5% provamos estatísticamente que há diferença significativa entre os métodos de ensino.

```r
q <- 3.77
delta <- q * (sqrt(MQD)/sqrt(nj))
sprintf('Amplitude total estudentizada q: %.2f', delta)
```
[1] "Amplitude total estudentizada q: 14.21"
![clipboard-2842578320](https://github.com/AlbertoFAraujo/R_anova_ensino/assets/105552990/2f5f2350-6270-437d-aedf-33a1eaeff478)
```r
# Contraste (em módulo)
met_1_2 <- abs(media_1 - media_2)
met_1_3 <- abs(media_1 - media_3)
met_2_3 <- abs(media_2 - media_3)

sprintf('Método 1 x Método 2: %.2f', met_1_2)
sprintf('Método 1 x Método 3: %.2f', met_1_3)
sprintf('Método 2 x Método 3: %.2f', met_2_3)
```
[1] "Método 1 x Método 2: 15.40"

[1] "Método 1 x Método 3: 5.80"

[1] "Método 2 x Método 3: 9.60"

**Conclusão:**
Os métodos 1 e 2 são diferentes significadamente, os demais não há efeito, ou seja, não há diferença significativa.

------------------------------------------------------------------------
### Resolução com funções R
```r
# Ajuste dos dados
met_1 <- data.frame('Metodos' = rep('Met1',5),'Obs'= df[1,])
met_2 <- data.frame('Metodos' = rep('Met2',5),'Obs'= df[2,])
met_3 <- data.frame('Metodos' = rep('Met3',5),'Obs'= df[3,])

# Unindo por linhas
df_r <- rbind(met_1, met_2, met_3)
df_r
```
| Metodos | Obs |
|---------|-----|
| Met1    | 75  |
| Met1    | 62  |
| Met1    | 71  |
| Met1    | 58  |
| Met1    | 73  |
| Met2    | 81  |
| Met2    | 85  |
| Met2    | 68  |
| Met2    | 92  |
| Met2    | 90  |
| Met3    | 73  |
| Met3    | 79  |
| Met3    | 60  |
| Met3    | 75  |
| Met3    | 81  |

```r
# Aplicação do ANOVA
anova_teste <- aov(Obs ~ Metodos, data = df_r)
summary(anova_teste)
```
 Df Sum Sq Mean Sq F value Pr(>F)  
Metodos      2  604.9  302.47   4.256 0.0401 *
Residuals   12  852.8   71.07                 

Signif. codes:  
0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

```r
# Aplicação do TUKEY
df_tukey <- TukeyHSD(anova_teste)
df_tukey
```
 Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = Obs ~ Metodos, data = df_r)

| $Metodos | diff | lwr        | upr       | p adj     |
|----------|------|------------|-----------|-----------|
| Met2-Met1| 15.4 |  1.175841  | 29.624159 | 0.0338349 |
| Met3-Met1|  5.8 | -8.424159  | 20.024159 | 0.5391163 |
| Met3-Met2| -9.6 | -23.824159 |  4.624159 | 0.2109028 |

Onde:

-   **diff**: Indica a diferença média entre os grupos comparados.

-   **lwr**: É o limite inferior do intervalo de confiança para a diferença entre as médias dos grupos.

-   **upr**: É o limite superior do intervalo de confiança para a diferença entre as médias dos grupos.

-   **p adj**: É o valor-p ajustado para múltiplas comparações.

**Conclusão:**

1.  Para a comparação entre Met2 e Met1, a diferença média é de 15.4, com um intervalo de confiança de 1.176 a 29.624. O valor-p ajustado é 0.0338, o que sugere **uma diferença significativa entre Met2 e Met1**.

2.  Para a comparação entre Met3 e Met1, a diferença média é de 5.8, com um intervalo de confiança de -8.424 a 20.024. O valor-p ajustado é 0.5391, indicando que **não há diferença significativa entre Met3 e Met1**.

3.  Para a comparação entre Met3 e Met2, a diferença média é de -9.6, com um intervalo de confiança de -23.824 a 4.624. O valor-p ajustado é 0.2109, novamente indicando que **não há diferença significativa entre Met3 e Met2**.

Em resumo, com base nos resultados do teste de Tukey, parece haver uma diferença significativa entre Met2 e Met1, mas não entre Met3 e Met1 ou entre Met3 e Met2.
