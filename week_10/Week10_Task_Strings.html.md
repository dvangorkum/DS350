---
title: "W10_Task_Strings"
author: "Daniel Van Gorkum"
date: "November 15, 2025"
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
# hidden_messages.R
# This script solves the Week 10 tasks for hidden messages

library(readr)

# -----------------------------
# 1. Read in the files
# -----------------------------
letters_only <- read_lines("~/Downloads/randomletters.txt")
letters_numbers <- read_lines("~/Downloads/randomletters_wnumbers.txt")

# Combine lines into single strings
letters_only_str <- paste(letters_only, collapse = "")
letters_numbers_str <- paste(letters_numbers, collapse = "")

# -----------------------------
# 2. Extract every 1700th letter to find the hidden quote
# -----------------------------
every_1700 <- substring(letters_only_str, seq(1, nchar(letters_only_str), 1700),
                        seq(1, nchar(letters_only_str), 1700))

hidden_quote <- paste(every_1700, collapse = "")
# Keep text up to the first period
hidden_quote <- sub("\\..*$", ".", hidden_quote)

cat("Hidden quote from randomletters.txt:\n", hidden_quote, "\n\n")
```

::: {.cell-output .cell-output-stdout}

```
Hidden quote from randomletters.txt:
 tpblhtk. 
```


:::

```{.r .cell-code}
# -----------------------------
# 3. Decode numbers into letters
# -----------------------------
# Extract all numbers
numbers <- as.integer(unlist(regmatches(letters_numbers_str, gregexpr("\\d+", letters_numbers_str))))

# Convert numbers to letters (1=a, 2=b, ..., 26=z)
decoded_letters <- letters[numbers]

decoded_message <- paste(decoded_letters, collapse = "")
cat("Decoded message from randomletters_wnumbers.txt:\n", decoded_message, "\n\n")
```

::: {.cell-output .cell-output-stdout}

```
Decoded message from randomletters_wnumbers.txt:
 expertsoftenpossessmoredatathanjudgment 
```


:::

```{.r .cell-code}
# -----------------------------
# 4. Find longest sequence of vowels
# -----------------------------
# Remove spaces and periods
cleaned_str <- gsub("[ .]", "", letters_only_str)

# Find all sequences of vowels
vowel_sequences <- gregexpr("[aeiouAEIOU]+", cleaned_str)
vowel_matches <- regmatches(cleaned_str, vowel_sequences)[[1]]

# Only proceed if we found at least one match
if(length(vowel_matches) > 0){
  longest_vowel_seq <- vowel_matches[which.max(nchar(vowel_matches))]
} else {
  longest_vowel_seq <- NA
}

cat("Longest sequence of vowels in randomletters.txt:\n", longest_vowel_seq, "\n")
```

::: {.cell-output .cell-output-stdout}

```
Longest sequence of vowels in randomletters.txt:
 oaaoooo 
```


:::
:::


The message is then: Experts often possess more data than judgement
