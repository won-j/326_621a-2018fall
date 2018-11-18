Q1. Big data analysis
---------------------

Our Apache Yarn cluster hosts the [flights](http://stat-computing.org/dataexpo/2009/the-data.html) data representing 123 million flights over 22 years. Read the [lecture notes](../lectures/12-sparklyr/sparklyr-flights.html) on how to access the Yarn cluster. Connect to the database using `sparklyr` and answer following questions. You can base your answers on a specific year or the whole data set.

1.  Map the top 10 busiest airports. Size of dots should reflect the number of flights through that destination.
    Hint: You may find this tutorial on [Making Maps in R](http://eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html) helpful.

2.  Map the top 10 busiest direct routes. Size of lines should reflect the number of flights through that route.

3.  Build a predictive model for the arrival delay (`arrdelay`) of flights flying from JFK. Use the same filtering criteria as in the [lecture notes](../lectures/12-sparklyr/sparklyr-flights.html) to construct training and validation sets. You are allowed to use a maximum of 5 predictors. The prediction performance of your model on the validation data set will be an important factor for grading this question.

4.  Visualize and explain any other information you want to explore.

Q2. Big data algorithm
----------------------

In the [lecture notes](../lectures/12-sparklyr/sparklyr-flights.html), function `ml_linear_regression()`, which takes advantage of Spark library `MLlib` for linear regression. Recall that we have 123 million observations and the standard `lm()` won't work for this size of data. In this question, we explore how big data analysis algorithms like `ml_linear_regression()` is implemented.

1.  The following code

``` r
sc <- spark_connect(master = "local")
sdf_len(sc, 1000) %>%
  spark_apply(function(df) runif(nrow(df), min = -1, max = 1)^2+runif(nrow(df), min = -1, max = 1)^2 < 1) %>% 
  filter(result == TRUE) %>% count() %>% collect() * 4 / 1000
```

computes a Monte Carlo estimation of *π*. Explain, as much in detail as possoble, how the above code does the computation.

Hint. This [lecture note](https://stanford.edu/~rezab/classes/cme323/S17/notes/lecture15/cme323_lec15.pdf) or [paper](./spark16.pdf) (in Korean) may help.

1.  Our goal is to reproduce the linear regression analysis in the [lecture notes](../lectures/12-sparklyr/sparklyr-flights.html) by stochatic gradient descent (SGD). SGD is an approximate optimization method for minimizing function $\\frac{1}{n}\\sum\_{j=1}^n f\_j(\\beta)$ by
    *β*<sup>(*k*)</sup> = *β*<sup>(*k* − 1)</sup> − *γ*<sub>*k*</sub>∇*f*<sub>*i*</sub>(*β*<sup>(*k* − 1)</sup>),
     where *i* is a uniformly sampled index from $1,2,\\dotsc,n$. The quantity *γ*<sub>*k*</sub> is called the step size. Note that $\\mathbf{E}\[\\nabla f\_i(\\beta))\]=\\frac{1}{n}\\sum\_{j=1}^n \\nabla f\_j(\\beta)$. In linear regression, *f*<sub>*i*</sub>(*β*)=(1/2)(*y*<sub>*i*</sub> − *x*<sub>*i*</sub><sup>*T*</sup>*β*)<sup>2</sup>.

Writg an R code that estimates the regression coefficients $\\hat{\\beta}$ from the lecture notes' regression analysis by implementing SGD in `sparklyr` without using the `MLlib` library (i.e., `ml_linear_regression()`).

Hints: - You may find functions `sdf_sample()` and `model.matrix()` useful. - To deal with the categorical variable `UniqueCarrier` properly, you may need to collect all the carriers in the entire dataset before the analysis. - Start from a smaller subset on a local machine (`spark_connect(master = "local")`) to save your time and resource. - Typical choices of the step size *γ*<sub>*k*</sub> are *γ*<sub>*k*</sub> = *γ*<sub>0</sub> (constant step size), *γ*<sub>*k*</sub> = *γ*<sub>0</sub>/*k*, and $\\gamma\_k = \\gamma\_0/\\sqrt{k}$ (diminishing step sizes). SGD is know to be sensitive to the step size.
