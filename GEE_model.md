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
