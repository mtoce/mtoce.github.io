---
layout: post
title: Astros Cheating Scandal
subtitle: Predicting Trash-Can Bangs
---

#### Astros Cheating Scandal - Predicting Trash-Can Bangs

## Introduction to the Cheating Scandal

The Astros Cheating Scandal is the biggest MLB scandal to date; the largest punishments ever were given out for the 
unprofessional behavior and actions of the team in the 2017 and 2018 seasons. Just to recap the scandal for anyone unaware, 
the Astros used HD cameras to steal the signs the catcher gives the pitcher, along with other methods. The camera feed was 
livestreamed to their dugout, where they would bang a trash can signaling their hitter what type of pitch would come next 
(fastball or off-speed). A simple advantage like this can go a huge way in baseball, where the best hitters in the game 
hit .300 and an average player hits .250. Hitting a baseball at the MLB level is extremely difficult, so every advantage 
the hitter can get goes a long way. Considering the Astros used this underhanded method during the 2017 season when they won 
the World Series Trophy, it was hotly debated whether their trophy should be stripped from them. The commisioner decided not 
to strip away the trophy, but handed out extreme punishments for the club: 

-   Suspended Manager AJ Hinch and General Manager Jeff Luhnow for one year (Astros owner Jim Crane has since fired both)
-   1st and 2nd round picks for 2020 and 2021 were stripped from team entirely
-   Fined $ 5 million, the max amount allowed under MLB Constitution

While no players were directly suspended, the Astros Club is taking a huge setback by losing it's management and losing these 
draft picks. For more background on the Astros Cheating Scandal, click [here](https://www.si.com/mlb/2020/01/13/houston-astros-cheating-punishment).

## Target and Baseline

The target for this problem is whether the Astros used a trash can to cheat during a batter's at-bat.
The baseline for a binary classification problem such as this is the most frequent value in the target column.

~~~
# Define target as whether bangs were heard during batter's AB
# Bangs were used by the Astros after using technology to steal signs to signal to the batter what pitch was coming
y = 'has_bangs'
data[y].value_counts(normalize=True)
~~~

## Cheating by Batter

{% include cheat_by_batter.html %}

This is an interactive graph that shows the number of at-bats where cheating was used for specific batters in the Astros' lineup. Hover over the bars in the graph to see game-specific information.
