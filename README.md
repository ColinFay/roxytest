# roxytest

Inline tests with roxygen and testthat.

See demo package using this at [roxytest-demo](https://github.com/mikldk/roxytest-demo).

## Usage

Add the following lines to your package's `DESCRIPTION` file:
```
Imports:
    roxygen2, 
    testthat,
    roxytest
Roxygen: list(roclets = c("namespace", "rd", "testthat_roclet"))
```
(Or make appropriate changes to obtain similar results.)

And the run the following (note the need to first load the `roxytest` library):
```r
library(roxytest)
roxygen2::roxygenise()
```
 

## Example

For example, if the file `R/functions.R` contains this code (from [roxytest-demo](https://github.com/mikldk/roxytest-demo)):

```r
#' A function to do x
#' 
#' @param x A number
#' 
#' @tests 
#' expect_equal(foo(2), sqrt(2))
#' expect_error(foo("a string"))
#' 
#' @return something
foo <- function(x) {
  return(sqrt(x))
}

#' A function to do y
#' 
#' @param x Character vector
#' @param y Character vector
#' 
#' @tests 
#' expect_equal(bar("A", "B"), paste("A", "B", sep = "/"))
#' 
#' @export
bar <- function(x, y) {
  paste0(x, "/", y)
}
```

Then `roxygen2::roxygenise()` will generated (with the `testthat_roclet` roclet) 
the file `tests/testthat/test-roxytest-functions.R` with this content:

```r
# Generated by roxytest: Do not edit by hand!

context("File R/functions.R")

test_that("Function foo()", {
  expect_equal(foo(2), sqrt(2))
  expect_error(foo("a string"))
})


test_that("Function bar()", {
  expect_equal(bar("A", "B"), paste("A", "B", sep = "/"))
})
```

## Wish-list

* Rstudio: CTRL+SHIFT+D would run `roxygen2::roxygenise()` instead of `devtools::document(roclets=c('rd', 'collate', 'namespace'))`
    + Project options -> Build tools -> If all check marks are removed, nothing happens with CTRL+SHIFT+D. If instead `devtools::document()` would be ran it would work.
* `roxygen2`: 
    + In `DESCRIPTION`, `Roxygen: list(roclets = c("namespace", "rd", "testthat_roclet"))` must be added. It would be more consistent to omit `_roclet`.
    + It would be convenient to avoid the need to call `library(roxytest)` first.
    + Easier test; `roxygen2` uses `roxygen2::roc_proc_text`; it would be nice to be able to use multiple roclets



