---
layout: post
title: Astros Cheating Scandal
subtitle: Using Game State to Predict Cheating
---

## Abstract

The Astros sign-stealing scandal has sparked huge debate and discussion in the MLB community. The Astros used HD cameras to steal the opposing catcher's signs and live-streamed them to their dugout, where they would bang on a trash can to signal whether an off-speed pitch or fastball was coming next. This project attempts to predict whether the Astros will cheat using this method based on the state of the game. The hope is that these models will not only be able to predict cheating, but also provide the most impactful game conditions to warrant cheating. In an attempt to minimize data leakage, I removed the game date from the data fed to the models, since the team was much more likely to cheat on specific days for no apparent reason, with the goal is to keep the models fairly general so they are future proofed. Since the problem is a binary classification problem, two models were fitted: Logistic Regression and Random Forest Classifier. Both models used cross-validation with 5-folds. Since the classes are imbalanced, and accuracy is less trustworthy, the score metric I chose to report is the area under the receiver operator characteristic curve, or ROC_AUC. The random forest ended with the highest ROC_AUC score, being .928. This value is higher than I expected it to be when I started the project. I'm concerned it might be related to the class imbalance, if anything.

## Target, Baseline, and Imbalanced Classes

The target for this problem is whether the Astros cheated (1) or not (0). The baseline for a binary classification problem such as this is the most frequent value in the target column.

~~~
df['cheats'].value_counts(normalize=True)
~~~


<center>
  
<i> with output </i>
</center>


~~~
0    0.855746
1    0.144254
Name: cheats, dtype: float64
~~~


<center>
  
<i> where 1 is cheating and 0 is not cheating </i>
</center>

This means our baseline is the value of the most frequent class, always predicting 0, or not cheating.

## Data Visualizations

{% include cheat_by_batter.html %}

This is an interactive graph that shows where cheating was used for batters in the Astros' lineup. Hover over the bars in the graph to see game-state information. Below is a code snippet showing the main syntax for creating this interactive graph using Altair.

~~~
chart = alt.Chart(batter_cheats,
    title = "Astros Sign Stealing by Batter (2017 Season)", 
    width = 600, height = 400).mark_bar().encode(
        alt.X('game_date', title = 'Game Date', 
            axis = alt.Axis(titleFont = "Helvetica Neue")),
        alt.Y('cheats', title = 'Cheating During At-Bat', 
            axis = alt.Axis(titleFont = "Helvetica Neue")),
        alt.Color('batter', title = 'Batter', scale = alt.Scale(
                domain = batter_list,
                range = color_list)),
        tooltip = ['batter', 'opponent', 'pitch_category', 'pitch_type_code', 
        'runners_on_base', 'at_bat_event']).interactive()
~~~

## Model Choice and Results

Since the issue is binary classification, I chose a Logistic Regression for my linear based model and a Random Forest Classifier for my tree-based model. Cross-validation with 5-folds for both techniques was used to ensure the models are reproduceable. For the encoding strategy, features were encoded ordinally for the forest and one-hot-encoded for the Logistic Regression. A few of the one-hot features probably had strong linear relationships with the target.

<p align="center">
  <img src="https://raw.githubusercontent.com/mtoce/Build2-Project/master/roc_auc.png">
</p>

As the graph shows, the Random Forest Classifier is a more robust model in terms of roc_auc score, beating out both the Logistic Regression and baseline models.

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#ccc;border-spacing:0;}
.tg td{background-color:#fff;border-bottom-width:1px;border-color:#ccc;border-style:solid;border-top-width:1px;
  border-width:0px;color:#333;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;
  word-break:normal;}
.tg th{background-color:#f0f0f0;border-bottom-width:1px;border-color:#ccc;border-style:solid;border-top-width:1px;
  border-width:0px;color:#333;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;
  padding:10px 5px;word-break:normal;}
.tg .tg-c3ow{border-color:inherit;text-align:center;vertical-align:top;font-weight:bold}
.tg .tg-7btt{border-color:inherit;font-weight:bold;text-align:center;vertical-align:top}
</style>
<center>
<table class="tg">
  <tr>
    <th class="tg-7btt" colspan="3">ROC-AUC Score</th>
  </tr>
  <tr>
    <td class="tg-c3ow">&nbsp;Random Forest&nbsp;</td>
    <td class="tg-c3ow">&nbsp;Logistic Regression&nbsp;</td>
    <td class="tg-c3ow">&nbsp;Baseline&nbsp;</td>
  </tr>
  <tr>
    <td class="tg-c3ow">&nbsp;0.929&nbsp;</td>
    <td class="tg-c3ow">&nbsp;0.675&nbsp;</td>
    <td class="tg-c3ow">&nbsp;0.500&nbsp;</td>
  </tr>
</table>
</center>

## Precision, Recall and the Confusion Matrix

The confusion matrix is a very useful tool for visualizing the recall and precision of a model. In this case of imbalanced classes, it makes understanding our model much easier.

<p align="center">
  <img src="https://raw.githubusercontent.com/mtoce/Build2-Project/master/cmatrix.png">
</p>

This confusion matrix shows that the model is 97% correct when predicting the Astros cheat when they are actually cheating. However, when they are not cheating the model is only 67% correct. In a real-world scenario, using this model would result in us incorrectly assuming they are cheating in many cases. This is not the worst outcome if we are an opposing team trying to protect our signs and pitcher, or even if we are an umpire trying to protect game integrity.


By using the non-normalized confusion matrix, we can calculate the model's precision and recall scores. These two metrics are another option for score metrics for imbalanced classes.

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#ccc;border-spacing:0;}
.tg td{background-color:#fff;border-bottom-width:1px;border-color:#ccc;border-style:solid;border-top-width:1px;
  border-width:0px;color:#333;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;
  word-break:normal;}
.tg th{background-color:#f0f0f0;border-bottom-width:1px;border-color:#ccc;border-style:solid;border-top-width:1px;
  border-width:0px;color:#333;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;
  padding:10px 5px;word-break:normal;}
.tg .tg-c3ow{border-color:inherit;text-align:center;vertical-align:top}
.tg .tg-7btt{border-color:inherit;font-weight:bold;text-align:center;vertical-align:top}
</style>
<center>
<table class="tg">
  <tr>
    <th class="tg-7btt" colspan="2">Random Forest Model</th>
  </tr>
  <tr>
    <td class="tg-c3ow">&nbsp;Precision&nbsp;</td>
    <td class="tg-c3ow">&nbsp;Recall&nbsp;</td>
  </tr>
  <tr>
    <td class="tg-c3ow">&nbsp;0.953&nbsp;</td>
    <td class="tg-c3ow">&nbsp;0.956&nbsp;</td>
  </tr>
</table>
</center>

## Model Permutation Importances

<p align="center">
  <img src="https://raw.githubusercontent.com/mtoce/Build2-Project/master/permutation_importances.png">
</p>

The permutation importances for the random forest classifier are shown above. There were features which had no affect on the model, likely because they were not related to the game-state during the at-bat, such as at_bat_event. On_3b, a feature which describes if there is a runner on third, actually confused the model, contributing a negative permutation importance, which is quite interesting. 


These permutation importances are useful because they help paint a picture of the game-state when cheating occurs. Thus, they are useful for preventing these underhanded tactics in the future by making umpires more aware of the situations in which cheating is likely to occur.


## Additional Background on the Cheating Scandal

The Astros Cheating Scandal is the biggest MLB scandal to date; the largest punishments ever were given out for the unprofessional behavior and actions of the team in the 2017 and 2018 seasons. Just to recap the scandal for anyone unaware, the Astros used HD cameras to steal the signs the catcher gives the pitcher, along with other methods. The camera feed was live-streamed to their dugout, where they would bang a trash can signaling their hitter what type of pitch would come next (fastball or off-speed). A simple advantage like this can go a huge way in baseball, where the best hitters in the game hit **.300** and an average player hits **.250**. Hitting a baseball at the MLB level is extremely difficult, so every advantage the hitter can get goes a long way. Considering the Astros used this underhanded tactic during the 2017 season when they won the World Series Trophy, it was hotly debated whether their trophy should be stripped from them. The commisioner decided not to strip away the trophy, but handed out extreme punishments for the club: 

> **Suspended** Manager AJ Hinch and General Manager Jeff Luhnow for one year (Astros owner Jim Crane has since **fired both**)

> **1st & 2nd** round picks for **2020 & 2021** were stripped from team

> Fined **$5 million**, the max amount allowed under MLB Constitution

While no players were directly suspended, the Astros Club is taking a huge setback by losing it's management and losing these 
draft picks. For more background on the Astros Cheating Scandal, click [here](https://www.si.com/mlb/2020/01/13/houston-astros-cheating-punishment).
