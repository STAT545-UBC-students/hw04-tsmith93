hw04-tsmith93
================
Thomas Smith
2018-10-09

Overview
--------

-   Assignment introduction
-   Load Packages
-   Task Selection

### Assignment introduction

For this assignment, the focus is to refine our data wranglign skills. Specifically, we are hoping to fortify the bridge between data aggregation and data reshaping.

### Loading packages

If you haven't already done so, download both gapminder and tidyverse using `install.packages()`

Next load gapminder, tidyverse and knitr:

``` r
#suppressPackageStartupMessages stops unecessary messages from popping up
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(knitr))
```

### Task selection

For this assignment, there are two over-arching themes. The first is "Data Reshaping Prompts (and relationship to aggregation)" and the second one is "Join Prompts (join, merge, look up)". For the first theme, I am selecting Task 5: Data manipulation sampler. For the second theme, I am selecting Task 2: Create your own cheatsheet.

Data manipulation sampler
-------------------------

Join prompts cheatsheet - Favourite vocalists example
-----------------------------------------------------

### The data

We will create two small dataframes, `vocalists` and `labels`, to start off

``` r
#assign a name to the dataframes first, followed by the collumn names, and then the contained data
vocalists <- "
Name, Gender, Genre, Label
Celine, female, pop, Columbia
Eddie, male, rock, Republic
Florence, female, rock, Republic,
Whitney, female, pop, RCA
Mariah, female, pop, Columbia
Freddie, male, rock, Columbia
Robert, male, rock, Atlantic
"
#make the datframe into a csv
vocalists <- read_csv(vocalists, skip = 1)
```

    ## Warning in rbind(names(probs), probs_f): number of columns of result is not
    ## a multiple of vector length (arg 2)

    ## Warning: 1 parsing failure.
    ## row # A tibble: 1 x 5 col     row col   expected  actual    file         expected   <int> <chr> <chr>     <chr>     <chr>        actual 1     3 <NA>  4 columns 5 columns literal data file # A tibble: 1 x 5

``` r
#repeat for second dataframe
labels <- "
Label, Year Founded
Columbia, 1887
RCA, 1901
Republic, 1995
Arista, 1974
"

labels <- read_csv(labels, skip = 1)

kable(vocalists)
```

| Name     | Gender | Genre | Label    |
|:---------|:-------|:------|:---------|
| Celine   | female | pop   | Columbia |
| Eddie    | male   | rock  | Republic |
| Florence | female | rock  | Republic |
| Whitney  | female | pop   | RCA      |
| Mariah   | female | pop   | Columbia |
| Freddie  | male   | rock  | Columbia |
| Robert   | male   | rock  | Atlantic |

``` r
kable(labels)
```

| Label    |  Year Founded|
|:---------|-------------:|
| Columbia |          1887|
| RCA      |          1901|
| Republic |          1995|
| Arista   |          1974|

### Mutating joins

These combine variables from the two dataframes

#### Inner joins

> Return all rows from x where there are matching values in y, and all columns from x and y. If there are multiple matches between x and y, all combination of the matches are returned.

##### Vocalists = x, labels = y

``` r
(ijvl <- inner_join(vocalists, labels)) %>% 
#pipe into kable function for a nice clean look!  
 kable()
```

    ## Joining, by = "Label"

| Name     | Gender | Genre | Label    |  Year Founded|
|:---------|:-------|:------|:---------|-------------:|
| Celine   | female | pop   | Columbia |          1887|
| Eddie    | male   | rock  | Republic |          1995|
| Florence | female | rock  | Republic |          1995|
| Whitney  | female | pop   | RCA      |          1901|
| Mariah   | female | pop   | Columbia |          1887|
| Freddie  | male   | rock  | Columbia |          1887|

``` r
#note that joining, by = "Label" is telling you that the dataframes are being joined by the variable "Label"
```

For this, we lose Robert because even though he is in `vocalists`, his label is not in `labels`. We also lose Arista from `labels`, as it is not present in `vocalists`. Though, the join has all variables present in both dataframes.

##### Labels = x, vocalists = y

``` r
(ijlv <- inner_join(labels, vocalists)) %>% 
  kable()
```

    ## Joining, by = "Label"

| Label    |  Year Founded| Name     | Gender | Genre |
|:---------|-------------:|:---------|:-------|:------|
| Columbia |          1887| Celine   | female | pop   |
| Columbia |          1887| Mariah   | female | pop   |
| Columbia |          1887| Freddie  | male   | rock  |
| RCA      |          1901| Whitney  | female | pop   |
| Republic |          1995| Eddie    | male   | rock  |
| Republic |          1995| Florence | female | rock  |

For this, we again lose Robert and Arista. Also this time, the variables in `labels` appear first.

#### Left joins

> Return all rows from x, and all columns from x and y. Rows in x with no match in y will have NA values in the new columns. If there are multiple matches between x and y, all combinations of the matches are returned.

##### Vocalists = x, labels = y

``` r
(ljvl <- left_join(vocalists, labels)) %>% 
  kable()
```

    ## Joining, by = "Label"

| Name     | Gender | Genre | Label    |  Year Founded|
|:---------|:-------|:------|:---------|-------------:|
| Celine   | female | pop   | Columbia |          1887|
| Eddie    | male   | rock  | Republic |          1995|
| Florence | female | rock  | Republic |          1995|
| Whitney  | female | pop   | RCA      |          1901|
| Mariah   | female | pop   | Columbia |          1887|
| Freddie  | male   | rock  | Columbia |          1887|
| Robert   | male   | rock  | Atlantic |            NA|

For this, we lose Arista as it is not present in `vocalists` (the x dataframe), and NA appears in Year Founded for Atlantic records, as this data does not exist.

##### Labels = x, vocalists = y

``` r
(ljlv <- left_join(labels, vocalists)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Label    |  Year Founded| Name     | Gender | Genre |
|:---------|-------------:|:---------|:-------|:------|
| Columbia |          1887| Celine   | female | pop   |
| Columbia |          1887| Mariah   | female | pop   |
| Columbia |          1887| Freddie  | male   | rock  |
| RCA      |          1901| Whitney  | female | pop   |
| Republic |          1995| Eddie    | male   | rock  |
| Republic |          1995| Florence | female | rock  |
| Arista   |          1974| NA       | NA     | NA    |

For this, collumns present in `labels` is presented first, Robert is lost as he is not present in `labels`. As well for Arista, NA appears for "Name", "Gender", and "Genre" as this information does not exist in the dataframe `labels`.

#### Right joins

> Return all rows from y, and all columns from x and y. Rows in y with no match in x will have NA values in the new columns. If there are multiple matches between x and y, all combinations of the matches are returned.

##### Vocalists = x, labels = y

``` r
(rjvl <- right_join(vocalists, labels)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Name     | Gender | Genre | Label    |  Year Founded|
|:---------|:-------|:------|:---------|-------------:|
| Celine   | female | pop   | Columbia |          1887|
| Mariah   | female | pop   | Columbia |          1887|
| Freddie  | male   | rock  | Columbia |          1887|
| Whitney  | female | pop   | RCA      |          1901|
| Eddie    | male   | rock  | Republic |          1995|
| Florence | female | rock  | Republic |          1995|
| NA       | NA     | NA    | Arista   |          1974|

The same data is returned as left\_join(labels, vocalists), though the order of data from the two dataframes is reversed.

##### Labels = x, vocalists = y

``` r
(rjlv <- right_join(labels, vocalists)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Label    |  Year Founded| Name     | Gender | Genre |
|:---------|-------------:|:---------|:-------|:------|
| Columbia |          1887| Celine   | female | pop   |
| Republic |          1995| Eddie    | male   | rock  |
| Republic |          1995| Florence | female | rock  |
| RCA      |          1901| Whitney  | female | pop   |
| Columbia |          1887| Mariah   | female | pop   |
| Columbia |          1887| Freddie  | male   | rock  |
| Atlantic |            NA| Robert   | male   | rock  |

The same data is returned as left\_join(vocalists, labels), though the order of data from the two dataframes is reversed.

#### Full join

> Return all rows and all columns from both x and y. Where there are not matching values, returns NA for the one missing.

``` r
(fjlv <- full_join(vocalists, labels)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Name     | Gender | Genre | Label    |  Year Founded|
|:---------|:-------|:------|:---------|-------------:|
| Celine   | female | pop   | Columbia |          1887|
| Eddie    | male   | rock  | Republic |          1995|
| Florence | female | rock  | Republic |          1995|
| Whitney  | female | pop   | RCA      |          1901|
| Mariah   | female | pop   | Columbia |          1887|
| Freddie  | male   | rock  | Columbia |          1887|
| Robert   | male   | rock  | Atlantic |            NA|
| NA       | NA     | NA    | Arista   |          1974|

All the data from both dataframes is presented, and NA is present where there is no crossover data.

### Filtering joins

These keep cases from the left-hand dataframe

#### Semi joins

> Return all rows from x where there are matching values in y, keeping just columns from x.

> A semi join differs from an inner join because an inner join will return one row of x for each matching row of y, where a semi join will never duplicate rows of x.

##### Vocalists = x, labels = y

``` r
(sjvl <- semi_join(vocalists, labels)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Name     | Gender | Genre | Label    |
|:---------|:-------|:------|:---------|
| Celine   | female | pop   | Columbia |
| Eddie    | male   | rock  | Republic |
| Florence | female | rock  | Republic |
| Whitney  | female | pop   | RCA      |
| Mariah   | female | pop   | Columbia |
| Freddie  | male   | rock  | Columbia |

We lose Robert here, similar to the inner join, but the variable year in `label` is not returned.

##### Labels = x, vocalists = y

``` r
(sjlv <- semi_join(labels, vocalists)) %>% 
  kable()
```

    ## Joining, by = "Label"

| Label    |  Year Founded|
|:---------|-------------:|
| Columbia |          1887|
| RCA      |          1901|
| Republic |          1995|

We lose Arista, as it is not present n `vocalists` and only columns from `labels` are kept.

#### Anti joins

> Return all rows from x where there are not matching values in y, keeping just columns from x.

##### Vocalists = x, labels = y

``` r
(ajvl <- anti_join(vocalists, labels)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Name   | Gender | Genre | Label    |
|:-------|:-------|:------|:---------|
| Robert | male   | rock  | Atlantic |

Only Robert is kept, as Atlanic is not present in `labels`.

##### Labels = x, vocalists = y

``` r
(ajlv <- anti_join(labels, vocalists)) %>% 
 kable()
```

    ## Joining, by = "Label"

| Label  |  Year Founded|
|:-------|-------------:|
| Arista |          1974|

Only Arista is kept, as it is not present in `vocalists`.

### Merge

> Merge two data frames by common columns or row names, or do other versions of database join operations.

``` r
merge(labels, vocalists) %>% 
  #Again use kable to make a clean table
  kable()
```

| Label    |  Year Founded| Name     | Gender | Genre |
|:---------|-------------:|:---------|:-------|:------|
| Columbia |          1887| Celine   | female | pop   |
| Columbia |          1887| Mariah   | female | pop   |
| Columbia |          1887| Freddie  | male   | rock  |
| RCA      |          1901| Whitney  | female | pop   |
| Republic |          1995| Eddie    | male   | rock  |
| Republic |          1995| Florence | female | rock  |

Data from Robert, who is associated with Atlantic, and Arista are lost, as those labels are not common for both dataframes.