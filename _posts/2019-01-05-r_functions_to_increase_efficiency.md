---
layout: post
title: R Helper Functions to Increase Efficiency
mathjax: true
---

At Inspire, we have built an internal R package consisting of all kinds of functions to achieve business functionalities. In this package, we created some helper functions to make some frequent jobs simpler. Some of the helper functions are based on solutions provided on public websites, such as StackOverflow. In this post, I would like to share some of the helper functions we use in daily work. Hope you can benefit or get inspired from them!

1. Calculate weights from a non-negative vector using `lw()`:

    We use this function a lot because we deal with a lot of redistribution jobs. The weight of each entry is calculated as its proportion to the sum of all entries. If the sum is equal to 0, then all weights should be equal across all entries because they should be all equal to 0. The source code is
    ```r
    lw <- function(v) {
          if (sum(v, na.rm = TRUE) == 0) {
                1 / length(v)
          } else {
                v / sum(v, na.rm = TRUE)
          }
    }
    ```
    An example is
    ```r
    > lw(c(1,2,3))
    [1] 0.1666667 0.3333333 0.5000000
    ```

1. Quickly convert date vector in character type to date type using `quick_date()`:

    When we read data from a csv file or from a database through DBI, the date column might still be in character type. People normally use command such as `as.Date()` in base R or `ymd()` in lubridate package, etc. to convert the column into date type. Noticing that the conversion could take a pretty long time for large data set with 10 million plus rows, we have built a function based on some online solution as follows:
    ```r
    quick_date <- function(x, ...) {
        if (anyDuplicated(x)) {
            ux <- unique(x)
            idx <- match(x, ux)
            y <- as.Date.character(ux, ...)
            return(y[idx])
        }
            as.Date.character(x, ...)
    }
    ```

1. Quickly check missing value in data frame using `cc()` and `cd()`:

    Every R user should be very familiar with the `summary()` function to have a quick statistics overview across all columns in a data frame. However, in most of the cases, we only care about a specific statistics. Personally, I care about the missing value in each column very much in my work, so I developed two functions. One is `cc()`, which stands for "check cleanness" for me. The source code is as follows:
    ```r
    cc <- function(df) {
        print(df %>% is.na() %>% colSums())
    }
    ```
    It simply tells you how many missing value in each column. I like this function because I just need to press "c" twice in the keyboard which is much faster than typing "summary". The other function is `cd()`, which stands for "cleanness density" for me. The source code is as follows:
    ```r
    cd <- function(df) {
        print(df %>% is.na() %>% colMeans())
    }
    ```
    It simply tells you the percentage of missing value in each column.

1. Start H2O instance using `h2o_start()`:

    H2O is one of my favorite machine learning frameworks and R packages. I like to start H2o instance with my personal setting other than the default. So I use `h2o_start()` function to wrap the original init function with my preference. The source code is
    ```r
    h2o_start <- function() {
      h2o::h2o.init(nthreads = -1, enable_assertions = FALSE, min_mem_size = '8g',
                    strict_version_check = FALSE, port = 54321)
    }
    ```

1. Stop H2O instance using `h2o_stop()`:

    Just like the above `h2o_start()` function, I like to stop H2O instance with my personal preference as follows
    ```r
    h2o_stop <- function() {
      h2o::h2o.shutdown(prompt = FALSE)
    }
    ```
