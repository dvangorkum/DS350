---
title: "W8_CaseStudy"
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
# Load packages
library(USAboundaries)
library(tidyverse)
library(sf)
library(geofacet)
library(buildings)

# --- Get boundaries ---
states <- us_states(resolution = "low")
counties_id <- us_counties(states = "Idaho", resolution = "low")

# --- Prepare FIPS columns for joining ---
states <- states %>%
  mutate(state_fips = as.integer(statefp))

counties_id <- counties_id %>%
  mutate(state_fips = as.integer(statefp),
         county_fips = as.integer(countyfp))

# --- Load permits data ---
data("permits", package = "buildings")

# --- Step 1. Filter to single-family permits ---
permits_single <- permits %>%
  filter(variable == "Single Family",
         year >= 1980, year <= 2010)

# --- Step 2. Aggregate to state level ---
state_permits <- permits_single %>%
  group_by(state, StateAbbr, year) %>%
  summarize(single_family = sum(value, na.rm = TRUE), .groups = "drop") %>%
  rename(state_fips = state)

# --- Step 3. County-level for Idaho ---
idaho_permits <- permits_single %>%
  filter(StateAbbr == "ID") %>%
  group_by(state, county, countyname, year) %>%
  summarize(single_family = sum(value, na.rm = TRUE), .groups = "drop") %>%
  rename(state_fips = state, county_fips = county)

# --- Step 4. Join to spatial data ---
states_sf <- states %>%
  left_join(state_permits, by = "state_fips")

idaho_sf <- counties_id %>%
  left_join(idaho_permits, by = c("state_fips", "county_fips"))
```
:::



::: {.cell}

```{.r .cell-code}
ggplot(state_permits, aes(x = year, y = single_family)) +
  geom_line(color = "#2c7fb8") +
  facet_geo(~ StateAbbr, grid = "us_state_grid1") +
  scale_y_log10() +
  labs(
    title = "Single-Family Building Permits (1980–2010)",
    subtitle = "Trends by U.S. State — highlighting 2007–2010 collapse",
    x = "Year", y = "Permits (log scale)"
  ) +
  theme_minimal()
```

::: {.cell-output-display}
![](Week6_Case_Study_files/figure-html/unnamed-chunk-2-1.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
states_sf %>%
  filter(year %in% c(2005, 2010)) %>%
  ggplot(aes(fill = single_family, geometry = geometry)) +
  geom_sf(color = "white", size = 0.2) +
  scale_fill_viridis_c(option = "magma", trans = "log", na.value = "grey90") +
  facet_wrap(~ year) +
  labs(
    title = "Single-Family Building Permits Before and After the Housing Collapse",
    fill = "Permits (log scale)"
  ) +
  theme_void()
```

::: {.cell-output-display}
![](Week6_Case_Study_files/figure-html/unnamed-chunk-3-1.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
ggplot(idaho_sf, aes(fill = single_family, geometry = geometry)) +
  geom_sf(color = "grey70") +
  facet_wrap(~ year) +
  scale_fill_viridis_c(option = "plasma", trans = "log", na.value = "grey90") +
  labs(
    title = "Idaho County-Level Building Permits (1980–2010)",
    subtitle = "Single-family permits over time",
    fill = "Permits (log scale)"
  ) +
  theme_void()
```

::: {.cell-output-display}
![](Week6_Case_Study_files/figure-html/unnamed-chunk-4-1.png){width=672}
:::
:::


## Summary and Insights

This analysis uses building permit data from the `buildings` package to explore spatial and temporal trends in single-family housing construction across the United States from 1980 to 2010. Using the `USAboundaries` package, state and county shapefiles were combined with permit data via FIPS codes to create both national and Idaho-specific visualizations.

Overall We see that permits have decreased from 2005 to 2010 and that for some reason my visuals are small and I am not sure how to make it larger, this is analyzing on the national level. Looking on a Idaho level from 1980 to 2010 we see a decrease in permits, especially in the Boise area and the entire southern Idaho area. However, interestingly we see that some counties fluctuate and don't necessarily have a linear change in permits. Overall by state using the "Single-Family Building Permits (1980-2010) we see a recent dip in building permits across the board.
