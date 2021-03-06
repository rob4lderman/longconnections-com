

## Intro to Regression: Part 6: Regressing against a factor variable

Factor variables (aka "nominal" variables) are not numeric, so they require special handling.  Let's walk thru an example.

Going back to the **mtcars** dataset, let's regress mpg against transmission type (am).  There are two transmission
types: "automatic" and "manual", which are encoded as "0" and "1" in the dataset, respectively. 

Just so it's explicitly clear that we're NOT dealing with a numeric variable, I'm going to rename the levels "automatic" and "manual".

---

```r
    data(mtcars)
    
    # convert am to factor and rename levels
    mtcars$am <- as.factor(mtcars$am)
    levels(mtcars$am) <- c("automatic", "manual")
```
---

As per usual, let's first do a scatterplot of mpg vs. transmission type (am):

---

```r
    library(ggplot2)
    library(grid)
    
    qplot(x=am, y=mpg, data=mtcars, colour=am) + 
        ggtitle("mpg vs. transmission type (am)") + 
        xlab("transmission type (am)")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

It appears that automatics have lower mpg than manuals, on average.

OK, let's go ahead and build a regression model with mpg as the response variable and
transmission type (am) as the sole predictor variable. 

---

```r
    model <- lm(mpg ~ am - 1, data=mtcars)
```
---

This corresponds to the regression model:

$$
mpg = \beta_1 \cdot [am == "automatic"] + \beta_2 \cdot [am == "manual"] + \epsilon
$$

<ul>
<li>mpg is the response variable</li>
<li>\([am == "automatic"]\) is a "dummy variable"</li>
<li>\([am == "manual"]\) is another dummy variable</li>
<li>\(\epsilon\) is the error term (the residuals)</li>
</ul>

*Note: The "-1" in "mpg ~ am - 1" removes the Y-intercept term from the model. Notice that the
regression equation above doesn't have a standalone Y-intercept coefficient. Ignore this detail 
for now, we'll come back to it later.*

<br />
### Dummy variables

Since a factor variable does not have numeric values, we need to
create "dummy variables" for it.  That's what *[am == "automatic"]* and *[am == "manual"]* represent - they're 
both dummy variables. 

There's one dummy variable for each factor level.  The dummy variable takes on
the value 0 or 1, depending on the value of the factor variable.  If the am factor
variable for a particular car equals "automatic", then the *[am == "automatic"]* dummy
variable is set to 1, while the *[am == "manual"]* dummy variable is set to 0.

Think of *[am == "automatic"]* and *[am == "manual"]* as boolean expressions (note the double "==",
for the programmers out there) that evaluate to *true* (1) or *false* (0), depending on the
actual value of the am variable.

Only one dummy variable in the regression function is set to 1 at any given time.
The remaining dummy variables are all 0.  A dummy variable is set to 1 when the factor variable's value 
takes on the corresponding factor level.

So in this example we have two factor levels: "automatic" and "manual", and therefore
two dummy variables.  When am = "automatic", the *[am == "automatic"]* dummy variable is 1, while *[am == "manual"]*
is 0, so it drops out of the equation:

$$
\begin{align*}
mpg &= \beta_1 \cdot [am == "automatic"] + \beta_2 \cdot [am == "manual"] 
\\\\
mpg &= \beta_1 \cdot [1] + \beta_2 \cdot [0] 
\\\\
mpg &= \beta_1 
\end{align*}
$$

<p>
So \(\beta_1\) is the predicted mpg value for "automatic" cars.  There is no slope to this equation, it's just 
a horizontal line at \(Y = \beta_1\), which happens to be the mean(mpg) for cars with am = "automatic".
</p>

<p>
When am = "manual", the *[am == "manual"]* variable is set to 1, while the *[am == "automatic"]* term drops out:
</p>

$$
\begin{align*}
mpg &= \beta_1 \cdot [am == "automatic"] + \beta_2 \cdot [am == "manual"] 
\\\\
mpg &= \beta_1 \cdot [0] + \beta_2 \cdot [1] 
\\\\
mpg &= \beta_2 
\end{align*}
$$

So \(\beta_2\) is the predicted mpg for "manual" cars. Again, this is a zero-sloped horizontal line at \(Y = \beta_1\),
which is the mean(mpg) for am = "manual".

Note that the regression model is not a single line but actually two lines (or points, really), one for each factor level.
Let's chart them:

---

```r
    B1 <- coef(model)[1]
    B2 <- coef(model)[2]
    
    qplot(x=am, y=mpg, data=mtcars, colour=am) + 
        ggtitle("mpg vs. transmission type (am)") + 
        xlab("transmission type (am)") +
        geom_hline(y=B1, colour=2) + 
        geom_hline(y=B2, colour=5)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

The regression model is two lines: 

<ol>
<li>the lower line, at predicted mpg = \(\beta_1\) = mean(mpg) for am = "automatic"</li>
<li>the upper line, at predicted mpg = \(\beta_2\) = mean(mpg) for am = "manual"</li>
</ol>

If the factor variable had 3 levels, the regression model would have 3 lines. 4 levels, 4 lines. And so on.

Let's look at the model summary to get the coefficient values:

---

```r
    summary(model)
```

```
## 
## Call:
## lm(formula = mpg ~ am - 1, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## amautomatic   17.147      1.125   15.25 1.13e-15 ***
## ammanual      24.392      1.360   17.94  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.902 on 30 degrees of freedom
## Multiple R-squared:  0.9487,	Adjusted R-squared:  0.9452 
## F-statistic: 277.2 on 2 and 30 DF,  p-value: < 2.2e-16
```

```r
    ## 
    ## Call:
    ## lm(formula = mpg ~ am - 1, data = mtcars)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## amautomatic   17.147      1.125   15.25 1.13e-15 ***
    ## ammanual      24.392      1.360   17.94  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 4.902 on 30 degrees of freedom
    ## Multiple R-squared:  0.9487, Adjusted R-squared:  0.9452 
    ## F-statistic: 277.2 on 2 and 30 DF,  p-value: < 2.2e-16
```

---

<ul>
<li>\(\beta_1\) = 17.147, the Estimate for the <i>[am==automatic]</i> coefficient</li>
<li>\(\beta_2\) = 24.392, the Estimate for the <i>[am==manual]</i> coefficient</li>
</ul>

As mentioned above, \(\beta_1\) can be interpreted as the average mpg for "automatics", while
\(\beta_2\) can be interpreted as the average mpg for "manuals".


<br />
### Re-introducing the Y-intercept term into the model

Normally you'd include the Y-intercept term in the regression model.
For that we need to select a level from the factor variable that
represents the "base" level.  The Y-intercept coefficient corresponds to
the "base" level.  

The coefficients for other levels are evaluated relative to the "base" level.
This is because the "base" level coefficient (the Y-intercept term) is always
present (non-zero) in the regression function. 

This is made clear by looking at the regression equation.  Without the Y-intercept
term, the regression function (from above) is: 

$$
mpg = \beta_1 \cdot [am == "automatic"] + \beta_2 \cdot [am == "manual"] + \epsilon
$$

With the Y-intercept term, the regression equation is:

$$
mpg = \beta_0 + \beta_1 \cdot [am == "manual"] + \epsilon
$$

The difference being that the *[am == "automatic"]* dummy variable has been removed. 
Thus, *[am == "automatic"]* represents the "base" level.   When the factor variable
equals "automatic", the *[am == "manual"]* dummy variable drops out, and the
regression equation becomes (ignoring the error term):

$$
\begin{align*}
mpg &= \beta_0 + \beta_1 \cdot [am == "manual"] 
\\\\
mpg &= \beta_0 + \beta_1 \cdot [0] 
\\\\
mpg &= \beta_0 
\end{align*}
$$

<p>
So \(\beta_0\) is the predicted mpg when am = "automatic".  
</p>

When am = "manual", the regression function is:


$$
\begin{align*}
mpg &= \beta_0 + \beta_1 \cdot [am == "manual"] 
\\\\
mpg &= \beta_0 + \beta_1 \cdot [1] 
\\\\
mpg &= \beta_0 + \beta_1
\end{align*}
$$

<p>
The predicted mpg when am = "manual" is: \(mpg = \beta_0 + \beta_1\).  Contrast this with the non-Y-intercept model above, where
the predicted mpg for am = "manual" is just: \(mpg = \beta_2\).  In the Y-intercept model, the Y-intercept coefficient must be
added to the *[am == "manual"]* coefficient to get the predicted mpg.
</p>

<p>
This is what I mean when I say the coefficients for other factor levels are evaluated relative to the "base" level.
The "base" level coefficient, \(\beta_0\), is always in play.  It's never "turned off" for other factor levels.
</p>

<p>
This affects the coefficients of other levels.  When a factor level dummy variable is set to 1, its coefficient 
is added to \(\beta_0\) to get the predicted mpg.  The factor level coefficient represents the difference (in the 
response variable) between that level and the "base" level.
</p>


<br />
### The linear model in R


```r
    model.y <- lm(mpg ~ am, data=mtcars)
    summary(model.y)
```

```
## 
## Call:
## lm(formula = mpg ~ am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   17.147      1.125  15.247 1.13e-15 ***
## ammanual       7.245      1.764   4.106 0.000285 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.902 on 30 degrees of freedom
## Multiple R-squared:  0.3598,	Adjusted R-squared:  0.3385 
## F-statistic: 16.86 on 1 and 30 DF,  p-value: 0.000285
```

```r
    ## 
    ## Call:
    ## lm(formula = mpg ~ am, data = mtcars)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   17.147      1.125  15.247 1.13e-15 ***
    ## ammanual       7.245      1.764   4.106 0.000285 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 4.902 on 30 degrees of freedom
    ## Multiple R-squared:  0.3598, Adjusted R-squared:  0.3385 
    ## F-statistic: 16.86 on 1 and 30 DF,  p-value: 0.000285
```

---

By default, **lm** will use the "lowest" level in the
factor variable to represent the "base" level (in this example, the lowest level is "automatic").  
The coefficients for remaining factor levels represent the difference between that
level and the base level.  The coefficient values are:

<ul>
<li>\(\beta_0\) = 17.147, the Estimate for the (Intercept) coefficient</li>
<li>\(\beta_1\) = 7.245, the Estimate for the <i>[am == "manual"]</i> coefficient</li>
</ul>

<p>
Note that \(\beta_0\) from the Y-intercept model equals \(\beta_1\) from the non-Y-intercept model,
which makes sense since they both correspond to <i>[am == "automatic"]</i>.
</p>

<p>
Also note that \(\beta_0 + \beta_1\) from the Y-intercept model equals \(\beta_2\) from the non-Y-intercept
model.  This makes sense because the combined coefficients from the Y-intercept model give you the
predicted mpg for <i>[am == "manual"]</i>, just as \(\beta_2\) does in the non-Y-intercept model.
</p>

The regression lines from the Y-intercept model are identical to those from the non-Y-intercept model:

---

```r
    B0 <- coef(model.y)[1]
    B1 <- coef(model.y)[2]
    
    qplot(x=am, y=mpg, data=mtcars, colour=am) + 
        ggtitle("mpg vs. transmission type (am)") + 
        xlab("transmission type (am)") +
        geom_hline(y=B0, colour=2) + 
        geom_hline(y=B0+B1, colour=5)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

Again, the regression model is two lines: 

<ol>
<li>the lower line, at predicted mpg = \(\beta_0\) = mean(mpg) for am = "automatic"</li>
<li>the upper line, at predicted mpg = \(\beta_0 + \beta_1\) = mean(mpg) for am = "manual"</li>
</ol>


<br />
### The residuals

Let's take a look at the residuals for the Y-intercept model:

---

```r
    mpg.actual <- mtcars$mpg
    mpg.predicted <- predict(model.y)
    mpg.residual <- resid(model.y)
    
    data.frame(mpg.actual, mtcars$am, mpg.predicted, mpg.residual)
```

```
##                     mpg.actual mtcars.am mpg.predicted mpg.residual
## Mazda RX4                 21.0    manual      24.39231   -3.3923077
## Mazda RX4 Wag             21.0    manual      24.39231   -3.3923077
## Datsun 710                22.8    manual      24.39231   -1.5923077
## Hornet 4 Drive            21.4 automatic      17.14737    4.2526316
## Hornet Sportabout         18.7 automatic      17.14737    1.5526316
## Valiant                   18.1 automatic      17.14737    0.9526316
## Duster 360                14.3 automatic      17.14737   -2.8473684
## Merc 240D                 24.4 automatic      17.14737    7.2526316
## Merc 230                  22.8 automatic      17.14737    5.6526316
## Merc 280                  19.2 automatic      17.14737    2.0526316
## Merc 280C                 17.8 automatic      17.14737    0.6526316
## Merc 450SE                16.4 automatic      17.14737   -0.7473684
## Merc 450SL                17.3 automatic      17.14737    0.1526316
## Merc 450SLC               15.2 automatic      17.14737   -1.9473684
## Cadillac Fleetwood        10.4 automatic      17.14737   -6.7473684
## Lincoln Continental       10.4 automatic      17.14737   -6.7473684
## Chrysler Imperial         14.7 automatic      17.14737   -2.4473684
## Fiat 128                  32.4    manual      24.39231    8.0076923
## Honda Civic               30.4    manual      24.39231    6.0076923
## Toyota Corolla            33.9    manual      24.39231    9.5076923
## Toyota Corona             21.5 automatic      17.14737    4.3526316
## Dodge Challenger          15.5 automatic      17.14737   -1.6473684
## AMC Javelin               15.2 automatic      17.14737   -1.9473684
## Camaro Z28                13.3 automatic      17.14737   -3.8473684
## Pontiac Firebird          19.2 automatic      17.14737    2.0526316
## Fiat X1-9                 27.3    manual      24.39231    2.9076923
## Porsche 914-2             26.0    manual      24.39231    1.6076923
## Lotus Europa              30.4    manual      24.39231    6.0076923
## Ford Pantera L            15.8    manual      24.39231   -8.5923077
## Ferrari Dino              19.7    manual      24.39231   -4.6923077
## Maserati Bora             15.0    manual      24.39231   -9.3923077
## Volvo 142E                21.4    manual      24.39231   -2.9923077
```

```r
    hist(mpg.residual, 
         col="yellow", 
         main="Distribution of mpg residuals", 
         xlab="mpg residuals")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

```r
    ##                     mpg.actual mtcars.am mpg.predicted mpg.residual
    ## Mazda RX4                 21.0    manual      24.39231   -3.3923077
    ## Mazda RX4 Wag             21.0    manual      24.39231   -3.3923077
    ## Datsun 710                22.8    manual      24.39231   -1.5923077
    ## Hornet 4 Drive            21.4 automatic      17.14737    4.2526316
    ## Hornet Sportabout         18.7 automatic      17.14737    1.5526316
    ## Valiant                   18.1 automatic      17.14737    0.9526316
    ## ...    
```

The residuals appear to be distributed somewhat normally.  This tells us the prediction errors
are somewhat balanced around the regression line(s), which is what we want.

The residuals for the Y-intercept model are exactly the same as the residuals
for the non-Y-intercept model:

---

```r
    mpg.actual <- mtcars$mpg
    mpg.predicted <- predict(model)
    mpg.residual <- resid(model)
    
    data.frame(mpg.actual, mtcars$am, mpg.predicted, mpg.residual)
```

```
##                     mpg.actual mtcars.am mpg.predicted mpg.residual
## Mazda RX4                 21.0    manual      24.39231   -3.3923077
## Mazda RX4 Wag             21.0    manual      24.39231   -3.3923077
## Datsun 710                22.8    manual      24.39231   -1.5923077
## Hornet 4 Drive            21.4 automatic      17.14737    4.2526316
## Hornet Sportabout         18.7 automatic      17.14737    1.5526316
## Valiant                   18.1 automatic      17.14737    0.9526316
## Duster 360                14.3 automatic      17.14737   -2.8473684
## Merc 240D                 24.4 automatic      17.14737    7.2526316
## Merc 230                  22.8 automatic      17.14737    5.6526316
## Merc 280                  19.2 automatic      17.14737    2.0526316
## Merc 280C                 17.8 automatic      17.14737    0.6526316
## Merc 450SE                16.4 automatic      17.14737   -0.7473684
## Merc 450SL                17.3 automatic      17.14737    0.1526316
## Merc 450SLC               15.2 automatic      17.14737   -1.9473684
## Cadillac Fleetwood        10.4 automatic      17.14737   -6.7473684
## Lincoln Continental       10.4 automatic      17.14737   -6.7473684
## Chrysler Imperial         14.7 automatic      17.14737   -2.4473684
## Fiat 128                  32.4    manual      24.39231    8.0076923
## Honda Civic               30.4    manual      24.39231    6.0076923
## Toyota Corolla            33.9    manual      24.39231    9.5076923
## Toyota Corona             21.5 automatic      17.14737    4.3526316
## Dodge Challenger          15.5 automatic      17.14737   -1.6473684
## AMC Javelin               15.2 automatic      17.14737   -1.9473684
## Camaro Z28                13.3 automatic      17.14737   -3.8473684
## Pontiac Firebird          19.2 automatic      17.14737    2.0526316
## Fiat X1-9                 27.3    manual      24.39231    2.9076923
## Porsche 914-2             26.0    manual      24.39231    1.6076923
## Lotus Europa              30.4    manual      24.39231    6.0076923
## Ford Pantera L            15.8    manual      24.39231   -8.5923077
## Ferrari Dino              19.7    manual      24.39231   -4.6923077
## Maserati Bora             15.0    manual      24.39231   -9.3923077
## Volvo 142E                21.4    manual      24.39231   -2.9923077
```

```r
    hist(mpg.residual, 
         col="yellow", 
         main="Distribution of mpg residuals", 
         xlab="mpg residuals")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 

```r
    ##                     mpg.actual mtcars.am mpg.predicted mpg.residual
    ## Mazda RX4                 21.0    manual      24.39231   -3.3923077
    ## Mazda RX4 Wag             21.0    manual      24.39231   -3.3923077
    ## Datsun 710                22.8    manual      24.39231   -1.5923077
    ## Hornet 4 Drive            21.4 automatic      17.14737    4.2526316
    ## Hornet Sportabout         18.7 automatic      17.14737    1.5526316
    ## Valiant                   18.1 automatic      17.14737    0.9526316
    ## ...
```

<br />
## Goodness of fit - R<sup>2</sup>

Finally, let's calculate R<sup>2</sup>:

---

```r
    ss.res <- sum(mpg.residual^2) 
    ss.tot <- with(mtcars, sum( (mpg - mean(mpg))^2 ) )
    
    R2 <- 1 - ss.res / ss.tot
    R2
```

```
## [1] 0.3597989
```


Note this is the same R<sup>2</sup> reported by the Y-intercept model summary (the "Multiple R-squared" field).
It's not that great - only 36% of the variance is explained by the model.  But that's not much of a surprise
since our model is very simplistic.

Note, however, that it is **not** the same R<sup>2</sup> reported by the non-Y-intercept model above. For an explanation
of this apparent inconsistency, check out this thread: 
[http://stats.stackexchange.com/questions/26176/removal-of-statistically-significant-intercept-term-boosts-r2-in-linear-model](http://stats.stackexchange.com/questions/26176/removal-of-statistically-significant-intercept-term-boosts-r2-in-linear-model)





### TODO: t-tests

The t-tests in the output represent the significance in the DIFFERENCE between
the two levels.  If a level is measurably different from another level, then we
will see this difference in the data.  If it is not, then we will not, and
that's all that matters.  In that case the effect of that level is entirely and
already explained by the other level.  So this new level is redundant.


The t-tests for the non-Y-intercept model are comparing the level means to the
"null hypothesis" mean, which would be the mean mpg across ALL levels of the
factor.  

Note that, in the Y-intercept model, the first t-test, on the level which
establishes "base" level, is a t-test comparing the mean response for the base
level to the mean response across all levels, so it's the same t-test in both
models (TODO: is this true??).  However in the Y-intercept model, all
subsequent t-tests are comparing the difference in response means between each
level and the base level.

