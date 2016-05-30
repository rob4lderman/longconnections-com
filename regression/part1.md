## Intro to Regression: Part 1: What is regression? (generally speaking)

Regression analysis is a statistical procedure that examines correlations
between variables in dataset.  Regression relates a single "response" variable
to one or more "predictor" variables using a regression function.

The regression function takes the predictor variables as arguments and returns
a value that is the predicted score of the response variable.  The regression
function is tuned in order to minimize the difference between the predicted and
actual values of the response variable.

<br />
### Terminology

* The "regression function" is aka the "regression model" or simply the "model"
* The "response variable" is aka the "outcome variable" (as in, the "outcome" of the regression function)
* The "predictor variable(s)" are aka the "regressor variable(s)"

<br />
### Types of regression models

The world of regression models can be divided tautologically into two types:

1. Linear models
2. Non-linear models

Linear models are (relatively) easy.  Non-linear models are hard.  Non-linear
models are so hard that statisticians are always looking for ways to transform
them into linear models.  

This blog series will focus exclusively on linear models.  Linear models are
easier to analyze and interpret, and they are much less computationally complex
to solve.  

That said, linear models carry with them a rather lengthy list of assumptions
about the underlying data.  These assumptions are, in part, the reason that
linear models are relatively easy to work with.  We'll cover those assumptions
later on, after we've developed a basic understanding of regression itself.

<br />
<div class="sqs-block-content html-block">
    <h3 class="text-align-center">
        <a href="/regression/">Intro to Regression</a>: 
        1 .
        <a href="/blog/2015/8/23/intro-to-regression-part-2-simple-linear-regression-an-example">2</a> . 
        <a href="/blog/2015/8/23/intro-to-regression-part-3-covariance-and-correlation">3</a> . 
        <a href="/blog/2015/8/24/intro-to-regression-part-4-distribution-of-prediction-errors-residuals-and-goodness-of-fit-rsup2sup">4</a> . 
        <a href="/blog/2015/8/25/intro-to-regression-part-5-interpretting-coefficients-centering-predictor-variables">5</a> ...
    </h3>
</div>

