GEE Model
================
Roxy Zhang
4/11/2022

``` r
data = read_excel("HW8-HEALTH.xlsx") %>% 
  janitor::clean_names() %>% 
  mutate(health = ifelse(health == "Good", 1, 0)) # recode "Good" as 1, "Poor" as 0

dim(data)
```

    ## [1] 279   5

``` r
summary(data)
```

    ##        id           time          txt                health      
    ##  Min.   :101   Min.   :1.00   Length:279         Min.   :0.0000  
    ##  1st Qu.:120   1st Qu.:1.00   Class :character   1st Qu.:0.0000  
    ##  Median :140   Median :2.00   Mode  :character   Median :1.0000  
    ##  Mean   :287   Mean   :2.33                      Mean   :0.6237  
    ##  3rd Qu.:605   3rd Qu.:3.00                      3rd Qu.:1.0000  
    ##  Max.   :625   Max.   :4.00                      Max.   :1.0000  
    ##    agegroup        
    ##  Length:279        
    ##  Class :character  
    ##  Mode  :character  
    ##                    
    ##                    
    ## 

## a)

``` r
data_a = data %>% 
  filter(time == 1)

a = glm(health ~ txt, family = binomial, data = data_a)

summary(a)
```

    ## 
    ## Call:
    ## glm(formula = health ~ txt, family = binomial, data = data_a)
    ## 
    ## Deviance Residuals: 
    ##    Min      1Q  Median      3Q     Max  
    ## -1.157  -1.157  -1.028   1.198   1.335  
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error z value Pr(>|z|)
    ## (Intercept)     -0.04879    0.31244  -0.156    0.876
    ## txtIntervention -0.31412    0.45122  -0.696    0.486
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 110.10  on 79  degrees of freedom
    ## Residual deviance: 109.62  on 78  degrees of freedom
    ## AIC: 113.62
    ## 
    ## Number of Fisher Scoring iterations: 4

## b)

``` r
data_b = data %>% 
  mutate(baseline = ifelse(health == 1, 1, 0)) %>% 
  filter(time != 1)

b = gee(health ~ txt + time + as.factor(agegroup),
    id = id,
    data = data_b,
    family = "binomial",
    corstr = "unstructured",
    scale.fix = TRUE,
    scale.value = 1)
```

    ## Beginning Cgee S-function, @(#) geeformula.q 4.13 98/01/27

    ## running glm to get initial regression estimate

    ##              (Intercept)          txtIntervention                     time 
    ##               -0.9551595                1.7596909                0.1732759 
    ## as.factor(agegroup)25-34   as.factor(agegroup)35+ 
    ##                1.1257614                0.8470369

``` r
summary(b)
```

    ## 
    ##  GEE:  GENERALIZED LINEAR MODELS FOR DEPENDENT DATA
    ##  gee S-function, version 4.13 modified 98/01/27 (1998) 
    ## 
    ## Model:
    ##  Link:                      Logit 
    ##  Variance to Mean Relation: Binomial 
    ##  Correlation Structure:     Unstructured 
    ## 
    ## Call:
    ## gee(formula = health ~ txt + time + as.factor(agegroup), id = id, 
    ##     data = data_b, family = "binomial", corstr = "unstructured", 
    ##     scale.fix = TRUE, scale.value = 1)
    ## 
    ## Summary of Residuals:
    ##         Min          1Q      Median          3Q         Max 
    ## -0.93184489 -0.34735709  0.08869142  0.30712401  0.65264291 
    ## 
    ## 
    ## Coefficients:
    ##                            Estimate Naive S.E.    Naive z Robust S.E.
    ## (Intercept)              -0.9163386  0.5742471 -1.5957218   0.5923957
    ## txtIntervention           1.8017808  0.4757016  3.7876278   0.4987547
    ## time                      0.1428309  0.1633492  0.8743903   0.1748229
    ## as.factor(agegroup)25-34  1.1586144  0.4758163  2.4350038   0.5048907
    ## as.factor(agegroup)35+    0.9033525  0.7927441  1.1395260   0.6918614
    ##                            Robust z
    ## (Intercept)              -1.5468353
    ## txtIntervention           3.6125593
    ## time                      0.8170033
    ## as.factor(agegroup)25-34  2.2947828
    ## as.factor(agegroup)35+    1.3056842
    ## 
    ## Estimated Scale Parameter:  1
    ## Number of Iterations:  4
    ## 
    ## Working Correlation
    ##           [,1]      [,2]      [,3]
    ## [1,] 1.0000000 0.4280426 0.4738533
    ## [2,] 0.4280426 1.0000000 0.3807954
    ## [3,] 0.4738533 0.3807954 1.0000000

## c)

``` r
c = glmer(health ~ txt + time + as.factor(agegroup) + (1|id),
          family = "binomial",
          data = data_b)

summary(c)
```

    ## Generalized linear mixed model fit by maximum likelihood (Laplace
    ##   Approximation) [glmerMod]
    ##  Family: binomial  ( logit )
    ## Formula: health ~ txt + time + as.factor(agegroup) + (1 | id)
    ##    Data: data_b
    ## 
    ##      AIC      BIC   logLik deviance df.resid 
    ##    194.6    214.4    -91.3    182.6      193 
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -1.69294 -0.28461  0.08957  0.24967  1.63295 
    ## 
    ## Random effects:
    ##  Groups Name        Variance Std.Dev.
    ##  id     (Intercept) 7.906    2.812   
    ## Number of obs: 199, groups:  id, 78
    ## 
    ## Fixed effects:
    ##                          Estimate Std. Error z value Pr(>|z|)   
    ## (Intercept)               -1.6661     1.1559  -1.441  0.14945   
    ## txtIntervention            3.5946     1.1343   3.169  0.00153 **
    ## time                       0.2425     0.3105   0.781  0.43482   
    ## as.factor(agegroup)25-34   2.2995     1.0251   2.243  0.02488 * 
    ## as.factor(agegroup)35+     1.4030     1.4834   0.946  0.34426   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##             (Intr) txtInt time   a.()25
    ## txtIntrvntn -0.411                     
    ## time        -0.746  0.058              
    ## as.f()25-34 -0.409  0.273  0.000       
    ## as.fct()35+ -0.253  0.101 -0.010  0.326
