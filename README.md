
<!-- README.md is generated from README.Rmd. Please edit only README.Rmd! -->

# roxytest

Inline tests with roxygen and testthat.

See demo package using this at
[roxytestdemo](https://github.com/mikldk/roxytestdemo).

## Usage

There are a number of roclets included:

  - `testthat_roclet`: Write [`testthat`](https://testthat.r-lib.org/)
    tests in the [`roxygen2`](https://roxygen2.r-lib.org/) documentation
      - `@tests`: Generate test skeletons
      - `@testexamples`: Generate test skeletons with `@examples`
        included first such that e.g. variables can be reused
  - `param_roclet`: Checks for consistency in documentation of
    parameters (too many/too few)
  - `return_roclet`: Checks if `@export`ed functions has `@return`
    documentation (possibly just `@return None`)
  - `examples_roclet`: Checks if `@export`ed functions has `@examples`
    documentation

To use the package in your own package you do not need to add any
additional dependencies in your package’s `DESCRIPTION` file apart from
the usual `Suggests: testthat` that is required for testing with
`testthat`. (If you only use `param_roclet`, `return_roclet` or
`examples_roclet` you do not need to add `testthat` to `Suggests`.)

    Suggests:
      testthat

However, any developer working on your package needs to have `roxytest`
installed to be able to successfully run `roxygen2::roxygenise()` (or
`devtools::document()`). For this reason you may consider adding
`roxytest` to `Suggests`, but this is not required.

### `testthat` roclet

Add the following lines to your package’s `DESCRIPTION` file (along with
`Suggests:
    testthat`):

    Roxygen: list(roclets = c("namespace", "rd", "roxytest::testthat_roclet"))

(Or make appropriate changes to obtain similar results.)

Then run the following:

``` r
roxygen2::roxygenise()
```

Similarly for the other roclets.

### Multiple roclets

You can of course also add multiple, e.g.:

    Roxygen: list(roclets = c("namespace", "rd", 
                              "roxytest::testthat_roclet",
                              "roxytest::param_roclet",
                              "roxytest::return_roclet"))

(Or make appropriate changes to obtain similar results.)

Then run the following:

``` r
roxygen2::roxygenise()
```

## Examples

### `testthat` roclet

As mentioned above, there are two tags available:

  - `@tests`: Generate test skeletons
  - `@testexamples`: Generate test skeletons with `@examples` included
    first such that e.g. variables can be reused

Note that both can be used at the same time.

We first demonstrate `@tests` and afterwards `@testexamples`.

#### `@tests` tag

For example, if the file `R/functions.R` contains this code (from
[roxytestdemo](https://github.com/mikldk/roxytestdemo)):

``` r
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

Then `roxygen2::roxygenise()` will generate (with the `testthat_roclet`
roclet) the file `tests/testthat/test-roxytest-tests-functions.R` with
this content:

``` r
# Generated by roxytest: Do not edit by hand!

context("File R/functions.R: @tests")

test_that("Function foo() @ L10", {
  expect_equal(foo(2), sqrt(2))
  expect_error(foo("a string"))
})


test_that("Function bar() @ L23", {
  expect_equal(bar("A", "B"), paste("A", "B", sep = "/"))
})
```

#### `@testexamples` tag

For example, if the file `R/functions.R` contains this code (from
[roxytestdemo](https://github.com/mikldk/roxytestdemo)):

``` r
#' A function to do w
#' 
#' @param x A number
#' 
#' @examples 
#' x <- 2
#' foo(x)
#' 
#' @testexamples
#' expect_equal(foo(x), foo(2))
#' 
#' @return something
foo2 <- function(x) {
  return(sqrt(x))
}
```

Then `roxygen2::roxygenise()` will generate (with the `testthat_roclet`
roclet) the file `tests/testthat/test-roxytest-testexamples-functions.R`
with this content:

``` r
# Generated by roxytest: Do not edit by hand!

context("File R/functions.R: @testexamples")

test_that("Function foo2() @ L13", {
  
  x <- 2
  foo(x)
  
  expect_equal(foo(x), foo(2))
})
```

### `param` roclet

To demonstrate the `param_roclet` roclet assume that this block of
documentation exists:

``` r
#' Summing two numbers
#'
#' @param x A number
foobar <- function(x, y) {
  x + y
}
```

When the package is documented, the following output will be displayed:

    Functions with @param inconsistency:
      * Function 'foobar' with title 'Summing two numbers': 
        - Missing @param's: y

Similarly if there are too many documented arguments.

### `return` roclet

To demonstrate the `return_roclet` roclet assume that this block of
documentation exists:

``` r
#' Summing two numbers
#'
#' @param x A number
#' 
#' @export
foobar2 <- function(x, y) {
  x + y
}
```

When the package is documented, the following output will be displayed:

    Functions with @export but no @return:
      * Function 'foobar2()' with title 'Summing two numbers'

### `examples` roclet

To demonstrate the `examples_roclet` roclet assume that this block of
documentation exists:

``` r
#' Summing two numbers
#'
#' @param x A number
#' 
#' @export
foobar2 <- function(x, y) {
  x + y
}
```

When the package is documented, the following output will be displayed:

    Functions with @export but no @example(s):
      * Function 'foobar2()' with title 'Summing two numbers'

## Notes

  - Rstudio: CTRL+SHIFT+D option does not run `devtools::document()` but
    only `devtools::document(roclets=c('rd', 'collate', 'namespace'))`
    (possibly with fewer, but not none)
      - Use `devtools`’ `document()` addin: see e.g. [Rstudio
        documentation](https://rstudio.github.io/rstudioaddins/), and
        override the keyboard shortcut
