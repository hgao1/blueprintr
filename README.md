
<!-- README.md is generated from README.Rmd. Please edit that file -->

# blueprintr <img src="man/figures/logo.png" align="right" height="139" />

> blueprintr is a plugin to [drake](https://github.com/ropensci/drake)
> that adds automated steps for tabular dataset documentation and
> testing. Designed for social science research projects, this package
> creates a framework to build trust in your data and to prevent
> programming issues from affecting your analysis results.

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/blueprintr)](https://CRAN.R-project.org/package=blueprintr)
[![R-CMD-check](https://github.com/nyuglobalties/blueprintr/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/nyuglobalties/blueprintr/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

## Usage

Define blueprints of your data using `blueprint()`. Blueprints combine
drake target commands with some extra metadata about the output tabular
dataset, including the name and description of the data.

``` r
library(blueprintr)

blueprint1 <- blueprint(
  "blueprint1",
  description = "My first blueprint",
  command = {
    # Put all code related to building this dataset here
    mtcars
  }
)

blueprint1
#> <blueprint: 'blueprint1'>
#> 
#> Description: My first blueprint
#> Annotations: DISABLED
#> Metadata location: '/Users/patrickanker/dev/blueprintr/blueprints/blueprint1.csv'
#> 
#> -- Command --
#> Workflow command:
#> {
#>     mtcars
#> }
#> 
#> Raw command:
#> {
#>     mtcars
#> }
```

Refer to other datasets using `.TARGET()` to guarantee that parent
datasets are also tested and documented. Run checks on the dataset
entirely with the `checks` parameter and define variable tests in the
metadata files.

``` r
no_missing_cyl <- function(df) {
  all(!is.na(df$cyl))
}

blueprint2 <- blueprint(
  "blueprint2",
  description = "My second blueprint that depends on another",
  checks = check_list(
    no_missing_cyl()
  ),
  command =
    .TARGET("blueprint1") %>%
      filter(cyl == 4)
)
```

Once all blueprints are defined, attach them to a plan using
`attach_blueprint()` or `attach_blueprints()` so drake can run the
needed tasks.

``` r
library(magrittr)
library(drake)

plan_from_blueprint(blueprint1) %>%
  attach_blueprint(blueprint2)
```

drake handles the code execution based on the targets generated by
blueprintr.

## Installation

As blueprintr is not yet on CRAN, you must install the package from this
repository:

``` r
install.packages("remotes")
remotes::install_github("nyuglobalties/blueprintr")
```

## Contributing

Please note that the ‘blueprintr’ project is released with a
[Contributor Code of Conduct](.github/CODE_OF_CONDUCT.md). By
contributing to this project, you agree to abide by its terms.
