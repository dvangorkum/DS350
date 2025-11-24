---
title: "W12_Task_Chipotle"
author: "Daniel Van Gorkum"
date: "November 23, 2025"
format: html
editor: visual
execute:
  echo: true
  warning: false
  message: false
  keep-md: true
---


::: {.cell}

```{.r .cell-code}
library(tidyverse)
library(stringr)


pop_string_to_tibble <- function(pop_string) {


  clean <- pop_string |>
    str_replace_all("“", "\"") |>
    str_replace_all("”", "\"") |>
    str_remove_all("[{}]")

  # Extract day and visits
  df <- str_match_all(clean, "\"(\\w+)\":(\\d+)")[[1]] |>
    as_tibble() |>
    select(day = V2, visits = V3) |>
    mutate(visits = as.numeric(visits))

  return(df)
}
```
:::



::: {.cell}

```{.r .cell-code}
#---- Function 2 -------------------------------------------------------------

most_popular_day <- function(pop_string) {
  
  df <- pop_string_to_tibble(pop_string)
  
  max_visits <- max(df$visits)
  
  top_days <- df |> 
    filter(visits == max_visits) |> 
    pull(day)
  
  # Return comma-separated string if ties
  return(paste(top_days, collapse = ", "))
}
```
:::



::: {.cell}

```{.r .cell-code}
test1 <- "{“Monday”:94,“Tuesday”:76,“Wednesday”:89,“Thursday”:106,“Friday”:130,“Saturday”:128,“Sunday”:58}"

pop_string_to_tibble(test1)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 7 × 2
  day       visits
  <chr>      <dbl>
1 Monday        94
2 Tuesday       76
3 Wednesday     89
4 Thursday     106
5 Friday       130
6 Saturday     128
7 Sunday        58
```


:::

```{.r .cell-code}
most_popular_day(test1)
```

::: {.cell-output .cell-output-stdout}

```
[1] "Friday"
```


:::
:::



::: {.cell}

```{.r .cell-code}
test2 <- "{“Monday”:18,“Tuesday”:16,“Wednesday”:14,“Thursday”:27,“Friday”:26,“Saturday”:36,“Sunday”:20}"

pop_string_to_tibble(test2)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 7 × 2
  day       visits
  <chr>      <dbl>
1 Monday        18
2 Tuesday       16
3 Wednesday     14
4 Thursday      27
5 Friday        26
6 Saturday      36
7 Sunday        20
```


:::

```{.r .cell-code}
most_popular_day(test2)
```

::: {.cell-output .cell-output-stdout}

```
[1] "Saturday"
```


:::
:::



::: {.cell}

```{.r .cell-code}
test3 <- "{“Monday”:0,“Tuesday”:0,“Wednesday”:1,“Thursday”:0,“Friday”:0,“Saturday”:1,“Sunday”:0}"

pop_string_to_tibble(test3)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 7 × 2
  day       visits
  <chr>      <dbl>
1 Monday         0
2 Tuesday        0
3 Wednesday      1
4 Thursday       0
5 Friday         0
6 Saturday       1
7 Sunday         0
```


:::

```{.r .cell-code}
most_popular_day(test3)
```

::: {.cell-output .cell-output-stdout}

```
[1] "Wednesday, Saturday"
```


:::
:::

