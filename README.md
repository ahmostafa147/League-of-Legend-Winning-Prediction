# Predicting League of Legends Results with different models

by Ahmed Mostafa and Ethan Vo (ahmostafa@ucsd.edu, etvo@ucsd.edu)

If you would like to look at out previous project where we analyzed League of Legends kills on winrate look here: <a href="https://ahmostafa147.github.io/dsc80-project/">League of Legends Kill Analysis</a>.

---

## Introduction

Our dataset is on all of the professional League of Legends games that have taken place in 2023. The dataset contains 12 rows per game, one row per player and 2 rows of summary statistics (one for each team). Furthermore, there are over 100 columns of nearly all the data you could collect on a league of legends match. Our question is “Given support data predict whether the support will win or lose?” Addressing this question would help predict the result of a game given a support player's game statistics and support players determine which aspects of the game they need to focus on in order to win. Although the data does not explicitly collect information on whether a player has more kills than a lane opponent, we can collect the data from columns “kills”, “positions”, and “side”  to compare the kills of a player and their lane opponent and make our own column “has_more_kills.”


The columns we are interested in to answer, “Given support data predict whether the support will win or lose?” are: <code class="language-plaintext highlighter-rouge">“gameid”</code>, <code class="language-plaintext highlighter-rouge">“side”</code>, <code class="language-plaintext highlighter-rouge">“champion”</code>, <code class="language-plaintext highlighter-rouge">“position”</code>, <code class="language-plaintext highlighter-rouge">“result”</code>, <code class="language-plaintext highlighter-rouge">“kills”</code>, <code class="language-plaintext highlighter-rouge">“teamkills”</code>, and <code class="language-plaintext highlighter-rouge">“damageshare”</code>. These are the columns from the dataset that are relevant to our proposed question, as they provide us with information about the position, the side they belong to, whether the position won or lost, and the number of kills done by each position. In the process of cleaning, we removed rows that belong to the team, as they were irrelevant to our analysis, and sorted the data to have each position next to its opponent for easier visual comparison. After that, we decided to add the boolean column, <code class="language-plaintext highlighter-rouge">“has_more_kills”</code>, where True means the position had more kills, and False otherwise. From there we only kept the rows that had a 'position' of 'support'. The column was added to simplify the analysis and comparison process. After cleaning, our dataframe had 21544 rows, representing 21544 supports, and 9 columns.

Descriptions of Columns

* <code class="language-plaintext highlighter-rouge">"gameid"</code> is the identification number of the particular game from that tournament. This is unique for all games in the dataset
* <code class="language-plaintext highlighter-rouge">"side"</code> is the side that player played on. This can either be red or blue (corresponding to the red and blue sides on the map for League of Legends)
* <code class="language-plaintext highlighter-rouge">"champion"</code> is the champion that the player selected for that game.
* <code class="language-plaintext highlighter-rouge">“position”</code> is the position or role the player played. This can be “top”, “mid”, “bot”, “jng”, or “sup” for the five positions in League of Legends
* <code class="language-plaintext highlighter-rouge">“result”</code> is whether or not the team won. It will be 1 if the player’s team won or 0 for a loss
* <code class="language-plaintext highlighter-rouge">"kills"</code> is the number of kills that particular player got in that game
* <code class="language-plaintext highlighter-rouge">“teamkills”</code> is the total number of kills a player's team got
* <code class="language-plaintext highlighter-rouge">“has_more_kills”</code> states whether or not the player has more kills than their lane opponent. It is a boolean that returns either “True” or “False”
* <code class="language-plaintext highlighter-rouge">“damageshare”</code> is the proportion of the team's damage that player dealt


## Problem Identification
Our prediction problem is trying to predict whether a support player won their game of League of Legends, given only their post game statistics (without the result). Note that we do not include the results of the any other player in each game in our analysis, as we want to focus on only the support's statistics. This is a binary classification problem, with the two outcomes being a win or a loss. Our response variable is the “result” column, which is 1 if a team won a game, and 0 otherwise. We chose this because we wanted to further explore the influence of a support's behavior on the result of a game from our previous project. We are evaluating our model through accuracy. A false positive or false negative do not really have much consequences so an F-1 score isn't as important in this context.

## Baseline Model
For our Baseline model we took our cleaned DataFrame and further isolated two features. The features of our model are `"champion"`, meaning the selected champion, and `"damageshare"`, meaning the total kills of the team. We one-hot-encoded `"champion"`. Below you will find the first five rows of the data we used in our Baseline Model where we tested `"champion"` and `"damageshare"` and tried to predict `"result"`.


We chose `"champion"` and `"damageshare"` as our two features because we thought that certain champions are more successful than others in the support role, and that supports with higher damageshares of their team are more likely to lose. This is because some champions win more in a professional setting with their chemistry with other champions used in pro play. In addition, typically when a support does a greater proportion of their teams damage that means that their teammates (the ones who should be doing damage) aren't doing enough damage, and will likely mean that they're behind and losing. We one-hot-encoded `"champion"` by using OneHotEncoder().

## Final Model
### Hyperparameters
The modeling algorithm we chose was a decision tree, as we have a classification problem. The hyperparameters that worked the best for our model were 

**Here are our desired hyper parameters as shown in a DataFrame:**
'forest__criterion': 'gini',
 'forest__max_depth': None,
 'forest__max_features': 'auto',
 'forest__min_samples_leaf': 4,
 'forest__min_samples_split': 25,
 'forest__n_estimators': 50
 
### Features

The features we added were `"has_more_kills"` and `"teamkills"`. We converted `"has_more_kills"` from a boolean to binary, and performed feature engineering on `"teamkills"` as well as `"damageshare"`. `"has_more_kills"` determines if the player had more kills than their opponenet in the same position for that game, and `"teamkills"` is the total number of kills the player's team had for that game.

**Here is what our Dataframe looks like:**

We chose to add `"has_more_kills"` and `"teamkills"` because kills are heavily influential on a games outcome. In our previous project we found that when a support has more kills they are more likely to lose than if they did not have more kills. In addition, the team with more kills will have more resources than the other team. The gold they get from those kills can be used to buy items faster which in turn them win the game.
We standardized `"damageshare"` and `"teamkills"` because these are both quantitative variables that vary from game to game. Standardizing these variables allows us to compare the number of kills to the other games as a whole and can more concretely determine what is considered a amount of teamkills. The same applies to damageshare, as standardizing it will make it easier to compare with the other games as a whole. We standardized these variables by using StandardScaler().

Final Model VS Baseline Model
Our Final model had an accuracy of roughly 80%, which is a roughly a 30% increase from our Baseline model’s accuracy of 50%. This is a substantial improvement to our baseline, giving us about a 4/5 chance to get the correct result. This increase is likely due to access to a wider range of data, allowing for the decision tree to make more accurate decisions based on more variables/information.

## Fairness Analysis
The question we have for our fairness analysis is, “Does this model perform worse for supports on the red side than it does for supports on the blue side?"
Our first group is players on the 'red' side and the second group are the players on the 'blue' side. Our evaluation metric is whether our classifier’s accuracy is the same between the two groups. The null hypothesis is “Our classifier's accuracy is the same for both if a player played on the red side or blue side. Any difference was due to random chance.” The alternative hypothesis is “Our classifier's accuracy is not the same. Its accuracy for players on the red side is greater than the blue side.” The test statistic we chose is the difference in accuracy between the two groups, with a significance level of 0.05. Our resulting P-value was *** insert prop ***, meaning we failed to reject the null hypothesis, and that any difference between the two groups can be explained by random chance. There is no evidence that the classifier's accuracy is different depending on if the player played on the red or blue side.

Below you will find a histogram of our test statistic. The red vertical line is our observed accuracy difference and the rest of the histogram is our simulated differences in accuracy for group X and group Y.


