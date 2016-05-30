
## Intro to Regression: Part 3: Covariance and correlation

At the heart of regression analysis is the measure of ***covariance*** and
***correlation*** between variables.  Covariance measures how two variables vary with
respect to each other (how much they "move together").

The covariance of two variables, X and Y, is given by the below equation:

$$ Cov(X,Y) = \frac{1}{N-1} \cdot \sum_i^N (X_i - \overline{X})(Y_i - \overline{Y}) $$

Technically speaking, this is the ***sample covariance***.  Note the degrees of
freedom, N-1, where the -1 accounts for the estimation of the variables' true
means using their respective sample means.

Compare the equation for sample covariance above with the equation for sample variance:

$$
\begin{align*}
Var(X) &= \frac{1}{N-1} \cdot \sum_i^N (X_i - \overline{X})^2
\\\\[8pt]
&= \frac{1}{N-1} \cdot \sum_i^N (X_i - \overline{X})(X_i - \overline{X})
\end{align*}
$$

One could say that a variable's sample variance is the same as its covariance
with itself.  (I'm not sure if this provides any sort of useful insight).

The correlation between two variables is just the "normalized" covariance.  By
"normalized" I mean the variables are converted to z-scores.  A z-score is
calculated using the below equation:

$$
Z = \frac{X - \overline{X}}{\sigma_x}
$$

We've already subtracted the means in the sample covariance formula, so to convert it to the correlation we need to divide by the standard devations of both X and Y:

$$
Cor(X,Y) = \frac{ Cov(X,Y) } {\sigma_x \sigma_y}
$$

The correlation between two variables always falls in the range between +1 and -1.  
* +1 = a perfectly linear relationship  
  A perfectly linear relationship means that a 1-unit change in X corresponds to a 1-unit change in Y. 
* -1 = a perfectly inverse linear relationship  
  A perfectly inverse linear relationship  means that a 1-unit change in X corresponds to a negative 1-unit change in Y.
* 0 = no relationship at all  
  No relationship at all means that X and Y don't move together.  A 1-unit change in X tells you nothing about the change in Y.

<br />
### How is the correlation applied to the linear model?

A linear model relating Y (the response) to X (the predictor) takes on the form:

$$
Y = \beta_0 + \beta_1 X + \epsilon
$$

The coefficient \(\beta_1\), which relates X to Y, is determined by the
correlation between the two variables.  In this simple model (with a single
predictor variable), \(\beta_1\) is calculated by the below equation:

$$
\beta_1 = Cor(X,Y) \cdot \frac{\sigma_y}{\sigma_x}
$$

*Aside: the calculation of \(\beta_1\) is a bit more complex when you add more predictor 
variables to the model.  We'll cover that later.*

Let's step back and make sense of this.  Cor(X,Y) is the "normalized"
covariance, which means it's the ratio of "normalized" movements in X to
"normalized" movements in Y.  Multiplying by the standard deviation of Y
effectively "de-normalizes" it, to get us back to the actual (non-normalized)
values of Y.  

So how come we divide by the standard deviation of X?  Well, because we're
multiplying the whole thing by the (non-normalized) values of X.  So
technically we have to first "normalize" the X value (by dividing it by its
standard deviation), then apply the correlation (the "normalized"
covariance), which gives is the "normalized" movement in Y, which
we then multiply by the standard deviation of Y to get back to the
(non-normalized) values of Y.

Maybe it's a little clearer if we re-arrange the terms:

\[
\begin{align}
Y &= \beta_0 + \beta_1 \cdot X  \\\\[8pt]
&= \beta_0  + Cor(X,Y) \cdot \frac{\sigma_y}{\sigma_x} \cdot X  \\\\[8pt]
&= \beta_0 + Cor(X,Y) \cdot \frac{X}{\sigma_x} \cdot \sigma_y  
\end{align} 
\]


<br />
### The units of covariance, correlation, and coefficients

It sometimes helps me understand things by looking at the units.

* The units of covariance are (units of X)*(units of Y), since X and Y are multiplied together in the covariance equation. 
* Correlation is unit-less, since the units of covariance (X*Y) are cancelled
out when dividing by the standard deviations of both X and Y, which
respectively have (units of X) and (units of Y)
* The units of \(\beta_1\), the coefficient of X, are (units of Y) / (units of X).  When
\(\beta_1\) is multipled by X, the (units of X) cancel out, leaving just (units of Y).

