<center><h1>Homework3</h1></center>

<center>FAN Yifei</center>

> All plots are not shown in this report in form of paper, if you want to see the plots, please visit https://algebra-fun.github.io/MSDM5053/Homework/hw3/

> Note all tests use the `5%` significance level for type-I error and use `10` lags in all ACF or ARCH-effect tests.

## 1. SBUX returns

### a. Is there any serial correlation in the log returns of Starbucks stock?

Using Ljung-Box test, the output is as follows:

```
    Box-Ljung test

data:  sbux_data
X-squared = 19.823, df = 10, p-value = 0.03098
```

Using the $\alpha=0.05$ significance level, the $p=0.03 < \alpha$, then reject the null hypothesis. 

Thus, claim that there is serial correlation in the log return of SBUX data.

### b. Is there any ARCH effect in the log returns of Starbucks stock?

Using Ljung-Box test, the output is as follows:

```
    Box-Ljung test

data:  sbux_at^2
X-squared = 59.142, df = 10, p-value = 5.265e-09
```

Using the $\alpha=0.05$ significance level, the $p=5.27e-9 << \alpha$, then reject the null hypothesis. 

Thus, claim that there is ARCH correlation in the log return of SBUX data.

### c. Fit a GARCH(1,1) model for the percentage log return of Starbucks stock using normal distribution for the innovations. Perform model checking and write down the fitted model.

Using `garchFit` in r with the following code:

```r
sbux_garch = garchFit(~garch(1,1),data=sbux_at,trace = F,include.mean = F)
summary(sbux_garch)
```

The summary is as follows:

```
Title:
 GARCH Modelling 

Call:
 garchFit(formula = ~garch(1, 1), data = sbux_at, include.mean = F, 
    trace = F) 

Mean and Variance Equation:
 data ~ garch(1, 1)

Conditional Distribution:
 norm 

Coefficient(s):
   omega    alpha1     beta1  
0.015660  0.019164  0.976656  

Error Analysis:
        Estimate  Std. Error  t value Pr(>|t|)    
omega   0.015660    0.006521    2.401   0.0163 *  
alpha1  0.019164    0.003855    4.971 6.66e-07 ***
beta1   0.976656    0.004326  225.781  < 2e-16 ***

Standardised Residuals Tests:
                                Statistic p-Value        
 Ljung-Box Test     R    Q(10)  11.17972  0.3436884
 Ljung-Box Test     R^2  Q(10)  4.404105  0.9272815
```

#### The **formula** for the GARCH(1,1) is as follows:

$$
\begin{aligned}
r_t &= 0.0772 + a_t\\
a_t &= \sigma_t \epsilon_t\\
\sigma_t^2&=0.0157+0.0192 a_{t-1}^2+0.9767\sigma_{t-1}^2
\end{aligned}
$$

#### Model Checking

Using Ljung-Box test, the output is as follows:

```
    Box-Ljung test

data:  stresi
X-squared = 11.18, df = 10, p-value = 0.3437

    Box-Ljung test

data:  stresi^2
X-squared = 4.4041, df = 10, p-value = 0.9273
```

Using the $\alpha=0.05$ significance level, the $p=0.34 > \alpha$ for the test on the standardized residuals and $p=0.93 > \alpha$ for the test on the squre of the standardize residuals, then not reject the null hypothesis that the standardized residuals $\epsilon_t$ are uncorrelated. 

Thus, the model satisfies the assumption the standardized residuals $\epsilon_t$ are uncorrelated and obeys a normal distribution. And we claim that the model pass the model checking.

## 2. Consider the daily percentage log returns of S&P 500 index in Problem 1.

### a. Is there any serial correlation in the log returns of S&P 500 index?

Using Ljung-Box test, the output is as follows:

```
    Box-Ljung test
data:  sp500_data
X-squared = 12.253, df = 10, p-value = 0.2685
```

Using the $\alpha=0.05$ significance level, the $p=0.27 > \alpha$, then not reject the null hypothesis.

Thus, claim that there is no serial correlation in the log return of S&P 500 data.

### b. Is there any ARCH effect in the log returns of S&P 500 index?

Using Ljung-Box test, the output is as follows:

```
    Box-Ljung test
data:  sp500_at^2
X-squared = 850.73, df = 10, p-value < 2.2e-16
```

Using the $\alpha=0.05$ significance level, the $p < 2.2e-16 << \alpha$, then reject the null hypothesis. 

Thus, claim that there is ARCH correlation in the percentage log return of S&P 500 index data.

### c. Fit an IGARCH(1,1) model for the log return series of the index using normal distribution for the innovations.

Using `ugarchfit` in r with the following code:

```r
sp500_spec=ugarchspec(variance.model=list(model="iGARCH", garchOrder=c(1,1)),
                 mean.model=list(armaOrder=c(0,0)) )
sp500_igarch=ugarchfit(spec=sp500_spec,data=sp500_data)
sp500_igarch
```

The summary is as follows:

```
*---------------------------------*
*          GARCH Model Fit        *
*---------------------------------*

Conditional Variance Dynamics     
-----------------------------------
GARCH Model    : iGARCH(1,1)
Mean Model    : ARFIMA(0,0,0)
Distribution    : norm 

Optimal Parameters
------------------------------------
        Estimate  Std. Error  t value Pr(>|t|)
mu      0.039700    0.020102   1.9749 0.048284
omega   0.003201    0.001920   1.6671 0.095485
alpha1  0.067541    0.013261   5.0933 0.000000
beta1   0.932459          NA       NA       NA
```

### d. Compute 1 to 4-step ahead forecasts for the daily percentage log return and its 1-step forecasting interval.

The 1 to 4-step ahead forecast is as follows:

```
*------------------------------------*
*       GARCH Model Forecast         *
*------------------------------------*
Model: iGARCH
Horizon: 4
Roll Steps: 0
Out of Sample: 0

0-roll forecast [T0=1974-02-16 09:00:00]:
    Series  Sigma
T+1 0.0397 0.5003
T+2 0.0397 0.5035
T+3 0.0397 0.5066
T+4 0.0397 0.5098
```

The 1-step forecasting interval is $[0.0397\pm0.9806]$

## 3. Again, consider the daily percentage log returns of Starbucks stock in Problem 1.

### a. Fit a GARCH(1,1)-M model for the series with normal distribution. Write down the fitted model.

Using `garchfit` in r with the following code:

```r
sbux_garch11m_spec = ugarchspec(variance.model = list(model="sGARCH", garchOrder=c(1, 1)),
                            mean.model=list(armaOrder=c(0, 0), include.mean=T, archm=T,archpow=2),
                            distribution.model="norm")
sbux_garch11m = ugarchfit(spec=sbux_garch11m_spec,data=sbux_data)
sbux_garch11m
```

The summary is as follows:

```
*---------------------------------*
*          GARCH Model Fit        *
*---------------------------------*

Conditional Variance Dynamics     
-----------------------------------
GARCH Model    : sGARCH(1,1)
Mean Model    : ARFIMA(0,0,0)
Distribution    : norm 

Optimal Parameters
------------------------------------
        Estimate  Std. Error   t value Pr(>|t|)
mu      0.200578    0.112709   1.77961 0.075139
archm  -0.022281    0.029817  -0.74725 0.454915
omega   0.016130    0.005970   2.70176 0.006897
alpha1  0.019818    0.001982   9.99963 0.000000
beta1   0.975892    0.001020 956.55714 0.000000
```

The formula of the fitted model is as follows:

$$
\begin{aligned}
r_t &= 0.2006 -0.0223\sigma_t^2 + a_t\\
a_t &= \sigma_t \epsilon_t\\
\sigma_t^2&=0.0161+0.0198 a_{t-1}^2+0.9759\sigma_{t-1}^2
\end{aligned}
$$

### b. Is the ARCH-in-mean parameter significant at the 5% level?

The p-value for the archm parameter is about $0.45 > \alpha=0.05$, then it isn't significant.  

### c. Fit a EGARCH(1,1) model with normal innovations to the log returnseries. Perform model checking and write down the fitted model.

Using `ugarchfit` in r with the following code:

```r
sbux_egarch11_spec = ugarchspec(variance.model = list(model="eGARCH", garchOrder=c(1, 1)),
                            mean.model=list(armaOrder=c(0, 0), include.mean=T),
                            distribution.model="norm")
sbux_egarch11 = ugarchfit(spec=sbux_egarch11_spec,data=sbux_data)
sbux_egarch11
```

The summary is as follows:

```
*---------------------------------*
*          GARCH Model Fit        *
*---------------------------------*

Conditional Variance Dynamics     
-----------------------------------
GARCH Model    : eGARCH(1,1)
Mean Model    : ARFIMA(0,0,0)
Distribution    : norm 

Optimal Parameters
------------------------------------
        Estimate  Std. Error     t value Pr(>|t|)
mu      0.091685    0.048378      1.8952 0.058069
omega   0.010879    0.001538      7.0729 0.000000
alpha1 -0.039481    0.008608     -4.5867 0.000005
beta1   0.993388    0.000003 356831.0316 0.000000
gamma1  0.047907    0.002269     21.1150 0.000000
```

#### The formula of the fitted model is as follows:

$$
\begin{aligned}
r_t &= 0.0917 + a_t\\
a_t &= \sigma_t \epsilon_t\\
\log(\sigma_t^2)&=0.0109-0.0394 \frac{|a_{t-1}|+0.0479a_{t-1}}{\sigma_{t-1}}+0.9934\sigma_{t-1}^2
\end{aligned}
$$

#### Model Checking

Using Ljung-Box test to check the residuals of the fitted model, the output is as follows:

```
    Box-Ljung test

data:  stresi
X-squared = 11.668, df = 10, p-value = 0.3079

    Box-Ljung test

data:  stresi^2
X-squared = 4.7932, df = 10, p-value = 0.9046
```

Using the $\alpha=0.05$ significance level, the $p=0.31 > \alpha$ for the test on the standardized residuals and $p=0.90 > \alpha$ for the test on the squre of the standardize residuals, then not reject the null hypothesis that the standardized residuals $\epsilon_t$ are uncorrelated. 

Thus, the model satisfies the assumption the standardized residuals $\epsilon_t$ are uncorrelated and obeys a normal distribution. And we claim that the model pass the model checking.

### d. Is the leverage parameter significant?

The p-value of the leverage parameter $\gamma_1$ is less than $10^{-6} << \alpha=0.05$, then we can claim that the leverage parameter is significant. 

## 4. The data file m-pg5606.txt contains the date and monthly simple returns of Procter & Gamble (PG) stock from 1956 to 2006. Transform the simple returns into percentage log returns.

### a. Is there any serial correlation in the monthly log returns of PG stock?

Using Ljung-Box test to check the serial correlation of the monthly log returns of PG stock, the output is as follows:

```
    Box-Ljung test
data:  pg_data
X-squared = 9.65, df = 10, p-value = 0.4717
```

Using the $\alpha=0.05$ significance level, the $p=0.47 > \alpha$, then not reject the null hypothesis that the monthly log returns of PG stock are uncorrelated, then there is no serial correlation in the monthly log returns of PG stock.

### b. Fit a GARCH(1,1) model to the monthly percentage log returns of PG stock using normal distribution for the innovations. Write down the fitted model.

Using `garchFit` in r with the following code:

```r
pg_garch = garchFit(~garch(1,1),data=pg_data,trace = F)
summary(pg_garch)
```

The summary is as follows:

```
Title:
 GARCH Modelling 

Call:
 garchFit(formula = ~garch(1, 1), data = pg_data, trace = F) 

Conditional Distribution:
 norm 

Coefficient(s):
      mu     omega    alpha1     beta1  
0.856243  0.853663  0.096309  0.862403  

Error Analysis:
        Estimate  Std. Error  t value Pr(>|t|)    
mu       0.85624     0.15801    5.419    6e-08 ***
omega    0.85366     0.39205    2.177 0.029450 *  
alpha1   0.09631     0.02697    3.571 0.000355 ***
beta1    0.86240     0.03133   27.526  < 2e-16 ***

Standardised Residuals Tests:
                                Statistic p-Value     
 Ljung-Box Test     R    Q(10)  7.001382  0.7253145     
 Ljung-Box Test     R^2  Q(10)  4.061319  0.9445384   
```

#### The formula of the fitted model is as follows:

$$
\begin{aligned}
r_t &= 0.8562 + a_t\\
a_t &= \sigma_t \epsilon_t\\
\sigma_t^2&=0.8536+0.0963a_{t-1}^2+0.8624\sigma_{t-1}^2
\end{aligned}
$$

### c. Use the fitted model to calculate 1-step to 5-step ahead forecasts for the log returns series and its 1-step forecasting interval.

The 1-step to 5-step ahead forecasts for the log returns series is as follows:

<!--
Transfrom this to markdown table 
meanForecast    meanError    standardDeviation    lowerInterval    upperInterval
<dbl>    <dbl>    <dbl>    <dbl>    <dbl>
0.8562431    2.952353    2.952353    -4.930263    6.642749
0.8562431    3.034827    3.034827    -5.091908    6.804394
0.8562431    3.111843    3.111843    -5.242858    6.955344
0.8562431    3.183931    3.183931    -5.384148    7.096634
0.8562431    3.251543    3.251543    -5.516664    7.229150 -->

| meanForecast | meanError | standardDeviation | lowerInterval | upperInterval |
|:------------:|:---------:|:-----------------:|:-------------:|:-------------:|
| 0.8562431    | 2.952353  | 2.952353          | -4.930263     | 6.642749      |
| 0.8562431    | 3.034827  | 3.034827          | -5.091908     | 6.804394      |
| 0.8562431    | 3.111843  | 3.111843          | -5.242858     | 6.955344      |
| 0.8562431    | 3.183931  | 3.183931          | -5.384148     | 7.096634      |
| 0.8562431    | 3.251543  | 3.251543          | -5.516664     | 7.229150      |

The 1-step forecasting interval is $[-4.9303,6.6427]$.

## 5. The file d-exuseu.txt contains the daily exchange rate between U.S. Dollars and Euro from January 1999 to March 20, 2007. Compute the percentage log returns of the exchange rate.

### a. Is there any serial correlation in the log return series?

Using Ljung-Box test to check the serial correlation of the log return series, the output is as follows:

```
    Box-Ljung test
data:  exchange_data
X-squared = 11.921, df = 10, p-value = 0.2904
```

Using the $\alpha=0.05$ significance level, the $p=0.29 > \alpha$, then not reject the null hypothesis that the log return series are uncorrelated, then there is no serial correlation in the log return series.

### b. Is there any ARCH effect in the log return series?

Using Ljung-Box test to check the ARCH effect of the log return series, the output is as follows:

```
    Box-Ljung test

data:  exchange_at^2
X-squared = 29.121, df = 10, p-value = 0.001191
```

Using the $\alpha=0.05$ significance level, the $p=0.0012 < \alpha$, then reject the null hypothesis. 

Thus, claim that there is ARCH correlation in the log return of Exchange data.

### c. Fit an IGARCH(1,1) model to the log return series using normal innovations. Perform model checking and write down the fitted model.

Using `ugarchfit` in r with the following code:

```r
exchange_spec=ugarchspec(variance.model=list(model="iGARCH", garchOrder=c(1,1)),
                 mean.model=list(armaOrder=c(0,0)) )
exchange_igarch=ugarchfit(spec=exchange_spec,data=exchange_data)
exchange_igarch
```

The summary is as follows:

```
*---------------------------------*
*          GARCH Model Fit        *
*---------------------------------*

Conditional Variance Dynamics     
-----------------------------------
GARCH Model    : iGARCH(1,1)
Mean Model    : ARFIMA(0,0,0)
Distribution    : norm 

Optimal Parameters
------------------------------------
        Estimate  Std. Error  t value Pr(>|t|)
mu      0.011123    0.012801 0.868957 0.384871
omega   0.000004    0.000108 0.034425 0.972538
alpha1  0.016037    0.003822 4.196090 0.000027
beta1   0.983963          NA       NA       NA
```

#### The formula of the fitted model is as follows:

$$
\begin{aligned}
r_t &= 0.0111 + a_t\\
a_t &= \sigma_t \epsilon_t\\
\sigma_t^2&=4\times10^{-6}+0.0160a_{t-1}^2+(1-0.0160)\sigma_{t-1}^2
\end{aligned}
$$

#### Model checking

Using Ljung-Box test to check the residuals of the fitted model, the output is as follows:

```
    Box-Ljung test

data:  stresi
X-squared = 9.6109, df = 10, p-value = 0.4753

    Box-Ljung test

data:  stresi^2
X-squared = 11.828, df = 10, p-value = 0.2968
```

Using the $\alpha=0.05$ significance level, the $p=0.48 > \alpha$ for the test on the standardized residuals and $p=0.29 > \alpha$ for the test on the squre of the standardize residuals, then not reject the null hypothesis that the standardized residuals $\epsilon_t$ are uncorrelated. 

Thus, the model satisfies the assumption the standardized residuals $\epsilon_t$ are uncorrelated and obeys a normal distribution. And we claim that the model pass the model checking.

### d. Use the model to produce 1-step to 4-step ahead forecasts for the log return series and its 1-step forecasting interval.

The 1-step to 4-step ahead forecasts for the log return series is as follows:

```
*------------------------------------*
*       GARCH Model Forecast         *
*------------------------------------*
Model: iGARCH
Horizon: 4
Roll Steps: 0
Out of Sample: 0

0-roll forecast [T0=1975-08-27 09:00:00]:
     Series  Sigma
T+1 0.01112 0.3746
T+2 0.01112 0.3746
T+3 0.01112 0.3746
T+4 0.01112 0.3746
```

The 1-step forecasting interval is $[0.0111\pm0.7342]$.
