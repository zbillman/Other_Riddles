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

There is something deceiving in this simulation. I was naive and though that I don't need to worry about having to set limits on how high we can let the Lakkari Felhound's damage get, but I was wrong because it has 6 health. Also Similarly with the hero having &gt;8 health. Doggone it. I had to write some if() statements that helped clear this up!

Using summarize(mtibble, mean(Hellfiend &gt;= 6)) these are 1.97% of cases if we don't remove them. That's quite a lot in my book.

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------------------------------------------------------------ tidyverse 1.2.1 --

    ## v ggplot2 3.1.0     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.8
    ## v tidyr   0.8.2     v stringr 1.3.1
    ## v readr   1.2.1     v forcats 0.3.0

    ## -- Conflicts --------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
#Column 1 is the Hellfiend, Column 2 is the Lakkari Felhound, Column 3 is the hero
#Every row of the matrix is a run of the simulation
#Run 1.5 million repeats of this and then find the % of them that do not have any damage on the hellfiend
#Avoid doing >6 damage to the Lakkari Felhound and >5 to the Hellfiend

trial <- 1.5e6L

m <- matrix(0L, nrow = trial, ncol = 3L)

for (i in 1:trial){
    for (attack in 1:8) {
        target <- sample(1:3, 1)
        m[i, target] <- m[i, target] + 1 
        if(m[i, 2] > 6){ 
            m[i, 2] <- m[i, 2] - 1 
            newtarget1 <- sample(c(1,3), 1)
            m[i, newtarget1] <- m[i, newtarget1] + 1 
            }
        if(m[i, 1] > 5){
            m[i, 1] <- m[i, 1] - 1
            newtarget2 <- sample(c(2,3), 1)
            m[i, newtarget2] <- m[i, newtarget2] + 1
            }
        }   
    }   

#Put that matrix m into a tibble so we can summarize it and also remove all of the outcomes that aren't possible
#i.e. all outcomes where Hellfiend takes more than 5 damage or the Lakkari Felhound takes more than 6

mtibble <- m %>%
  as_tibble(m) %>%
  rename("Hellfiend" = "V1",
         "Lakkari_Felhound" = "V2",
         "Hero" = "V3")
```

    ## Warning in if (validate) {: the condition has length > 1 and only the first
    ## element will be used

``` r
summarize(mtibble,
          pct_win = mean(Hellfiend == 0),
          max_LF = max(Lakkari_Felhound),
          max_HF = max(Hellfiend),
          max_hero = max(Hero))
```

    ## # A tibble: 1 x 4
    ##   pct_win max_LF max_HF max_hero
    ##     <dbl>  <dbl>  <dbl>    <dbl>
    ## 1  0.0382      6      5        8

Result
======

Looks like there was ~3.8% chance of that happening. Quite lucky!
