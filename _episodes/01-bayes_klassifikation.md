---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 01-bayes_klassifikation.md in _episodes_rmd/
title: "Bayes klassifikation"
teaching: 42
exercises: 47
questions: 
- "Hvad er Bayes i relation til klassifikation"
objectives:
- "Forstå det her nok til at vi kan hjælpe studerende"

keypoints:
- "FIXME"
source: Rmd
math: yes
---



### Hvad er det?

Klassifikation baseret på Bayes teorem. 

Denne side skal muligvis deles op i to.

Bayes teorem fortæller os hvordan vi opdaterer vores overbevisning om 
noget - baseret på ny viden.

Der er noget nomenklatur vi nok skal have styr på:

P(B) er sandsynligheden for at hændelsen B sker.

P(A|B) er sandsynligheden for at hændelsen A sker, *givet* at hændelsen B sker.

P(A, B) er sandsynligheden for at hændelserne A *og* B sker.

Der er også nogle regneregler det kan være værd at have styr på:

$$P(A, B) = P(A|B)P(B)$$

$$P(A, B) = P(B, A)$$

$P(A|B) = \frac{P(A, B)}{P(B)}$

$P(A|B) \neq P(B|A)$

De regneregler kan vi bruge. Kender vi P(A|B), P(A) og P(B), kan 
vi finde ud af at:

$P(B|A) = \frac{P(A|B)P(B)}{P(A)}$

Vi kan også finde ud af at:

$P(!A) = 1 - P(A)$

Hvor P(!A) er sandsynligheden for at A ikke sker.

Og at 

$P(B|!A) = 1 - P(!B|!A)$

Altså at sandsynligheden for at B sker, hvis A *ikke* sker, er 1 minus
sandsynligheden for at B ikke sker, når A heller ikke sker.

Og endelig kan vi beregne P(B), hvis vi kender P(B|A) og P(A):

$P(B) = P(B|A)*P(A) + P(B|!A)*P(!A)$


Nu kan vi forstå hvad Bayes teorem egentlig siger:

$P(A|B) = \frac{P(B|A)P(A)}{P(B)}$

Og hvad bruger vi så det til? Jo. Før B sker, har vi et bud på hvad sandsynligheden er for at A sker. 

Når B er sket, har vi en opdateret sandsynlighed. Vi forventer at sandsynligheden for at toget er forsinket til at være 20% (P(A) = 0.2). 
Nu falder der blade på skinnerne. Hvad er nu vores bud på hvor forsinket toget er?

P(A) er vores *prior*, det vi mener *før* B sker. P(A|B) er vores *posterior*, det vi mener om A, *efter* B er sket.

Der går sjældent røg af en brand, uden at der er ild i den.

Hvad er sandsynligheden for at der er brand, hvis der er røg?

Vi mener at sandsynligheden for at der er ild er 2% (P(A)). 
Sandsynligheden for at der er røg i det hele taget er 5% (P(B)). Og sandsynligheden for at der er røg, hvis der er ild er 80% (P(B|A)).

Hvis vi observerer røg, opdaterer vi vores bud på hvad sandsynligheden er for at der er ild:

$P(ild|røg) = \frac{P(røg|ild)P(ild)}{P(røg)} = \frac{0.8*0.02}{0.05} = 0.32  = 32\%$

Før vi observerede røg, mente vi sandsynligheden for at der var ild var 2%. Nu ser vi røg. Så nu opdaterer vi vores bud på sandsynligheden for ild til at være 32%

### Hvor er det ellers nyttigt?

Folk har en tilbøjelighed til at vurdere sandsynligheder forkert. Sandsynligheden for at jeg har COVID-19, er 1%. Jeg bliver testet med en test der er 95% sikker (som i - hvis den er positiv, er der 95% sandsynlighed for at jeg har COVID-19).

Så når jeg får stukket vatpinden i næsen, er gættet på om jeg har COVID-19 1%. Når resultatet kommer positivt tilbage, hvad er så bedste gæt på om jeg har COVID-19?

Min posterior, sandsynligheden for at jeg har COVID, P(A) = 0.01. 
Sandsynligheden for en positiv test, *hvis* jeg *har* COVID: P(B|A) 0.95
P(B|A), altså sandsynligheden for at jeg får en positiv test, givet at jeg *har* COVID-19, er 0.95.

Det vil være:

$P(A|B) = \frac{P(B|A)P(A)}{P(B)} = \frac{0.95 * 0.01}{P(B)}$

Vi kender ikke P(B), sandsynligheden for at testen er negativ. Uanset om jeg har COVID eller ej. Men vi kan bruge regnereglerne ovenfor til at beregne det:

$P(B) = P(B|A)*P(A) + P(B|!A)*P(!A)$

Eller:

$P(B) = 0.95*0.01 + P(B|!A)*P(!A)$

Vi ved at:

$P(!A) = 1 - P(A) = 1- 0.01 = 0.99$

Det sætter vi ind, og får:

$P(B) = 0.95*0.01 + P(B|!A)*0.99$

P(B|!A) kender vi ikke. Det er de falsk positive. Der hvor testen er positiv, selvom patienten er negativ. Lad os sætte den til 5%

Lad os sætte det ind:

$P(B) = 0.95*0.01 + 0.05*0.99 = 0.059$

Det sætter vi også ind i vores oprindelige formel:

$P(A|B) = \frac{P(B|A)P(A)}{P(B)} = \frac{0.95 * 0.01}{P(B)} = \frac{0.95 * 0.01}{0.059} = 0.161 = 16%$

Efter den positive test, opdaterer jeg derfor min vurdering af om jeg har COVID fra 1% til 16%. 


Hvordan klassificerer vi så med Bayes?

Naiv bayes antager at de prediktive variable er uafhængige af hinanden.

Der er mange implementeringer af Naiv Bayes. En af dem finder vi i pakken e1071.

Lad os kigge på pingviner. 


~~~
library(palmerpenguins)
library(tidymodels)
~~~
{: .language-r}



~~~
── Attaching packages ────────────────────────────────────── tidymodels 1.0.0 ──
~~~
{: .output}



~~~
✔ broom        1.0.4     ✔ recipes      1.0.5
✔ dials        1.1.0     ✔ rsample      1.1.1
✔ dplyr        1.1.0     ✔ tibble       3.2.0
✔ ggplot2      3.4.1     ✔ tidyr        1.3.0
✔ infer        1.0.4     ✔ tune         1.0.1
✔ modeldata    1.1.0     ✔ workflows    1.1.3
✔ parsnip      1.0.4     ✔ workflowsets 1.0.0
✔ purrr        1.0.1     ✔ yardstick    1.1.0
~~~
{: .output}



~~~
── Conflicts ───────────────────────────────────────── tidymodels_conflicts() ──
✖ purrr::discard() masks scales::discard()
✖ dplyr::filter()  masks stats::filter()
✖ dplyr::lag()     masks stats::lag()
✖ recipes::step()  masks stats::step()
• Search for functions across packages at https://www.tidymodels.org/find/
~~~
{: .output}



~~~
library(caret)
~~~
{: .language-r}



~~~
Loading required package: lattice
~~~
{: .output}



~~~

Attaching package: 'caret'
~~~
{: .output}



~~~
The following objects are masked from 'package:yardstick':

    precision, recall, sensitivity, specificity
~~~
{: .output}



~~~
The following object is masked from 'package:purrr':

    lift
~~~
{: .output}



~~~
library(e1071)
~~~
{: .language-r}



~~~

Attaching package: 'e1071'
~~~
{: .output}



~~~
The following object is masked from 'package:tune':

    tune
~~~
{: .output}



~~~
The following object is masked from 'package:rsample':

    permutations
~~~
{: .output}



~~~
The following object is masked from 'package:parsnip':

    tune
~~~
{: .output}



~~~
penguin_sample <- sample(c(TRUE, FALSE), nrow(penguins), replace=TRUE, prob=c(0.8,0.2))
penguin_train  <- penguins[penguin_sample, ]
penguin_test   <- penguins[!penguin_sample, ]

penguin_model <- naiveBayes(species~., penguin_train)

penguin_predictions <- predict(penguin_model, newdata = penguin_test) 

confusionMatrix(penguin_predictions, penguin_test$species)
~~~
{: .language-r}



~~~
Confusion Matrix and Statistics

           Reference
Prediction  Adelie Chinstrap Gentoo
  Adelie        36         1      0
  Chinstrap      0         8      0
  Gentoo         0         0     23

Overall Statistics
                                          
               Accuracy : 0.9853          
                 95% CI : (0.9208, 0.9996)
    No Information Rate : 0.5294          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9747          
                                          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: Adelie Class: Chinstrap Class: Gentoo
Sensitivity                 1.0000           0.8889        1.0000
Specificity                 0.9688           1.0000        1.0000
Pos Pred Value              0.9730           1.0000        1.0000
Neg Pred Value              1.0000           0.9833        1.0000
Prevalence                  0.5294           0.1324        0.3382
Detection Rate              0.5294           0.1176        0.3382
Detection Prevalence        0.5441           0.1176        0.3382
Balanced Accuracy           0.9844           0.9444        1.0000
~~~
{: .output}

### Hvornår bruger man Naiv Bayes i stedet for andre klassifikationer?

Lærebøgerne fortæller at Bayes har en fordel ved at håndtere både kontinuert og diskret data i et hug. Men er bedst til kategoriske data.  At den skalerer ret godt til større datasæt. Og klarer sig ret godt med mindre træningsdata end andre metoder. 

Forudsætningen er dog at de features der fittes på er uafhængige af hinanden.

