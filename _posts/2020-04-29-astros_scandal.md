---
layout: post
title: Astros Cheating Scandal
subtitle: Using Game State to Predict Cheating
---

## Abstract

The Astros sign-stealing scandal has sparked huge debate and discussion in the MLB community. The Astros used HD cameras to steal the opposing catcher's signs and live-streamed them to their dugout, where they would bang on a trash can to signal whether an off-speed pitch or fastball was coming next. This project attempts to predict whether the Astros will cheat using this method based on the state of the game. The hope is that these models will not only be able to predict cheating, but also provide the most impactful game conditions to warrant cheating. In an attempt to minimize data leakage, I removed the game date from the data fed to the models, since the team was much more likely to cheat on specific days for no apparent reason, with the goal is to keep the models fairly general so they are future proofed. Since the problem is a binary classification problem, two models were fitted: Logistic Regression and Random Forest Classifier. Both models used cross-validation with 5-folds. Since the classes are imbalanced, and accuracy is less trustworthy, the score metric I chose to report is the area under the receiver operator characteristic curve, or ROC_AUC. The random forest ended with the highest ROC_AUC score, being .928. This value is higher than I expected it to be when I started the project. I'm concerned it might be related to the class imbalance, if anything.

## Target, Baseline, and Imbalanced Classes

The target for this problem is whether the Astros used a trash can to cheat during a batter's at-bat.
The baseline for a binary classification problem such as this is the most frequent value in the target column.

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
  
*Where 1 is cheating and 0 is not cheating*
</center>

This means our baseline is the value of the most frequent class, always predicting 0, or not cheating.

{% include cheat_by_batter.html %}

This is an interactive graph that shows the number of at-bats where cheating was used for specific batters in the Astros' lineup. Hover over the bars in the graph to see game-specific information.

## Class Imbalance & Choice of Model Score Metric

Since the two classes are so imbalanced, accuracy is not a good choice for model scoring, therefore I chose to use the Area Under the Receiver Operator Characteristic Curve, also known as roc_auc. The ROC is a perfect metric because it tells us how much model is capable of being distinguished between classes.

## Model Choice and Results

Since the issue is binary classification, I chose a Logistic Regression for my linear based model and a Random Forest Classifier for my tree-based model. Cross-validation with 5-folds for both techniques was used to ensure the models are reproduceable. Since most of the features are categorical, I used the "mode" imputer strategy. For the encoding strategy, features were encoded ordinally for the forest and one-hot-encoded for the Logistic Regression. A few of the one-hot features probably had strong linear relationships with the target.

<p align="center">
  <img src="https://raw.githubusercontent.com/mtoce/Build2-Project/master/roc_auc.png">
</p>

As the graph shows, the Random Forest Classifier is a more robust model, beating out both the Logistic Regression and baseline models.

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

## A Closer Look at Precision, Recall with the Confusion Matrix

The confusion matrix is a very useful tool for visualizing the recall and precision of a model. In this case of imbalanced classes, it makes understanding our model much easier.

![Confusion_Matrix](https://raw.githubusercontent.com/mtoce/Build2-Project/master/cmatrix.png)

This confusion matrix shows that the model is 97% accurate at predicting when the Astros cheat when they are actually cheating. However, when they are not cheating the model is 50/50. In a real-world scenario, using this model would result in us incorrectly assuming they are cheating in many cases. This really isn't the worst outcome if we are an opposing team trying to protect our signs and pitcher.

## Final Conclusions

The Random Forest Classifier used in this project is certainly robust and fairly reproduceable for this scandal. However, how reproduceable would the model be given entirely different data? I think the model likely slightly overfit the specific data for the problem, in this case. Also, since the target was only recorded for the batter's entire at-bat instead of each pitch, the model can fall short in terms of when an opponent might cheat **during** an at-bat.

## Additional Background on the Cheating Scandal

The Astros Cheating Scandal is the biggest MLB scandal to date; the largest punishments ever were given out for the unprofessional behavior and actions of the team in the 2017 and 2018 seasons. Just to recap the scandal for anyone unaware, the Astros used HD cameras to steal the signs the catcher gives the pitcher, along with other methods. The camera feed was live-streamed to their dugout, where they would bang a trash can signaling their hitter what type of pitch would come next (fastball or off-speed). A simple advantage like this can go a huge way in baseball, where the best hitters in the game hit **.300** and an average player hits **.250**. Hitting a baseball at the MLB level is extremely difficult, so every advantage the hitter can get goes a long way. Considering the Astros used this underhanded tactic during the 2017 season when they won the World Series Trophy, it was hotly debated whether their trophy should be stripped from them. The commisioner decided not to strip away the trophy, but handed out extreme punishments for the club: 

-   _**Suspended** Manager AJ Hinch and General Manager Jeff Luhnow for one year (Astros owner Jim Crane has since **fired both**)_
-   _**1st & 2nd** round picks for **2020 & 2021** were stripped from team_
-   _Fined **$5 million**, the max amount allowed under MLB Constitution_

While no players were directly suspended, the Astros Club is taking a huge setback by losing it's management and losing these 
draft picks. For more background on the Astros Cheating Scandal, click [here](https://www.si.com/mlb/2020/01/13/houston-astros-cheating-punishment).
