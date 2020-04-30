---
layout: post
title: Astros Cheating Scandal
subtitle: Using Game State to Predict Cheating
---

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
df['cheats'].value_counts(normalize=True)
~~~
with output
~~~
0    0.855746
1    0.144254
Name: cheats, dtype: float64
~~~

where 0 is not-cheating during the at-bat and 1 is cheating. This means our baseline is always guessing there is not cheating.

{% include cheat_by_batter.html %}

This is an interactive graph that shows the number of at-bats where cheating was used for specific batters in the Astros' lineup. Hover over the bars in the graph to see game-specific information.

## Class Imbalance and Choice of Model Score Metric

Since the two classes are so imbalanced, accuracy is not a good choice for model scoring, therefore I chose to use the Area Under the Receiver Operator Characteristic Curve, also known as roc_auc. The ROC is a perfect metric because it tells us how much model is capable of being distinguished between classes.

## Model Choice and Results

Since the issue is binary classification, I chose a Logistic Regression for my linear based model and a Random Forest Classifier for my tree-based model. Cross-validation with 5-folds for both techniques was used to ensure the models are reproduceable. Since most of the features are categorical, I used the "mode" imputer strategy. For the encoding strategy, features were encoded ordinally for the forest and one-hot-encoded for the Logistic Regression. A few of the one-hot features probably had strong linear relationships with the target as opposed to the forest.

![ROC_AUC](https://raw.githubusercontent.com/mtoce/Build2-Project/master/roc_auc.png)

As the graph clearly shows, the Random Forest Classifier was much more robust model, beating out both our baseline and Logistic Regression.

  


| Random Forest | Logistic Regression  | Baseline |
|      :-:      |          :-:         |    :-:   |
|     0.903     |         0.689        |  0.500   |

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#93a1a1;border-spacing:0;}
.tg td{background-color:#fdf6e3;border-color:#93a1a1;border-style:solid;border-width:1px;color:#002b36;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#657b83;border-color:#93a1a1;border-style:solid;border-width:1px;color:#fdf6e3;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-1wig{font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-ezbu{background-color:#eee8d5;border-color:inherit;text-align:center;vertical-align:top}
.tg .tg-c3ow{border-color:inherit;text-align:center;vertical-align:top}
</style>
<table class="tg">
  <tr>
    <th class="tg-1wig" colspan="3">ROC-AUC Score for Models and Baseline</th>
  </tr>
  <tr>
    <td class="tg-ezbu">Random Forest Classifier</td>
    <td class="tg-ezbu">Logistic Regression</td>
    <td class="tg-ezbu">Baseline</td>
  </tr>
  <tr>
    <td class="tg-c3ow">0.903</td>
    <td class="tg-c3ow">0.689</td>
    <td class="tg-c3ow">0.5</td>
  </tr>
</table>

## A Closer Look at Precision, Recall with the Confusion Matrix

![Confusion_Matrix](https://raw.githubusercontent.com/mtoce/Build2-Project/master/cmatrix.png)

This confusion matrix shows that the model is 97% accurate at predicting when the Astros cheat when they are actually cheating.  
However, when they are not cheating the model is 