
<!-- README.md is generated from README.qmd. Please edit that file -->

# leni: Linear Estimation with Nonlinear Inference

[Ethan M. McCormick](https://e-m-mccormick.github.io/) \| Department of
Methodology & Statistics \| Leiden University

The `leni` package provides a collection of tools for Linear Estimation
with Nonlinear Inference (LENI) which involves estimating the parameters
of nonlinear target functions as transformations of linear models.
Includes support for regression and mixed-effects models (main function:
`leni()`) which produces transformed parameter estimates, as well as
structural equation models (main function: `leni_sem()`) which generates
`lavaan` syntax (https://lavaan.ugent.be/) for fitting linearized SEMs.

------------------------------------------------------------------------

## Installation

Set up for (CRAN)\[https://CRAN.R-project.org/\] installation of `leni`
is underway.

In the meantime, to install the latest development version, you can
install `leni` directly from github using the `devtools` package.

``` r
#install.packages("devtools")
library(devtools)

install_github("e-m-mcormick/leni")
```

------------------------------------------------------------------------

## Questions

For questions, answers, and updates on the status of the `leni` package,
please email EMM.

------------------------------------------------------------------------

## Examples

To perform LENI on an `lm` model or a model of class `lme` (currently
supports models from
[`lme4`](https://cran.r-project.org/web/packages/lme4/index.html),
[`lmerTest`](https://cran.r-project.org/web/packages/lmerTest/index.html),
and [`nlme`](https://cran.r-project.org/web/packages/nlme/index.html)),
use the `leni()` function.

``` r
leni_fit <- leni(model, target_fx = "quadratic", theta = c("a0","ax","ay"),
                 model.class = "lme", bootstrap = FALSE)
```

To generate `lavaan` syntax, users can either use predefined functions
like the quadratic in [Cudeck & du Toit (2002)]() or cubic from
[McCormick (2023)]() using the code below, or input their own custom
target function with a list of parameters in `theta`.

``` r
quadratic_syntax <- leni_sem(target_fx = "quadratic", number_obs = 5, spacing = 2)

cubic_syntax <- leni_sem(target_fx = "cubic_betaN", spacing = c(1,2,4,6,10))
```

Using the `verbose = TRUE` argument will output display-friendly syntax
if users wish to copy it out for their own code.

    # Define Factors

    xN =~ xN.1*y.1 + xN.2*y.2 + xN.4*y.4 + xN.8*y.8 + xN.14*y.14 + xN.24*y.24;
    yN =~ yN.1*y.1 + yN.2*y.2 + yN.4*y.4 + yN.8*y.8 + yN.14*y.14 + yN.24*y.24;
    d =~ d.1*y.1 + d.2*y.2 + d.4*y.4 + d.8*y.8 + d.14*y.14 + d.24*y.24;
    betaN =~ betaN.1*y.1 + betaN.2*y.2 + betaN.4*y.4 + betaN.8*y.8 + betaN.14*y.14 + betaN.24*y.24;

    xN ~ 0*1;
    yN ~ 0*1;
    d ~ 0*1;
    betaN ~ 0*1;

    xN ~~ xN; 
    yN ~~ xN; 
    d ~~ xN; 
    betaN ~~ xN; 
    yN ~~ yN; 
    d ~~ yN; 
    betaN ~~ yN; 
    d ~~ d; 
    betaN ~~ d; 
    betaN ~~ betaN

    # Define Phantoms

    xN_ph =~ 0; xN_ph ~ NA*1 + label("xN_m")*1 + 0?1;
    yN_ph =~ 0; yN_ph ~ NA*1 + label("yN_m")*1 + 0?1;
    d_ph =~ 0; d_ph ~ NA*1 + label("d_m")*1 + 0?1;
    betaN_ph =~ 0; betaN_ph ~ NA*1 + label("betaN_m")*1 + 0?1;

    # Define Items

    y.1 ~ nu.1*1;
    y.2 ~ nu.2*1;
    y.4 ~ nu.4*1;
    y.8 ~ nu.8*1;
    y.14 ~ nu.14*1;
    y.24 ~ nu.24*1;

    y.1 ~~ epsilon.1*y.1;
    y.2 ~~ epsilon.2*y.2;
    y.4 ~~ epsilon.4*y.4;
    y.8 ~~ epsilon.8*y.8;
    y.14 ~~ epsilon.14*y.14;
    y.24 ~~ epsilon.24*y.24;

    # Define Constraints

    nu.1 == yN_m - (betaN_m * d_m/3) * (((0.000000 - xN_m)/d_m)^3 - 3 * ((0.000000 - xN_m)/d_m));
    nu.2 == yN_m - (betaN_m * d_m/3) * (((1.000000 - xN_m)/d_m)^3 - 3 * ((1.000000 - xN_m)/d_m));
    nu.4 == yN_m - (betaN_m * d_m/3) * (((3.000000 - xN_m)/d_m)^3 - 3 * ((3.000000 - xN_m)/d_m));
    nu.8 == yN_m - (betaN_m * d_m/3) * (((7.000000 - xN_m)/d_m)^3 - 3 * ((7.000000 - xN_m)/d_m));
    nu.14 == yN_m - (betaN_m * d_m/3) * (((13.000000 - xN_m)/d_m)^3 - 3 * ((13.000000 - xN_m)/d_m));
    nu.24 == yN_m - (betaN_m * d_m/3) * (((23.000000 - xN_m)/d_m)^3 - 3 * ((23.000000 - xN_m)/d_m));

    xN.1 == (betaN_m * d_m/3) * (3 * (1/d_m * ((0.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));
    xN.2 == (betaN_m * d_m/3) * (3 * (1/d_m * ((1.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));
    xN.4 == (betaN_m * d_m/3) * (3 * (1/d_m * ((3.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));
    xN.8 == (betaN_m * d_m/3) * (3 * (1/d_m * ((7.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));
    xN.14 == (betaN_m * d_m/3) * (3 * (1/d_m * ((13.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));
    xN.24 == (betaN_m * d_m/3) * (3 * (1/d_m * ((23.000000 - xN_m)/d_m)^2) - 3 * (1/d_m));

    yN.1 == 1;
    yN.2 == 1;
    yN.4 == 1;
    yN.8 == 1;
    yN.14 == 1;
    yN.24 == 1;

    d.1 == -(betaN_m/3 * (((0.000000 - xN_m)/d_m)^3 - 3 * ((0.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((0.000000 - xN_m)/d_m^2 * ((0.000000 - xN_m)/d_m)^2) - 3 * ((0.000000 - xN_m)/d_m^2)));
    d.2 == -(betaN_m/3 * (((1.000000 - xN_m)/d_m)^3 - 3 * ((1.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((1.000000 - xN_m)/d_m^2 * ((1.000000 - xN_m)/d_m)^2) - 3 * ((1.000000 - xN_m)/d_m^2)));
    d.4 == -(betaN_m/3 * (((3.000000 - xN_m)/d_m)^3 - 3 * ((3.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((3.000000 - xN_m)/d_m^2 * ((3.000000 - xN_m)/d_m)^2) - 3 * ((3.000000 - xN_m)/d_m^2)));
    d.8 == -(betaN_m/3 * (((7.000000 - xN_m)/d_m)^3 - 3 * ((7.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((7.000000 - xN_m)/d_m^2 * ((7.000000 - xN_m)/d_m)^2) - 3 * ((7.000000 - xN_m)/d_m^2)));
    d.14 == -(betaN_m/3 * (((13.000000 - xN_m)/d_m)^3 - 3 * ((13.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((13.000000 - xN_m)/d_m^2 * ((13.000000 - xN_m)/d_m)^2) - 3 * ((13.000000 - xN_m)/d_m^2)));
    d.24 == -(betaN_m/3 * (((23.000000 - xN_m)/d_m)^3 - 3 * ((23.000000 - xN_m)/d_m)) - (betaN_m * d_m/3) * (3 * ((23.000000 - xN_m)/d_m^2 * ((23.000000 - xN_m)/d_m)^2) - 3 * ((23.000000 - xN_m)/d_m^2)));

    betaN.1 == -(d_m/3 * (((0.000000 - xN_m)/d_m)^3 - 3 * ((0.000000 - xN_m)/d_m)));
    betaN.2 == -(d_m/3 * (((1.000000 - xN_m)/d_m)^3 - 3 * ((1.000000 - xN_m)/d_m)));
    betaN.4 == -(d_m/3 * (((3.000000 - xN_m)/d_m)^3 - 3 * ((3.000000 - xN_m)/d_m)));
    betaN.8 == -(d_m/3 * (((7.000000 - xN_m)/d_m)^3 - 3 * ((7.000000 - xN_m)/d_m)));
    betaN.14 == -(d_m/3 * (((13.000000 - xN_m)/d_m)^3 - 3 * ((13.000000 - xN_m)/d_m)));
    betaN.24 == -(d_m/3 * (((23.000000 - xN_m)/d_m)^3 - 3 * ((23.000000 - xN_m)/d_m)));

------------------------------------------------------------------------

## Licenses

**Text and figures:** All text and images are licensed under Creative
Commons ([CC-BY-4.0](http://creativecommons.org/licenses/by/4.0/))

<!-- **Code:** All code is licensed under the [MIT License](LICENSE.md). -->