Activity 2
================

### A typical modeling process

The process that we will use for today’s activity is:

1.  Identify our research question(s),
2.  Explore (graphically and with numerical summaries) the variables of
    interest - both individually and in relationship to one another,
3.  Fit a simple linear regression model to obtain and describe model
    estimates,
4.  Assess how “good” our model is, and
5.  Predict new values.

We will continue to update/tweak/adapt this process and you are
encouraged to build your own process. Before we begin, we set up our R
session and introduce this activity’s data.

## Day 1

### The setup

We will be using two packages from Posit (formerly
[RStudio](https://posit.co/)): `{tidyverse}` and `{tidymodels}`. If you
would like to try the *ISLR* labs using these two packages instead of
base R, [Emil Hvitfeldt](https://www.emilhvitfeldt.com/) (of Posit) has
put together a [complementary online
text](https://emilhvitfeldt.github.io/ISLR-tidymodels-labs/index.html).

- In the **Packages** pane of RStudio (same area as **Files**), check to
  see if `{tidyverse}` and `{tidymodels}` are installed. Be sure to
  check both your **User Library** and **System Library**.

- If either of these are not currently listed, type the following in
  your **Console** pane, replacing `package_name` with the appropriate
  name, and press Enter/Return afterwards.

  ``` r
  # Note: the "eval = FALSE" in the above line tells R not to evaluate this code
  install.packages("package_name")
  ```

- Once you have verified that both `{tidyverse}` and `{tidymodels}` are
  installed, load these packages in the R chunk below titled `setup`.
  That is, type the following:

  ``` r
  library(tidyverse)
  library(tidymodels)
  ```

- Run the `setup` code chunk and/or **knit**
  <img src="../README-img/knit-icon.png" alt="knit" width = "20"/> icon
  your Rmd document to verify that no errors occur.

``` r
  library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
  library(tidymodels)
```

    ## ── Attaching packages ────────────────────────────────────── tidymodels 1.1.0 ──
    ## ✔ broom        1.0.5     ✔ rsample      1.1.1
    ## ✔ dials        1.2.0     ✔ tune         1.1.1
    ## ✔ infer        1.0.4     ✔ workflows    1.1.3
    ## ✔ modeldata    1.1.0     ✔ workflowsets 1.0.1
    ## ✔ parsnip      1.1.0     ✔ yardstick    1.2.0
    ## ✔ recipes      1.0.6     
    ## ── Conflicts ───────────────────────────────────────── tidymodels_conflicts() ──
    ## ✖ scales::discard() masks purrr::discard()
    ## ✖ dplyr::filter()   masks stats::filter()
    ## ✖ recipes::fixed()  masks stringr::fixed()
    ## ✖ dplyr::lag()      masks stats::lag()
    ## ✖ yardstick::spec() masks readr::spec()
    ## ✖ recipes::step()   masks stats::step()
    ## • Use suppressPackageStartupMessages() to eliminate package startup messages

![check-in](../README-img/noun-magnifying-glass.png) **Check in**

Test your GitHub skills by staging, committing, and pushing your changes
to GitHub and verify that your changes have been added to your GitHub
repository.

### The data

The data we’re working with is from the OpenIntro site:
`https://www.openintro.org/data/csv/hfi.csv`. Here is the “about” page:
<https://www.openintro.org/data/index.php?data=hfi>.

In the R code chunk below titled `load-data`, you will type the code
that reads in the above linked CSV file by doing the following:

- Rather than downloading this file, uploading to RStudio, then reading
  it in, explore how to load this file directly from the provided URL
  with `readr::read_csv` (`{readr}` is part of `{tidyverse}`).
- Assign this data set into a data frame named `hfi` (short for “Human
  Freedom Index”).

``` r
hfi <- readr::read_csv("hfi.csv")
```

    ## Rows: 1458 Columns: 123
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr   (3): ISO_code, countries, region
    ## dbl (120): year, pf_rol_procedural, pf_rol_civil, pf_rol_criminal, pf_rol, p...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

After doing this and viewing the loaded data, answer the following
questions:

1.  What are the dimensions of the dataset? What does each row
    represent?

``` r
# Check dimensions of the dataset
dimensions <- dim(hfi)
print(dimensions)
```

    ## [1] 1458  123

``` r
# Inspect the structure of the dataset
str(hfi)
```

    ## spc_tbl_ [1,458 × 123] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ year                              : num [1:1458] 2016 2016 2016 2016 2016 ...
    ##  $ ISO_code                          : chr [1:1458] "ALB" "DZA" "AGO" "ARG" ...
    ##  $ countries                         : chr [1:1458] "Albania" "Algeria" "Angola" "Argentina" ...
    ##  $ region                            : chr [1:1458] "Eastern Europe" "Middle East & North Africa" "Sub-Saharan Africa" "Latin America & the Caribbean" ...
    ##  $ pf_rol_procedural                 : num [1:1458] 6.66 NA NA 7.1 NA ...
    ##  $ pf_rol_civil                      : num [1:1458] 4.55 NA NA 5.79 NA ...
    ##  $ pf_rol_criminal                   : num [1:1458] 4.67 NA NA 4.34 NA ...
    ##  $ pf_rol                            : num [1:1458] 5.29 3.82 3.45 5.74 5 ...
    ##  $ pf_ss_homicide                    : num [1:1458] 8.92 9.46 8.06 7.62 8.81 ...
    ##  $ pf_ss_disappearances_disap        : num [1:1458] 10 10 5 10 10 10 10 10 10 10 ...
    ##  $ pf_ss_disappearances_violent      : num [1:1458] 10 9.29 10 10 10 ...
    ##  $ pf_ss_disappearances_organized    : num [1:1458] 10 5 7.5 7.5 7.5 10 10 7.5 NA 2.5 ...
    ##  $ pf_ss_disappearances_fatalities   : num [1:1458] 10 9.93 10 10 9.32 ...
    ##  $ pf_ss_disappearances_injuries     : num [1:1458] 10 9.99 10 9.99 9.93 ...
    ##  $ pf_ss_disappearances              : num [1:1458] 10 8.84 8.5 9.5 9.35 ...
    ##  $ pf_ss_women_fgm                   : num [1:1458] 10 10 10 10 10 10 10 10 NA 10 ...
    ##  $ pf_ss_women_missing               : num [1:1458] 7.5 7.5 10 10 5 10 10 7.5 NA 7.5 ...
    ##  $ pf_ss_women_inheritance_widows    : num [1:1458] 5 0 5 10 10 10 10 5 NA 0 ...
    ##  $ pf_ss_women_inheritance_daughters : num [1:1458] 5 0 5 10 10 10 10 10 NA 0 ...
    ##  $ pf_ss_women_inheritance           : num [1:1458] 5 0 5 10 10 10 10 7.5 NA 0 ...
    ##  $ pf_ss_women                       : num [1:1458] 7.5 5.83 8.33 10 8.33 ...
    ##  $ pf_ss                             : num [1:1458] 8.81 8.04 8.3 9.04 8.83 ...
    ##  $ pf_movement_domestic              : num [1:1458] 5 5 0 10 5 10 10 5 10 10 ...
    ##  $ pf_movement_foreign               : num [1:1458] 10 5 5 10 5 10 10 5 10 5 ...
    ##  $ pf_movement_women                 : num [1:1458] 5 5 10 10 10 10 10 5 NA 5 ...
    ##  $ pf_movement                       : num [1:1458] 6.67 5 5 10 6.67 ...
    ##  $ pf_religion_estop_establish       : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_religion_estop_operate         : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_religion_estop                 : num [1:1458] 10 5 10 7.5 5 10 10 2.5 NA 10 ...
    ##  $ pf_religion_harassment            : num [1:1458] 9.57 6.87 8.9 9.04 8.58 ...
    ##  $ pf_religion_restrictions          : num [1:1458] 8.01 2.96 7.46 6.85 5.09 ...
    ##  $ pf_religion                       : num [1:1458] 9.19 4.94 8.79 7.8 6.22 ...
    ##  $ pf_association_association        : num [1:1458] 10 5 2.5 7.5 7.5 10 10 2.5 NA 5 ...
    ##  $ pf_association_assembly           : num [1:1458] 10 5 2.5 10 7.5 10 10 5 NA 0 ...
    ##  $ pf_association_political_establish: num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_political_operate  : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_political          : num [1:1458] 10 5 2.5 5 5 10 10 2.5 NA 0 ...
    ##  $ pf_association_prof_establish     : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_prof_operate       : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_prof               : num [1:1458] 10 5 5 7.5 5 10 10 2.5 NA 10 ...
    ##  $ pf_association_sport_establish    : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_sport_operate      : num [1:1458] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ pf_association_sport              : num [1:1458] 10 5 7.5 7.5 7.5 10 10 2.5 NA 10 ...
    ##  $ pf_association                    : num [1:1458] 10 5 4 7.5 6.5 10 10 3 NA 5 ...
    ##  $ pf_expression_killed              : num [1:1458] 10 10 10 10 10 10 10 10 10 10 ...
    ##  $ pf_expression_jailed              : num [1:1458] 10 10 10 10 10 ...
    ##  $ pf_expression_influence           : num [1:1458] 5 2.67 2.67 5.67 3.33 ...
    ##  $ pf_expression_control             : num [1:1458] 5.25 4 2.5 5.5 4.25 7.75 8 0.25 7.25 0.75 ...
    ##  $ pf_expression_cable               : num [1:1458] 10 10 7.5 10 7.5 10 10 10 NA 7.5 ...
    ##  $ pf_expression_newspapers          : num [1:1458] 10 7.5 5 10 7.5 10 10 0 NA 7.5 ...
    ##  $ pf_expression_internet            : num [1:1458] 10 7.5 7.5 10 7.5 10 10 7.5 NA 2.5 ...
    ##  $ pf_expression                     : num [1:1458] 8.61 7.38 6.45 8.74 7.15 ...
    ##  $ pf_identity_legal                 : num [1:1458] 0 NA 10 10 7 7 10 0 NA NA ...
    ##  $ pf_identity_parental_marriage     : num [1:1458] 10 0 10 10 10 10 10 10 10 0 ...
    ##  $ pf_identity_parental_divorce      : num [1:1458] 10 5 10 10 10 10 10 10 10 0 ...
    ##  $ pf_identity_parental              : num [1:1458] 10 2.5 10 10 10 10 10 10 10 0 ...
    ##  $ pf_identity_sex_male              : num [1:1458] 10 0 0 10 10 10 10 10 10 10 ...
    ##  $ pf_identity_sex_female            : num [1:1458] 10 0 0 10 10 10 10 10 10 10 ...
    ##  $ pf_identity_sex                   : num [1:1458] 10 0 0 10 10 10 10 10 10 10 ...
    ##  $ pf_identity_divorce               : num [1:1458] 5 0 10 10 5 10 10 5 NA 0 ...
    ##  $ pf_identity                       : num [1:1458] 6.25 0.833 7.5 10 8 ...
    ##  $ pf_score                          : num [1:1458] 7.6 5.28 6.11 8.1 6.91 ...
    ##  $ pf_rank                           : num [1:1458] 57 147 117 42 84 11 8 131 64 114 ...
    ##  $ ef_government_consumption         : num [1:1458] 8.23 2.15 7.6 5.34 7.26 ...
    ##  $ ef_government_transfers           : num [1:1458] 7.51 7.82 8.89 6.05 7.75 ...
    ##  $ ef_government_enterprises         : num [1:1458] 8 0 0 6 8 10 10 0 7 10 ...
    ##  $ ef_government_tax_income          : num [1:1458] 9 7 10 7 5 5 4 9 10 10 ...
    ##  $ ef_government_tax_payroll         : num [1:1458] 7 2 9 1 5 5 3 4 10 10 ...
    ##  $ ef_government_tax                 : num [1:1458] 8 4.5 9.5 4 5 5 3.5 6.5 10 10 ...
    ##  $ ef_government                     : num [1:1458] 7.94 3.62 6.5 5.35 7 ...
    ##  $ ef_legal_judicial                 : num [1:1458] 2.67 4.19 1.84 3.69 3.87 ...
    ##  $ ef_legal_courts                   : num [1:1458] 3.15 4.33 1.97 2.93 4.2 ...
    ##  $ ef_legal_protection               : num [1:1458] 4.51 4.69 2.51 4.26 5.66 ...
    ##  $ ef_legal_military                 : num [1:1458] 8.33 4.17 3.33 7.5 5.83 ...
    ##  $ ef_legal_integrity                : num [1:1458] 4.17 5 4.17 3.33 5 ...
    ##  $ ef_legal_enforcement              : num [1:1458] 4.39 4.51 2.3 3.63 5.2 ...
    ##  $ ef_legal_restrictions             : num [1:1458] 6.49 6.63 5.46 6.86 9.8 ...
    ##  $ ef_legal_police                   : num [1:1458] 6.93 6.14 3.02 3.39 5.71 ...
    ##  $ ef_legal_crime                    : num [1:1458] 6.22 6.74 4.29 4.13 7.01 ...
    ##  $ ef_legal_gender                   : num [1:1458] 0.949 0.821 0.846 0.769 1 ...
    ##  $ ef_legal                          : num [1:1458] 5.07 4.69 2.96 3.9 5.81 ...
    ##  $ ef_money_growth                   : num [1:1458] 8.99 6.96 9.39 5.23 9.08 ...
    ##  $ ef_money_sd                       : num [1:1458] 9.48 8.34 4.99 5.22 9.26 ...
    ##  $ ef_money_inflation                : num [1:1458] 9.74 8.72 3.05 2 9.75 ...
    ##  $ ef_money_currency                 : num [1:1458] 10 5 5 10 10 10 10 5 0 10 ...
    ##  $ ef_money                          : num [1:1458] 9.55 7.25 5.61 5.61 9.52 ...
    ##  $ ef_trade_tariffs_revenue          : num [1:1458] 9.63 8.48 8.99 6.06 8.87 ...
    ##  $ ef_trade_tariffs_mean             : num [1:1458] 9.24 6.22 7.72 7.26 8.76 9.5 8.96 8.2 3.36 9.06 ...
    ##  $ ef_trade_tariffs_sd               : num [1:1458] 8.02 5.92 4.25 5.94 8.02 ...
    ##  $ ef_trade_tariffs                  : num [1:1458] 8.96 6.87 6.99 6.42 8.55 ...
    ##  $ ef_trade_regulatory_nontariff     : num [1:1458] 5.57 4.96 3.13 4.47 5.92 ...
    ##  $ ef_trade_regulatory_compliance    : num [1:1458] 9.405 0 0.917 5.156 8.466 ...
    ##  $ ef_trade_regulatory               : num [1:1458] 7.49 2.48 2.02 4.81 7.19 ...
    ##  $ ef_trade_black                    : num [1:1458] 10 5.56 10 0 10 ...
    ##  $ ef_trade_movement_foreign         : num [1:1458] 6.31 3.66 2.95 5.36 5.11 ...
    ##  $ ef_trade_movement_capital         : num [1:1458] 4.615 0 3.077 0.769 5.385 ...
    ##  $ ef_trade_movement_visit           : num [1:1458] 8.297 1.106 0.111 7.965 10 ...
    ##  $ ef_trade_movement                 : num [1:1458] 6.41 1.59 2.04 4.7 6.83 ...
    ##  $ ef_trade                          : num [1:1458] 8.21 4.13 5.26 3.98 8.14 ...
    ##   [list output truncated]
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   year = col_double(),
    ##   ..   ISO_code = col_character(),
    ##   ..   countries = col_character(),
    ##   ..   region = col_character(),
    ##   ..   pf_rol_procedural = col_double(),
    ##   ..   pf_rol_civil = col_double(),
    ##   ..   pf_rol_criminal = col_double(),
    ##   ..   pf_rol = col_double(),
    ##   ..   pf_ss_homicide = col_double(),
    ##   ..   pf_ss_disappearances_disap = col_double(),
    ##   ..   pf_ss_disappearances_violent = col_double(),
    ##   ..   pf_ss_disappearances_organized = col_double(),
    ##   ..   pf_ss_disappearances_fatalities = col_double(),
    ##   ..   pf_ss_disappearances_injuries = col_double(),
    ##   ..   pf_ss_disappearances = col_double(),
    ##   ..   pf_ss_women_fgm = col_double(),
    ##   ..   pf_ss_women_missing = col_double(),
    ##   ..   pf_ss_women_inheritance_widows = col_double(),
    ##   ..   pf_ss_women_inheritance_daughters = col_double(),
    ##   ..   pf_ss_women_inheritance = col_double(),
    ##   ..   pf_ss_women = col_double(),
    ##   ..   pf_ss = col_double(),
    ##   ..   pf_movement_domestic = col_double(),
    ##   ..   pf_movement_foreign = col_double(),
    ##   ..   pf_movement_women = col_double(),
    ##   ..   pf_movement = col_double(),
    ##   ..   pf_religion_estop_establish = col_double(),
    ##   ..   pf_religion_estop_operate = col_double(),
    ##   ..   pf_religion_estop = col_double(),
    ##   ..   pf_religion_harassment = col_double(),
    ##   ..   pf_religion_restrictions = col_double(),
    ##   ..   pf_religion = col_double(),
    ##   ..   pf_association_association = col_double(),
    ##   ..   pf_association_assembly = col_double(),
    ##   ..   pf_association_political_establish = col_double(),
    ##   ..   pf_association_political_operate = col_double(),
    ##   ..   pf_association_political = col_double(),
    ##   ..   pf_association_prof_establish = col_double(),
    ##   ..   pf_association_prof_operate = col_double(),
    ##   ..   pf_association_prof = col_double(),
    ##   ..   pf_association_sport_establish = col_double(),
    ##   ..   pf_association_sport_operate = col_double(),
    ##   ..   pf_association_sport = col_double(),
    ##   ..   pf_association = col_double(),
    ##   ..   pf_expression_killed = col_double(),
    ##   ..   pf_expression_jailed = col_double(),
    ##   ..   pf_expression_influence = col_double(),
    ##   ..   pf_expression_control = col_double(),
    ##   ..   pf_expression_cable = col_double(),
    ##   ..   pf_expression_newspapers = col_double(),
    ##   ..   pf_expression_internet = col_double(),
    ##   ..   pf_expression = col_double(),
    ##   ..   pf_identity_legal = col_double(),
    ##   ..   pf_identity_parental_marriage = col_double(),
    ##   ..   pf_identity_parental_divorce = col_double(),
    ##   ..   pf_identity_parental = col_double(),
    ##   ..   pf_identity_sex_male = col_double(),
    ##   ..   pf_identity_sex_female = col_double(),
    ##   ..   pf_identity_sex = col_double(),
    ##   ..   pf_identity_divorce = col_double(),
    ##   ..   pf_identity = col_double(),
    ##   ..   pf_score = col_double(),
    ##   ..   pf_rank = col_double(),
    ##   ..   ef_government_consumption = col_double(),
    ##   ..   ef_government_transfers = col_double(),
    ##   ..   ef_government_enterprises = col_double(),
    ##   ..   ef_government_tax_income = col_double(),
    ##   ..   ef_government_tax_payroll = col_double(),
    ##   ..   ef_government_tax = col_double(),
    ##   ..   ef_government = col_double(),
    ##   ..   ef_legal_judicial = col_double(),
    ##   ..   ef_legal_courts = col_double(),
    ##   ..   ef_legal_protection = col_double(),
    ##   ..   ef_legal_military = col_double(),
    ##   ..   ef_legal_integrity = col_double(),
    ##   ..   ef_legal_enforcement = col_double(),
    ##   ..   ef_legal_restrictions = col_double(),
    ##   ..   ef_legal_police = col_double(),
    ##   ..   ef_legal_crime = col_double(),
    ##   ..   ef_legal_gender = col_double(),
    ##   ..   ef_legal = col_double(),
    ##   ..   ef_money_growth = col_double(),
    ##   ..   ef_money_sd = col_double(),
    ##   ..   ef_money_inflation = col_double(),
    ##   ..   ef_money_currency = col_double(),
    ##   ..   ef_money = col_double(),
    ##   ..   ef_trade_tariffs_revenue = col_double(),
    ##   ..   ef_trade_tariffs_mean = col_double(),
    ##   ..   ef_trade_tariffs_sd = col_double(),
    ##   ..   ef_trade_tariffs = col_double(),
    ##   ..   ef_trade_regulatory_nontariff = col_double(),
    ##   ..   ef_trade_regulatory_compliance = col_double(),
    ##   ..   ef_trade_regulatory = col_double(),
    ##   ..   ef_trade_black = col_double(),
    ##   ..   ef_trade_movement_foreign = col_double(),
    ##   ..   ef_trade_movement_capital = col_double(),
    ##   ..   ef_trade_movement_visit = col_double(),
    ##   ..   ef_trade_movement = col_double(),
    ##   ..   ef_trade = col_double(),
    ##   ..   ef_regulation_credit_ownership = col_double(),
    ##   ..   ef_regulation_credit_private = col_double(),
    ##   ..   ef_regulation_credit_interest = col_double(),
    ##   ..   ef_regulation_credit = col_double(),
    ##   ..   ef_regulation_labor_minwage = col_double(),
    ##   ..   ef_regulation_labor_firing = col_double(),
    ##   ..   ef_regulation_labor_bargain = col_double(),
    ##   ..   ef_regulation_labor_hours = col_double(),
    ##   ..   ef_regulation_labor_dismissal = col_double(),
    ##   ..   ef_regulation_labor_conscription = col_double(),
    ##   ..   ef_regulation_labor = col_double(),
    ##   ..   ef_regulation_business_adm = col_double(),
    ##   ..   ef_regulation_business_bureaucracy = col_double(),
    ##   ..   ef_regulation_business_start = col_double(),
    ##   ..   ef_regulation_business_bribes = col_double(),
    ##   ..   ef_regulation_business_licensing = col_double(),
    ##   ..   ef_regulation_business_compliance = col_double(),
    ##   ..   ef_regulation_business = col_double(),
    ##   ..   ef_regulation = col_double(),
    ##   ..   ef_score = col_double(),
    ##   ..   ef_rank = col_double(),
    ##   ..   hf_score = col_double(),
    ##   ..   hf_rank = col_double(),
    ##   ..   hf_quartile = col_double()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

``` r
# View the first few rows of the dataset
head(hfi)
```

    ## # A tibble: 6 × 123
    ##    year ISO_code countries region pf_rol_procedural pf_rol_civil pf_rol_criminal
    ##   <dbl> <chr>    <chr>     <chr>              <dbl>        <dbl>           <dbl>
    ## 1  2016 ALB      Albania   Easte…              6.66         4.55            4.67
    ## 2  2016 DZA      Algeria   Middl…             NA           NA              NA   
    ## 3  2016 AGO      Angola    Sub-S…             NA           NA              NA   
    ## 4  2016 ARG      Argentina Latin…              7.10         5.79            4.34
    ## 5  2016 ARM      Armenia   Cauca…             NA           NA              NA   
    ## 6  2016 AUS      Australia Ocean…              8.44         7.53            7.36
    ## # ℹ 116 more variables: pf_rol <dbl>, pf_ss_homicide <dbl>,
    ## #   pf_ss_disappearances_disap <dbl>, pf_ss_disappearances_violent <dbl>,
    ## #   pf_ss_disappearances_organized <dbl>,
    ## #   pf_ss_disappearances_fatalities <dbl>, pf_ss_disappearances_injuries <dbl>,
    ## #   pf_ss_disappearances <dbl>, pf_ss_women_fgm <dbl>,
    ## #   pf_ss_women_missing <dbl>, pf_ss_women_inheritance_widows <dbl>,
    ## #   pf_ss_women_inheritance_daughters <dbl>, pf_ss_women_inheritance <dbl>, …

The dataset spans a lot of years. We are only interested in data from
year 2016. In the R code chunk below titled `hfi-2016`, type the code
that does the following:

- Filter the data `hfi` data frame for year 2016, and
- Assigns the result to a data frame named `hfi_2016`.

``` r
library(dplyr)

# Filter the data for year 2016
hfi_2016 <- hfi %>%
  filter(year == 2016)
```

### 1. Identify our research question(s)

The research question is often defined by you (or your company, boss,
etc.). Today’s research question/goal is to predict a country’s personal
freedom score in 2016.

For this activity we want to explore the relationship between the
personal freedom score, `pf_score`, and the political pressures and
controls on media content index,`pf_expression_control`. Specifically,
we are going to use the political pressures and controls on media
content index to predict a country’s personal freedom score in 2016.

### 2. Explore the variables of interest

Answer the following questions (use your markdown skills) and complete
the following tasks.

2.  What type of plot would you use to display the distribution of the
    personal freedom scores, `pf_score`? Would this be the same type of
    plot to display the distribution of the political pressures and
    controls on media content index, `pf_expression_control`?

- In the R code chunk below titled `univariable-plots`, type the R code
  that displays this plot for `pf_score`.
- In the R code chunk below titled `univariable-plots`, type the R code
  that displays this plot for `pf_expression_control`.

``` r
# univariable-plots

library(ggplot2)

# Plot for pf_score
ggplot(hfi_2016, aes(x = pf_score)) +
  geom_histogram(binwidth = 1, fill = "skyblue", color = "black") +
  labs(title = "Distribution of pf_score in 2016",
       x = "pf_score",
       y = "Frequency")
```

![](activity02_files/figure-gfm/distribution-plots-1.png)<!-- -->

``` r
# Plot for pf_expression_control
ggplot(hfi_2016, aes(x = pf_expression_control)) +
  geom_histogram(binwidth = 1, fill = "skyblue", color = "black") +
  labs(title = "Distribution of pf_expression_control in 2016",
       x = "pf_expression_control",
       y = "Frequency")
```

![](activity02_files/figure-gfm/distribution-plots-2.png)<!-- -->

4.  Comment on each of these two distributions. Be sure to describe
    their centers, spread, shape, and any potential outliers.

5.  What type of plot would you use to display the relationship between
    the personal freedom score, `pf_score`, and the political pressures
    and controls on media content index,`pf_expression_control`?

- In the R code chunk below titled `relationship-plot`, plot this
  relationship using the variable `pf_expression_control` as the
  predictor/explanatory variable.

``` r
library(ggplot2)

# Scatter plot for the relationship between pf_expression_control and pf_score
ggplot(hfi_2016, aes(x = pf_expression_control, y = pf_score)) +
  geom_point(color = "blue") +
  labs(title = "Relationship between pf_expression_control and pf_score",
       x = "pf_expression_control",
       y = "pf_score")
```

![](activity02_files/figure-gfm/relationship-plot-1.png)<!-- -->

4.  Does the relationship look linear? If you knew a country’s
    `pf_expression_control`, or its score out of 10, with 0 being the
    most, of political pressures and controls on media content, would
    you be comfortable using a linear model to predict the personal
    freedom score?

#### Challenge

For each plot and using your `{dplyr}` skills, obtain the appropriate
numerical summary statistics and provide more detailed descriptions of
these plots. For example, in (4) you were asked to comment on the
center, spread, shape, and potential outliers. What measures
could/should be used to describe these? You might not know of one for
each of those terms.

What numerical summary would you use to describe the relationship
between two numerical variables? (hint: explore the `cor` function from
Base R)

``` r
# Compute the correlation coefficient between pf_expression_control and pf_score
correlation <- cor(hfi_2016$pf_expression_control, hfi_2016$pf_score)

# Print the correlation coefficient
print(correlation)
```

    ## [1] 0.8450646

### 3. Fit a simple linear regression model

Regardless of your response to (4), we will continue fitting a simple
linear regression (SLR) model to these data. The code that we will be
using to fit statistical models in this course use `{tidymodels}` - an
opinionated way to fit models in R - and this is likely new to most of
you. I will provide you with example code when I do not think you should
know what to do - i.e., anything `{tidymodels}` related.

To begin, we will create a `{parsnip}` specification for a linear model.

- In the code chunk below titled `parsnip-spec`, replace “verbatim” with
  “r” just before the code chunk title.

``` r
lm_spec <- linear_reg() %>%
  set_mode("regression") %>%
  set_engine("lm")

lm_spec
```

    ## Linear Regression Model Specification (regression)
    ## 
    ## Computational engine: lm

Note that the `set_mode("regression")` is really unnecessary/redundant
as linear models (`"lm"`) can only be regression models. It is better to
be explicit as we get comfortable with this new process. Remember that
you can type `?function_name` in the R **Console** to explore a
function’s help documentation.

The above code also outputs the `lm_spec` output. This code does not do
any calculations by itself, but rather specifies what we plan to do.

Using this specification, we can now fit our model:
$\texttt{pf\score} = \beta_0 + \beta_1 \times \texttt{pf\_expression\_control} + \varepsilon$.
Note, the “\$” portion in the previous sentence is LaTeX snytex which is
a math scripting (and other scripting) language. I do not expect you to
know this, but you will become more comfortable with this. Look at your
knitted document to see how this syntax appears.

- In the code chunk below titled `fit-lm`, replace “verbatim” with “r”
  just before the code chunk title.

``` r
slr_mod <- lm_spec %>% 
  fit(pf_score ~ pf_expression_control, data = hfi_2016)

tidy(slr_mod)
```

    ## # A tibble: 2 × 5
    ##   term                  estimate std.error statistic  p.value
    ##   <chr>                    <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)              4.28     0.149       28.8 4.23e-65
    ## 2 pf_expression_control    0.542    0.0271      20.0 2.31e-45

The above code fits our SLR model, then provides a `tidy` parameter
estimates table.

5.  Using the `tidy` output, update the below formula with the estimated
    parameters. That is, replace “intercept” and “slope” with the
    appropriate values

$\hat{\texttt{pf\score}} = intercept + slope \times \texttt{pf\_expression\_control}$

6.  Interpret each of the estimated parameters from (5) in the context
    of this research question. That is, what do these values represent?

## Day 2

Hopefully, you were able to interpret the SLR model parameter estimates
(i.e., the *y*-intercept and slope) as follows:

> For countries with a `pf_expression_control` of 0 (those with the
> largest amount of political pressure on media content), we expect
> their mean personal freedom score to be 4.28.

> For every 1 unit increase in `pf_expression_control` (political
> pressure on media content index), we expect a country’s mean personal
> freedom score to increase 0.542 units.

### 4. Assessing

#### 4.A: Assess with your Day 1 model

To assess our model fit, we can use $R^2$ (the coefficient of
determination), the proportion of variability in the response variable
that is explained by the explanatory variable. We use `glance` from
`{broom}` (which is automatically loaded with `{tidymodels}` - `{broom}`
is also where `tidy` is from) to access this information.

- In the code chunk below titled `glance-lm`, replace “verbatim” with
  “r” just before the code chunk title.

``` default
glance(slr_mod)
```

After doing this and running the code, answer the following questions:

7.  What is the value of $R^2$ for this model?

8.  What does this value mean in the context of this model? Think about
    what would a “good” value of $R^2$ would be? Can/should this value
    be “perfect”?

#### 4.B: Assess with test/train

You previously fit a model and evaluated it using the exact same data.
This is a bit of circular reasoning and does not provide much
information about the model’s performance. Now we will work through the
test/train process of fitting and assessing a simple linear regression
model.

Using the `diamonds` example provided to you in the Day 2 `README`, do
the following

- Create a new R code chunk and provide it with a descriptive tile
  (e.g., `train-test`).
- Set a seed.
- Create an initial 80-20 split of the `hfi_2016` dataset
- Using your initial split R object, assign the two splits into a
  training R object and a testing R object.

Now, you will use your training dataset to fit a SLR model.

- In the code chunk below titled `train-fit-lm`, replace “verbatim” with
  “r” just before the code chunk title and update the data set to your
  training R object you just created.

``` default
slr_train <- lm_spec %>% 
  fit(pf_score ~ pf_expression_control, data = hfi_2016)

tidy(slr_train)
```

Notice that you can reuse the `lm_spec` specification because we are
still doing a linear model.

9.  Using the `tidy` output, update the below formula with the estimated
    parameters. That is, replace “intercept” and “slope” with the
    appropriate values

$\hat{\texttt{pf\score}} = intercept + slope \times \texttt{pf\_expression\_control}$

10. Interpret each of the estimated parameters from (10) in the context
    of this research question. That is, what do these values represent?

Now we will assess using the testing data set.

- In the code chunk below titled `train-fit-lm`, replace “verbatim” with
  “r” just before the code chunk title and update `data_test` to
  whatever R object you assigned your testing data to above.

``` default
test_aug <- augment(slr_train, new_data = data_test)
test_aug
```

This takes your SLR model and applies it to your testing data.

![check-in](../README-img/noun-magnifying-glass.png) **Check in**

Look at the various information produced by this code. Can you identify
what each column represents?

The `.fitted` column in this output can also be obtained by using
`predict` (i.e., `predict(slr_fit, new_data = data_test)`)

11. Now, using your responses to (7) and (8) as an example, assess how
    well your model fits your testing data. Compare your results here to
    your results from your Day 1 of this activity. Did this model
    perform any differently?

### Model diagnostics

To assess whether the linear model is reliable, we should check for (1)
linearity, (2) nearly normal residuals, and (3) constant variability.
Note that the normal residuals is not really necessary for all models
(sometimes we simply want to describe a relationship for the data that
we have or population-level data, where statistical inference is not
appropriate/necessary).

In order to do these checks we need access to the fitted (predicted)
values and the residuals. We can use `broom::augment` to calculate
these.

- In the code chunk below titled `augment`, replace “verbatim” with “r”
  just before the code chunk title and update `data_test` to whatever R
  object you assigned your testing data to above.

``` default
train_aug <- augment(slr_train)

train_aug
```

**Linearity**: You already checked if the relationship between
`pf_score` and `pf_expression_control` is linear using a scatterplot. We
should also verify this condition with a plot of the residuals
vs. fitted (predicted) values.

- In the code chunk below titled `fitted-residual`, replace “verbatim”
  with “r” just before the code chunk title.

``` default
ggplot(data = train_aug, aes(x = .fitted, y = .resid)) +
  geom_point() +
  geom_hline(yintercept = 0, linetype = "dashed", color = "red") +
  xlab("Fitted values") +
  ylab("Residuals")
```

Notice here that `train_aug` can also serve as a data set because stored
within it are the fitted values ($\hat{y}$) and the residuals. Also note
that we are getting fancy with the code here. After creating the
scatterplot on the first layer (first line of code), we overlay a red
horizontal dashed line at $y = 0$ (to help us check whether the
residuals are distributed around 0), and we also rename the axis labels
to be more informative.

Answer the following question:

11. Is there any apparent pattern in the residuals plot? What does this
    indicate about the linearity of the relationship between the two
    variables?

**Nearly normal residuals**: To check this condition, we can look at a
histogram of the residuals.

- In the code chunk below titled `residual-histogram`, replace
  “verbatim” with “r” just before the code chunk title.

``` default
ggplot(data = train_aug, aes(x = .resid)) +
  geom_histogram(binwidth = 0.25) +
  xlab("Residuals")
```

Answer the following question:

12. Based on the histogram, does the nearly normal residuals condition
    appear to be violated? Why or why not?

**Constant variability**:

13. Based on the residuals vs. fitted plot, does the constant
    variability condition appear to be violated? Why or why not?

## Attribution

This document is based on labs from
[OpenIntro](https://www.openintro.org/).

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png"
style="border-width:0" alt="Creative Commons License" /></a><br />This
work is licensed under a
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative
Commons Attribution-ShareAlike 4.0 International License</a>.
