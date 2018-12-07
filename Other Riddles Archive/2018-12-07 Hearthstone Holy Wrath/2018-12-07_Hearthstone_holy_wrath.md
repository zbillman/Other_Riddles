2018-12-07 Hearthstone Probability
================
zpb
December 7, 2018

The Scenario
============

At 9:18 seconds in this youtube video <https://youtu.be/FcabbroD2mE?t=558> the opponent casts a Holy Wrath which sends out 8 bolts that can hit any opposing character. For our hero to win, he needs it to miss his Hellfiend, which has 5 HP. His hero has 24, and his Lakkari Felhound has 6 HP. This is very unlikely, right? Let's run a simulation to find out just how unlikely it is!

I am using the video <https://www.youtube.com/watch?v=pBGMt28xgvk> as a guide to help me create this simulation. If I struggle too much I'll give up lol, but I think this is a decent place to start.

Simulation
==========

There is something beneficial to this simulation, in that we don't need to worry about having to set limits on how high we can let the Lakkari Felhound's damage get because it has 8 health! Similarly with the hero having &gt;8 health. Thank god.

This one is probably just a bit off mathematically because it is including the cases where more than 5 bolts hit the Hellfiend. This is extremely unlikely so I guess I'm just going to ignore these cases.

Using summarize(mtibble, mean(Hellfiend &gt;= 6)) these are 1.97% of cases. Not completely insignificant.

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------------------------------------------------------------ tidyverse 1.2.1 --

    ## √ ggplot2 3.1.0     √ purrr   0.2.5
    ## √ tibble  1.4.2     √ dplyr   0.7.8
    ## √ tidyr   0.8.2     √ stringr 1.3.1
    ## √ readr   1.2.1     √ forcats 0.3.0

    ## -- Conflicts --------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
#Column 1 is the Hellfiend, Column 2 is the Lakkari Felhound, Column 3 is the hero
#Every row of the matrix is a run of the simulation
#Run 1 million repeats of this and then find the # of them that do not have any damage on the hellfiend

repeats <- 1.5e6L

m <- matrix(0L, nrow = repeats, ncol = 3L)

for (trials in 1:repeats){
  for (j in 1:8) {
    target <- sample(3, 1)
    m[trials, target] <- m[trials, target] + 1L
  }
}

#Put that matrix m into a tibble so we can summarize it and also remove all of the outcomes that aren't possible
#i.e. all outcomes where Hellfiend takes more than 5 damage or the Lakkari Felhound takes more than 6

mtibble <- m %>%
  as_tibble(m) %>%
  rename("Hellfiend" = "V1",
         "Lakkari_Felhound" = "V2",
         "Hero" = "V3") %>%
  filter(Hellfiend <= 5 & Lakkari_Felhound <= 6)
```

    ## Warning in if (validate) {: the condition has length > 1 and only the first
    ## element will be used

``` r
summarize(mtibble,
          mean(Hellfiend == 0))
```

    ## # A tibble: 1 x 1
    ##   `mean(Hellfiend == 0)`
    ##                    <dbl>
    ## 1                 0.0382

Result
======

Looks like there was ~3.85% chance of that happening. Quite lucky!
