---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 02-kappa.md in _episodes_rmd/
title: "Cohens Kappa"
teaching: 0
exercises: 0
questions: 
- "FIXME"

objectives:
- "FIXME"

keypoints:
- "FIXME"
source: Rmd
math: TRUE
---






## TL;DR

Bruges til at måle om kategorisering af ting sker pålideligt.

To "raters" rater noget. Det kan være en vurdering af farve eller andet 
godt kategorisk, kvalitativt, materiale.

Skal et udsagn i et spørgeskema vurderes positivt eller negativt?

Svært at afgøre. For at sikre os at mine personlige ideosynkrasier ikke farver
datamaterialet (unødigt), sætter vi en kollega til, uafhængigt af mig, at vurdere
materialet.

Hvor godt gik det? Var vi enige?

Man kunne beregne hvor mange procent af datapunkterne vi var enige om.

Men det tager ikke højde for at vi måske er enige ved et tilfælde.

Så bruger vi Cohens Kappa til at give et bedre mål.

cohens kappa kan bruges direkte for to kategoriske variable. Enten to nominale 
eller to ordinale variable.

Der findes varianter.

Vægtet kappa (weighted kappa), der kun kan bruges for ordinale variable
Light's kappa, Håndterer når vi har mere end to kategoriske variable -
snittet af alle mulige two-raters cohens kappa.
Fleiss kappa. en tilpasning af cohens kappa, for n raters, hvor n kan være to eller
flere.

## den korte forklaring
funktionen kan findes i biblioteket vcd

~~~
# install.packages("vcd")
library(vcd)
~~~
{: .language-r}



~~~
Loading required package: grid
~~~
{: .output}



~~~
library(tidyverse)
~~~
{: .language-r}

Vi skal have data i en matrix:

Vi har 30 observationer, to raters, og de kategoriske muligheder
dep, dis, neu, sch, oth



~~~
diagnoses <- tribble(~rater1, ~rater2,
  "dep", "dep",
  "dep", "dep",
  "dep", "dep",
  "dep", "dep",
  "dep", "dep",
  "dep", "dep",
  "dep", "dep",
  "dep", "dis",
  "dep", "sch",
  "dep", "sch",
  "dep", "neu",
  "dep", "neu",
  "dep", "neu",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "dis",
  "dis", "sch",
  "dis", "sch",
  "sch", "sch",
  "sch", "sch",
  "neu", "neu",
  "oth", "oth",
  "oth", "oth",
  "oth", "oth",
  "oth", "oth"
  )

tabel <- table(diagnoses)
tabel
~~~
{: .language-r}



~~~
      rater2
rater1 dep dis neu oth sch
   dep   7   1   3   0   2
   dis   0   8   0   0   2
   neu   0   0   1   0   0
   oth   0   0   0   4   0
   sch   0   0   0   0   2
~~~
{: .output}


~~~
res.k <- Kappa(tabel)
res.k
~~~
{: .language-r}



~~~
            value    ASE     z  Pr(>|z|)
Unweighted 0.6507 0.0999 6.513 7.372e-11
Weighted   0.5588 0.1282 4.359 1.304e-05
~~~
{: .output}
et 95% konfidensinterval (pil ved argumenter hvis du vil have et andet)

~~~
confint(res.k)
~~~
{: .language-r}



~~~
            
Kappa              lwr       upr
  Unweighted 0.4548491 0.8464610
  Weighted   0.3075771 0.8100699
~~~
{: .output}
Er det godt? Den uvægtede Kappa for vores ratere er 0.65. [Fleiss et al (2003)](https://onlinelibrary.wiley.com/doi/book/10.1002/0471445428), mener at vide,
at 



|value             |Beskrivelse                          |
|:-----------------|:------------------------------------|
|>0.75             |Excellent agreement beyond chance    |
|0.4> value < 0.75 |Fair to good agreement beyond chance |
|<0.4              |Poor agreement beyond chance         |


[McHugh (2012)](https://doi.org/10.11613/BM.2012.031)
har en tabel:


|Value of K  |Level of agreement | % of data that are reliable|
|:-----------|:------------------|---------------------------:|
|0 - 0.20    |None               |                      0 - 4%|
|0.21 - 0.39 |Minimal            |                     4 - 15%|
|0.40 - 0.59 |Weak               |                    15 - 35%|
|0.60 - 0.79 |Moderate           |                    35 - 63%|
|0.80 - 0.90 |Strong             |                    64 - 81%|
|Above 0.90  |Almost Perfect     |                   82 - 100%|
Men den er nok lidt kæk i sine vurderinger. 61% enighed betragtes som godt.
Det er ofte ikke helt godt nok.
Så overvej nøje hvad den faktisk bruges til. I et klinisk laboratorium er det 
ikke så godt hvis 40% af vurderinger er forkerte.

## forudsætninger

For Cohens kappa:
To outcome categorical variables - ordinal eller nominal

De to outcome variable skal have præcis de samme kategorier

Parrede observationer. Hver ting skal være kategoriseret to gange, af to
uafhængige raters (eller metoder)

De samme to raters skal bruges for alle deltagere.

Hypoteser:
Null hypotesen, kappa = 0, hvis der  enighed, er det tilfældigt.
Den alternative hypotese, kappa != 0, Enigheden er forskellig fra tilfældigheder.

## Den tekniske forklaring
den er defineret somg P0 - Pe / (1-Pe)
P0 er andelen af observeret enighed
Pe er andelen af tilfældig enighed.

Når man ser hvordan den beregnes, lugter det af chi i anden testen. 
Det er ikke et tilfælde:
Feingold, Marcia (1992). "The Equivalence of Cohen's Kappa and Pearson's Chi-Square Statistics in the 2 × 2 Table." Educational and Psychological Measurement 52(1): 57-61. <http://hdl.handle.net/2027.42/67443>

Hvis der er to raters og to udfald, er test statistikken for kappa den samme
som for Pearsons chi i anden. Så de er i familie.

Anyway, definitionen betyder at værdierne kan gå fra -1 til 1. Hvis den er 0,
er enigheden ikke bedre end hvad vi ville få tilfældigt. Ved negative værdier
er den mindre end hvad vi ville få tilfældigt. Hvis positiv er enigheden større
end ved tilfældighed.



~~~
##        Doctor2
## Doctor1 Yes No Total
##   Yes   a   b  R1   
##   No    c   d  R2   
##   Total C1  C2 N
~~~
{: .language-r}

a, b, c og d er de observerede, talte, værdier
$R_1 = a + b$ 
$R_2 = c + d$
$C_1 = a + c$
$C_2 = b + d$
og

ad $N = a + b + c + d$ adf 



$$P_0 = \frac{a + c}{N}$$

Det var den lette.

$P_e$ finder vi ved at finde sandsynligheden for at begge raters tilfældigt siger
yes.
Doctor1 siger Ja til R1/N tilfælde.
Doctor2 siger Ja til C1/N tilfælde
Sandsynligheden for at de tilfældigt begge siger ja er 
$R1/N * C1/N$


Så finder vi sandsynligheden for at de begge tilfældigt siger nej
Doctor1 siger nej til R2/N
Doctor2 siger nej til C2/N
Sandysnligheden for at de begge tilfældigt siger nej er:
$R2/N * C2/N$

Sandsynligheden for at de er enige er derfor:
$R1/N * C1/N + R2/N * C2/N$

Så har vi Pe. Og kan beregne Kappa som:
$\kappa = ((a+d)/N - (R1/N * C1/N + R2/N * C2/N))   / 1 - (R1/N * C1/N + R2/N * C2/N)$

Det bliver noget mere langt, men ikke nødvendigvis langhåret, hvis der er 
mere end en kategori.

Der er en formel for SE. 

$SE_\kappa = \frac{P_e + P_2^2 - \sum_i P_{i+}P_{+i}(P_{i+} + P_{+i})}{N(1+ P_e)^2}$

Og så kan der beregnes konfidensintervaller på sædvanligvis efter normalfordelingen:

$\kappa +- Z_{\alpha/2} SE_\kappa$

i+ og +i er række og kolonne summerne.

Gider vi gøre det? Nej, det gider vi ikke.
Der er en funktion.

## Tolkning

## Andet

Hvor bruges det? Blandt andet inden for psykiatri/psykologi. Visse kliniske
observationer kan pege på "personlighedsforstyrrelse" eller "Neurose". Hvilken
af de to diagnoser der stilles afhænger i ret høj grad af et skøn. Derfor får 
vi to uafhængige læger til at stille diagnosen.

Når de gør de i større omfang - er de så enige?

Men den bruges også til meget andet.