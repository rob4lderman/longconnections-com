
For this example we'll need some sample data. Let's just create some randomly.  We'll create two samples, both with a sample size of 30, using the rnorm function.  For one of the samples we'll add 1 to each value.  This should give us a real, measurable difference between the two samples.  



```r
set.seed(2)
n1 <- 30
n2 <- 30
samp.1 <- rnorm(n1)
samp.2 <- rnorm(n2) + 1
```

The significance test gives us the probability that the difference between the samples is due to random chance.  In this example, we know for a fact that there's a real difference, since we created that difference ourselves by shifting up the second sample by a full standard deviation (remember that the standard deviation of the standard normal distribution is 1).  So we should expect to see a statistically significant difference between the two samples.

For the purpose of comparing and contrasting the different types of t-tests, we'll run the same sample data thru all three t-tests described above: (1) paired samples, (2) indepedent samples with equal variances, and (3) independent samples with un-equal variances.  (Normally you would not use all three t-tests.  You'd use just the one t-test that's most appropriate for your sample data).

First we'll treat the data like paired samples.


```r
library(ggplot2)

samp.diffs <- samp.2 - samp.1
t.score <- mean(samp.diffs) / sqrt(var(samp.diffs)/n1)
df = n1 - 1
p.value <- 2 * (1 - pt(t.score, df=df) )

t.score
```

```
## [1] 2.29345
```

```r
df
```

```
## [1] 29
```

```r
p.value
```

```
## [1] 0.02925964
```

Note that the p-value is calculated according to a two-tailed significance test.  The chart below
illustrates where that p-value comes from:


```r
x <- seq(-4.5,4.5,0.01)
shade_x.lower <- seq(-4.5,-t.score,0.01)
shade_y.lower <- dt(shade_x.lower, df=df)
shade.lower <- data.frame( rbind(c(-4.5,0), cbind(shade_x.lower,shade_y.lower), c(-t.score,0)) )

shade_x.upper <- seq(t.score,4.5,0.01)
shade_y.upper <- dt(shade_x.upper, df=df)
shade.upper <- data.frame( rbind(c(t.score,0), cbind(shade_x.upper,shade_y.upper), c(4.5,0)) )

ggplot() + 
    stat_function(aes(x=x), 
                  fun=dt, 
                  args=list(df=df),
                  size=1, 
                  colour="blue") +
    ggtitle("t-distribution, degrees of freedom = n-1 = 29") + 
    geom_hline(y=0, colour="darkgray") +
    geom_vline(x=0, colour="darkgray") +
    geom_vline(x=-t.score, linetype="dashed", colour="orange", size=1) +
    geom_vline(x=t.score, linetype="dashed", colour="orange", size=1) +
    geom_polygon(data = shade.lower, aes(shade_x.lower, shade_y.lower), fill="yellow") +
    geom_polygon(data = shade.upper, aes(shade_x.upper, shade_y.upper), fill="yellow") +
    ylab("Probability density") + 
    xlab("t-scores")  +
    scale_x_continuous(breaks=-4:4, labels=-4:4)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

The vertical orange lines indicate the t-score and its corresponding opposite t-score (the negative t-score).
You use both the positive and negative t-scores for two-tailed significance tests.  Two-tailed significance
tests allow the difference between samples to vary in either direction.  If we were only interested in 
a difference in one direction, then we'd just use the one t-score along that tail.  The p-value
(probability) for the one-tailed test is always half the p-value of the two-tailed test.


Now let's treat the samples as if they were independent samples with equal variances.


```r
var.pooled <- ( (n1-1) * var(samp.1) + (n2-1) * var(samp.2) ) / (n1 + n2 - 2)
t.score <- (mean(samp.2) - mean(samp.1)) / ( sqrt(var.pooled) * sqrt( 1/n1 + 1/n2 ) )
df = n1 + n2 - 2
p.value <- 2 * (1 - pt(t.score, df=df) )

t.score
```

```
## [1] 2.457141
```

```r
df
```

```
## [1] 58
```

```r
p.value
```

```
## [1] 0.01701674
```

And finally, let's treat the samples as if they were independent samples with un-equal variances.


```r
t.score <- (mean(samp.2) - mean(samp.1)) /  sqrt(var(samp.1)/n1 + var(samp.2)/n2 ) 
df <- (var(samp.1)/n1 + var(samp.2)/n2)^2 / ( (var(samp.1)/n1)^2 / (n1-1) + (var(samp.2)/n2)^2 / (n2-1) )
p.value <- 2 * (1 - pt(t.score, df=df) )

t.score
```

```
## [1] 2.457141
```

```r
df
```

```
## [1] 57.96975
```

```r
p.value
```

```
## [1] 0.01701837
```

Note that the t.score for both the equal variances and unequal variances is exactly the same. 
This is always true when the sample sizes are equal (n1 == n2).  (I leave it as an exercise for 
the reader to prove that algebraically).

R provides a convenient function, t.test, that takes two samples and generates the t-statistic,
degrees of freedom, and the p-value:


```r
t.test(samp.1, samp.2, paired=T)
```

```
## 
## 	Paired t-test
## 
## data:  samp.1 and samp.2
## t = -2.2935, df = 29, p-value = 0.02926
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.39286961 -0.07968764
## sample estimates:
## mean of the differences 
##              -0.7362786
```

```r
t.test(samp.1, samp.2, paired=F, var.equal=T)
```

```
## 
## 	Two Sample t-test
## 
## data:  samp.1 and samp.2
## t = -2.4571, df = 58, p-value = 0.01702
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.3360903 -0.1364669
## sample estimates:
## mean of x mean of y 
## 0.2286718 0.9649504
```

```r
t.test(samp.1, samp.2, paired=F, var.equal=F)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  samp.1 and samp.2
## t = -2.4571, df = 57.97, p-value = 0.01702
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.3360970 -0.1364603
## sample estimates:
## mean of x mean of y 
## 0.2286718 0.9649504
```



## one sample t-stat
\mathchardef\mhyphen="2D
\begin{align*} 
t\mhyphen statistic & = \frac{\overline{X} - \mu_0}{ \frac{s}{\sqrt{n}} }
\\ \\
df & = n - 1
\\ \\
where,
\\[8pt]
\overline{X} & = sample \ mean
\\[8pt]
\mu_0 & = assumed \ population \ mean \ (null\ hypothesis)
\\[8pt]
s & = unbiased \ sample \ variance
\\[8pt]
df & = degrees \ of \ freedom
\end{align*}


## two sample paired t-stat

\mathchardef\mhyphen="2D
\begin{align*} 
t\mhyphen statistic & = \frac{\overline{X_d} - 0}{ \frac{s}{\sqrt{n}} }
\\ \\
df & = n - 1
\\ \\
where,
\\[8pt]
\overline{X_d} & = mean \ of \ the \ di\!f\!f\!s
\end{align*}


## two sample, equal vars
     
\mathchardef\mhyphen="2D
\begin{align*} 
s_p^2 & = \frac{ (n_x - 1) \cdot s_x^2 +  (n_y - 1) \cdot s_y^2 }{ n_x + n_y - 2 }
\\ \\
t\mhyphen statistic & = \frac{\overline{X} - \overline{Y} }{ s_p \cdot \sqrt{\frac{1}{n_x} + \frac{1}{n_y}}} 
\\ \\
df & = n_x + n_y - 2
\\ \\
where,
\\[8pt]
\overline{X} & = sample \ mean
\\[8pt]
\overline{Y} & = sample \ mean
\\[8pt]
s_p^2 & = pooled \ sample \ variance
\\[8pt]
s_p & = pooled \ sample \ standard \ deviation
\\[8pt]
s_x^2 & = unbiased \ sample \ variance \ for \ X
\\[8pt]
s_y^2 & = unbiased \ sample \ variance \ for \ Y
\\[8pt]
n_x & = sample \ size \ of \ X
\\[8pt]
n_y & = sample \ size \ of \ Y
\end{align*}



## two sample, UNequal vars


\mathchardef\mhyphen="2D
\begin{align*} 
t\mhyphen statistic & = \frac{\overline{X} - \overline{Y} }{ \sqrt{\frac{s_x^2}{n_x} + \frac{s_y^2}{n_y}}} 
\\ \\
df & = \frac{ (\frac{s_x^2}{n_x} + \frac{s_y^2}{n_y})^2 } { \frac{ (\frac{s_x^2}{n_x})^2 }{ n_x - 1 } +  \frac{ (\frac{s_y^2}{n_y})^2 }{ n_y - 1 } }
\end{align*}




