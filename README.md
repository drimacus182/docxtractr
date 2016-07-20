
<!-- README.md is generated from README.Rmd. Please edit that file -->
[![Travis-CI Build Status](https://travis-ci.org/hrbrmstr/docxtractr.svg?branch=master)](https://travis-ci.org/hrbrmstr/docxtractr) [![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/docxtractr)](http://cran.r-project.org/package=docxtractr)

![](docxtractr-logo.png)

docxtractr is an R package for extracting tables & comments out of Word documents (docx). Development versions are available here and production versions are [on CRAN](https://cran.rstudio.com/web/packages/docxtractr/index.html).

Microsoft Word docx files provide an XML structure that is fairly straightforward to navigate, especially when it applies to Word tables. The docxtractr package provides tools to determine table count, table structure and extract tables from Microsoft Word docx documents.

Many tables in Word documents are in twisted formats where there may be labels or other oddities mixed in that make it difficult to work with the underlying data. `docxtractr` provides a function—`assign_colnames`—that makes it easy to identify a particular row in a scraped (or any, really) `data.frame` as the one containing column names and have it become the column names, removing it and (optionally) all of the rows before it (since that's usually what needs to be done).

The following functions are implemented:

-   `read_docx`: Read in a Word document for table extraction
-   `docx_describe_tbls`: Returns a description of all the tables in the Word document
-   `docx_describe_cmntss`: Returns a description of all the comments in the Word document
-   `docx_extract_tbl`: Extract a table from a Word document
-   `docx_extract_cmnts`: Extract comments from a Word document
-   `docx_extract_all`: Extract all tables from a Word document (deprecated)
-   `docx_tbl_count`: Get number of tables in a Word document
-   `docx_cmnt_count`: Get number of comments in a Word document
-   `assign_colnames`: Make a specific row the column names for the specified data.frame

The following data file are included:

-   `system.file("examples/data.docx", package="docxtractr")`: Word docx with 1 table
-   `system.file("examples/data3.docx", package="docxtractr")`: Word docx with 3 tables
-   `system.file("examples/none.docx", package="docxtractr")`: Word docx with 0 tables
-   `system.file("examples/complex.docx", package="docxtractr")`: Word docx with non-uniform tables
-   `system.file("examples/comments.docx", package="docxtractr")`: Word docx with comments
-   `system.file("examples/realworld.docx", package="docxtractr")`: A "real world" Word docx file with tables of all shapes and sizes

### Installation

``` r
# devtools::install_github("hrbrmstr/docxtractr")
# OR 
install.packages("docxtractr")
```

### Usage

``` r
library(docxtractr)
library(tibble)
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union

# current verison
packageVersion("docxtractr")
#> [1] '0.2.0'

# one table
doc <- read_docx(system.file("examples/data.docx", package="docxtractr"))

docx_tbl_count(doc)
#> [1] 1

docx_describe_tbls(doc)
#> Word document [/Library/Frameworks/R.framework/Versions/3.3/Resources/library/docxtractr/examples/data.docx]
#> 
#> Table 1
#>   total cells: 16
#>   row count  : 4
#>   uniform    : likely!
#>   has header : likely! => possibly [This, Is, A, Column]

docx_extract_tbl(doc, 1)
#>   This      Is     A   Column
#> 1    1     Cat   3.4      Dog
#> 2    3    Fish 100.3     Bird
#> 3    5 Pelican   -99 Kangaroo

docx_extract_tbl(doc)
#>   This      Is     A   Column
#> 1    1     Cat   3.4      Dog
#> 2    3    Fish 100.3     Bird
#> 3    5 Pelican   -99 Kangaroo

docx_extract_tbl(doc, header=FALSE)
#> NOTE: header=FALSE but table has a marked header row in the Word document
#>     V1      V2    V3       V4
#> 1 This      Is     A   Column
#> 2    1     Cat   3.4      Dog
#> 3    3    Fish 100.3     Bird
#> 4    5 Pelican   -99 Kangaroo

# url 

budget <- read_docx("http://rud.is/dl/1.DOCX")

docx_tbl_count(budget)
#> [1] 2

docx_describe_tbls(budget)
#> Word document [http://rud.is/dl/1.DOCX]
#> 
#> Table 1
#>   total cells: 24
#>   row count  : 6
#>   uniform    : likely!
#>   has header : unlikely
#> 
#> Table 2
#>   total cells: 28
#>   row count  : 4
#>   uniform    : likely!
#>   has header : unlikely

docx_extract_tbl(budget, 1)
#>                                      Short-term Portfolio Long-term Portfolio Total Portfolio Values
#> 1 Portfolio Balance (Market Value) *       $  123,651,911       $ 294,704,136          $ 418,356,047
#> 2                    Effective Yield               0.16 %              1.42 %                 1.05 %
#> 3             Avg. Weighted Maturity              11 Days           2.4 Years              1.7 Years
#> 4                       Net Earnings        $      18,470      $      350,554         $      369,024
#> 5                        Benchmark**               0.02 %              0.41 %                 0.27 %

docx_extract_tbl(budget, 2) 
#>                        Amount of Funds (Market Value)  Maturity Effective Yield Interpolated Yield
#> 1 Short-Term Portfolio                  $ 123,651,911   11 days          0.16 %             0.01 %
#> 2  Long-Term Portfolio                  $ 294,704,136 2.4 years          1.42 %             0.41 %
#> 3      Total Portfolio                  $ 418,356,047 1.7 years          1.05 %             0.27 %
#>   Total Return  Monthly Total Return    Annual
#> 1                 0.013                  0.160
#> 2                 0.437                  0.250
#> 3                 0.298                  0.222

# three tables
doc3 <- read_docx(system.file("examples/data3.docx", package="docxtractr"))

docx_tbl_count(doc3)
#> [1] 3

docx_describe_tbls(doc3)
#> Word document [/Library/Frameworks/R.framework/Versions/3.3/Resources/library/docxtractr/examples/data3.docx]
#> 
#> Table 1
#>   total cells: 16
#>   row count  : 4
#>   uniform    : likely!
#>   has header : likely! => possibly [This, Is, A, Column]
#> 
#> Table 2
#>   total cells: 12
#>   row count  : 4
#>   uniform    : likely!
#>   has header : likely! => possibly [Foo, Bar, Baz]
#> 
#> Table 3
#>   total cells: 14
#>   row count  : 7
#>   uniform    : likely!
#>   has header : likely! => possibly [Foo, Bar]

docx_extract_tbl(doc3, 3)
#>   Foo Bar
#> 1  Aa  Bb
#> 2  Dd  Ee
#> 3  Gg  Hh
#> 4   1   2
#> 5  Zz  Jj
#> 6  Tt  ii

# no tables
none <- read_docx(system.file("examples/none.docx", package="docxtractr"))

docx_tbl_count(none)
#> [1] 0

# wrapping in try since it will return an error
# use docx_tbl_count before trying to extract in scripts/production
try(docx_describe_tbls(none))
#> No tables in document
try(docx_extract_tbl(none, 2))

# 5 tables, with two in sketchy formats
complx <- read_docx(system.file("examples/complex.docx", package="docxtractr"))

docx_tbl_count(complx)
#> [1] 5

docx_describe_tbls(complx)
#> Word document [/Library/Frameworks/R.framework/Versions/3.3/Resources/library/docxtractr/examples/complex.docx]
#> 
#> Table 1
#>   total cells: 16
#>   row count  : 4
#>   uniform    : likely!
#>   has header : likely! => possibly [This, Is, A, Column]
#> 
#> Table 2
#>   total cells: 12
#>   row count  : 4
#>   uniform    : likely!
#>   has header : likely! => possibly [Foo, Bar, Baz]
#> 
#> Table 3
#>   total cells: 14
#>   row count  : 7
#>   uniform    : likely!
#>   has header : likely! => possibly [Foo, Bar]
#> 
#> Table 4
#>   total cells: 11
#>   row count  : 4
#>   uniform    : unlikely => found differing cell counts (3, 2) across some rows
#>   has header : likely! => possibly [Foo, Bar, Baz]
#> 
#> Table 5
#>   total cells: 21
#>   row count  : 7
#>   uniform    : likely!
#>   has header : unlikely

docx_extract_tbl(complx, 3, header=TRUE)
#>   Foo Bar
#> 1  Aa  Bb
#> 2  Dd  Ee
#> 3  Gg  Hh
#> 4   1   2
#> 5  Zz  Jj
#> 6  Tt  ii

docx_extract_tbl(complx, 4, header=TRUE)
#>   Foo  Bar  Baz
#> 1  Aa BbCc <NA>
#> 2  Dd   Ee   Ff
#> 3  Gg   Hh   ii

docx_extract_tbl(complx, 5, header=TRUE)
#>    Foo Bar Baz
#> 1   Aa  Bb  Cc
#> 2   Dd  Ee  Ff
#> 3   Gg  Hh  Ii
#> 4 Jj88  Kk  Ll
#> 5       Uu  Ii
#> 6   Hh  Ii   h

# a "real" Word doc
real_world <- read_docx(system.file("examples/realworld.docx", package="docxtractr"))

docx_tbl_count(real_world)
#> [1] 8

# get all the tables
tbls <- docx_extract_all(real_world)
#> docx_extract_all() is deprecated; use docx_extract_all_tbls()

# see table 1
tbls[[1]]
#>                  V1        V2         V3                     V4                     V5
#> 1 Lesson 1:  Step 1      <NA>       <NA>                   <NA>                   <NA>
#> 2           Country Birthrate Death Rate Population Growth 2005 Population Growth 2050
#> 3               USA      2.06      0.51%                  0.92%                 -0.06%
#> 4             China      1.62       0.3%                   0.6%                 -0.58%
#> 5             Egypt      2.83      0.41%                   2.0%                  1.32%
#> 6             India      2.35      0.34%                  1.56%                  0.76%
#> 7             Italy      1.28      0.72%                  0.35%                 -1.33%
#> 8            Mexico      2.43      0.25%                  1.41%                  0.96%
#> 9           Nigeria      4.78      0.26%                  2.46%                  3.58%
#>                                    V6                      V7                   V8                              V9
#> 1                                <NA>                    <NA>                 <NA>                            <NA>
#> 2        Relative place in Transition        Social Factors 1     Social Factors 2                Social Factors 3
#> 3                    Post- Industrial     Female Independence    Stable Birth Rate                 Good technology
#> 4                    Post- Industrial Government intervention           Technology                    Urbanization
#> 5                   Mature Industrial  Not yet industrialized More children needed Slightly higher life expectancy
#> 6                     Post Industrial         Economic growth              Poverty    Becoming more industrialized
#> 7                Late Post industrial       Stable birth rate   People marry later              Better health care
#> 8                   Mature Industrial      Better health care           Emigration                 Economic growth
#> 9 End of Mechanization of Agriculture                 Disease   People marry early       People have many children

#' # make table 1 better
assign_colnames(tbls[[1]], 2)
#>   Country Birthrate Death Rate Population Growth 2005 Population Growth 2050        Relative place in Transition
#> 1     USA      2.06      0.51%                  0.92%                 -0.06%                    Post- Industrial
#> 2   China      1.62       0.3%                   0.6%                 -0.58%                    Post- Industrial
#> 3   Egypt      2.83      0.41%                   2.0%                  1.32%                   Mature Industrial
#> 4   India      2.35      0.34%                  1.56%                  0.76%                     Post Industrial
#> 5   Italy      1.28      0.72%                  0.35%                 -1.33%                Late Post industrial
#> 6  Mexico      2.43      0.25%                  1.41%                  0.96%                   Mature Industrial
#> 7 Nigeria      4.78      0.26%                  2.46%                  3.58% End of Mechanization of Agriculture
#>          Social Factors 1     Social Factors 2                Social Factors 3
#> 1     Female Independence    Stable Birth Rate                 Good technology
#> 2 Government intervention           Technology                    Urbanization
#> 3  Not yet industrialized More children needed Slightly higher life expectancy
#> 4         Economic growth              Poverty    Becoming more industrialized
#> 5       Stable birth rate   People marry later              Better health care
#> 6      Better health care           Emigration                 Economic growth
#> 7                 Disease   People marry early       People have many children

# see table 5
tbls[[5]]
#>                  V1      V2            V3        V4        V5       V6
#> 1 Lesson 2:  Step 1    <NA>          <NA>      <NA>      <NA>     <NA>
#> 2           Nigeria Default    Prediction + 5 years +15 years -5 years
#> 3        Birth rate    4.78     Goes Down      4.76      4.72     4.79
#> 4        Death rate   0.36% Stay the Same     0.42%     0.52%     0.3%
#> 5 Population growth   3.58%     Goes Down     3.02%     2.32%    4.38%

# make table 5 better
assign_colnames(tbls[[5]], 2)
#>             Nigeria Default    Prediction + 5 years +15 years -5 years
#> 1        Birth rate    4.78     Goes Down      4.76      4.72     4.79
#> 2        Death rate   0.36% Stay the Same     0.42%     0.52%     0.3%
#> 3 Population growth   3.58%     Goes Down     3.02%     2.32%    4.38%

# comments
cmnts <- read_docx(system.file("examples/comments.docx", package="docxtractr"))

print(cmnts)
#> No tables in document
#> Word document [/Library/Frameworks/R.framework/Versions/3.3/Resources/library/docxtractr/examples/comments.docx]
#> 
#> Found 3 comments.
#> # A tibble: 1 x 2
#>      author # Comments
#>       <chr>      <int>
#> 1 boB Rudis          3

glimpse(docx_extract_all_cmnts(cmnts))
#> Observations: 3
#> Variables: 5
#> $ id           <chr> "0", "1", "2"
#> $ author       <chr> "boB Rudis", "boB Rudis", "boB Rudis"
#> $ date         <chr> "2016-07-01T21:09:00Z", "2016-07-01T21:09:00Z", "2016-07-01T21:09:00Z"
#> $ initials     <chr> "bR", "bR", "bR"
#> $ comment_text <chr> "This is the first comment", "This is the second comment", "This is a reply to the second comm...
```

### Test Results

``` r
library(docxtractr)
library(testthat)
#> 
#> Attaching package: 'testthat'
#> The following object is masked from 'package:dplyr':
#> 
#>     matches

date()
#> [1] "Tue Jul 19 22:56:37 2016"

test_dir("tests/")
#> testthat results ========================================================================================================
#> OK: 10 SKIPPED: 0 FAILED: 0
#> 
#> DONE ===================================================================================================================
```

### Code of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.
