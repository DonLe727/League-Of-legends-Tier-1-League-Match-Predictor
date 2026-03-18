# League Of Legends Pro-league 15-Minute Match Predictor

## Introduction
In this project, we are analyzing professional competitive League of Legends esports game data that was gathered from [Oracle`s Elixer](https://oracleselixir.com/tools/downloads). This dataset provides post-match game data from official professional leagues spanning through various data collecting API`s from 2014 to 2026.

To target current and relevant game statistics, we extracted match data from the years 2023-2025. It is important to note that we did not want to include 2026 game data because it is still currently ongoing and do not want to have varying exploratory outcomes. Ultimately, we decided to  focus on to Tier-1 leagues which includes: CBLOL, LCK, LCP, LCS, LEC, and LPL.

League of Legends is arguably the most popular game across history. Many of its fanbase take competitive gaming to a whole new level for entertainment, rooting for a team, or even betting. League of Legends is notorious for intense gameplay, high skill ceiling, and unforgiving environments. As a result, the central question we are investigating is: **How much does early-game advantages affect a team`s win rate in top-tier pro play?** In other words, at the 15-minute mark, what drives a professional team to victory?

### DataFrame (Displaying some columns)

| gameid                | league   | side   | position   |   result |   firstblood |   firstdragon |   golddiffat15 |   killsat15 |   opp_killsat15 |   ckpm |
|:----------------------|:---------|:-------|:-----------|---------:|-------------:|--------------:|---------------:|------------:|----------------:|-------:|
| ESPORTSTMNT06_2753012 | LFL2     | Blue   | top        |        1 |            0 |           nan |            322 |           0 |               0 | 0.4594 |
| ESPORTSTMNT06_2753012 | LFL2     | Blue   | jng        |        1 |            0 |           nan |           -357 |           0 |               0 | 0.4594 |
| ESPORTSTMNT06_2753012 | LFL2     | Blue   | mid        |        1 |            0 |           nan |           -479 |           0 |               0 | 0.4594 |
| ESPORTSTMNT06_2753012 | LFL2     | Blue   | bot        |        1 |            0 |           nan |            200 |           0 |               1 | 0.4594 |
| ESPORTSTMNT06_2753012 | LFL2     | Blue   | sup        |        1 |            0 |           nan |           -216 |           0 |               0 | 0.4594 |

* **Total Rows**: 376,452 (188226 games)
* **Columns**: 165
* **Revelant Columns**:
    * `result`: The match outcome (1 for a win, 0 for a loss).
    * `firstblood`: A float indicating if a team secured the first kill. (1.0 for True, 0.0 for False)
    * `firstdragon`: A float indicating if a team secured first dragon. (1.0 for True, 0.0 for False)
    * `golddiffat15`: A team`s gold difference compared to the other team at the 15-minute mark.(float)
    * `killsat15`: The total number of kills a team has by the 15-minute mark. (float)
    * `opp_killsat15`: The total number of kills the opponent has by the 15-minute mark. (float)
    * `league`: The league that the match took place in. (string)
    * `side`: What side a team was playing on. (`Blue`, or `Red`)
    * `position`: 6 different labels: top, jng, mid, bot, sup, team
    * `ckpm`: Combined Kills per Minute. (float; No missing values)



* **Note**: There are many missing data.

## Data Cleaning and Exploratory Data Analysis
To prepare the data for our EDA, we took several cleaning steps.
1.  **Filtered Columns**: We filtered the DataFrame columns to only include: `[`gameid`, `league`, `side`, `position`, `result`, `firstblood`, `firstdragon`, `golddiffat15`, `killsat15`, `opp_killsat15`, `ckpm`]`
    * This allows us to focus on early-game statistics such as `firstblood`, `firstdragon` and etc.
2.  **Restricted to Tier-1 Leagues**: We filtered the `league` column to only include: `[`LCK`, `LEC`, `LCS`, `CBLOL`, `LCP`, `LPL`]`
    * This filters our data to only Tier-1 leagues.
3. **Filtered to team Rows**: The dataset records 12 rows per game (10 roles and 2 team summaries). We filtered the `position` column to ``team`` so we can only anaylze overall team metrics.
4. **Type Conversion**: We converted categorical boolean columns like `result`,`firstblood`,`firstdragon` into integers (1 and 0).
5. **Handling Missingness**: We dropped NA values to maintain consistency during our initial EDA.

| gameid                | league   | side   | position   |   result |   firstblood |   firstdragon |   golddiffat15 |   killsat15 |   opp_killsat15 |   ckpm |
|:----------------------|:---------|:-------|:-----------|---------:|-------------:|--------------:|---------------:|------------:|----------------:|-------:|
| ESPORTSTMNT04_2659018 | LCK      | Blue   | team       |        1 |            0 |             1 |           3176 |           5 |               1 | 0.7965 |
| ESPORTSTMNT04_2659018 | LCK      | Red    | team       |        0 |            1 |             0 |          -3176 |           1 |               5 | 0.7965 |
| ESPORTSTMNT04_2661035 | LCK      | Blue   | team       |        1 |            1 |             0 |           1287 |           3 |               0 | 0.5804 |
| ESPORTSTMNT04_2661035 | LCK      | Red    | team       |        0 |            0 |             1 |          -1287 |           0 |               3 | 0.5804 |
| ESPORTSTMNT04_2660040 | LCK      | Blue   | team       |        1 |            0 |             0 |            905 |           2 |               4 | 0.8326 |

### Aggregation

| league   |   avg_ckpm |   avg_kills_at_15 |   first_blood_rate |   first_dragon |
|:---------|-----------:|------------------:|-------------------:|---------------:|
| LCP      |      0.869 |             3.414 |              0.5   |            0.5 |
| LEC      |      0.849 |             3.757 |              0.5   |            0.5 |
| CBLOL    |      0.831 |             2.72  |              0.5   |            0.5 |
| LCS      |      0.805 |             3.339 |              0.499 |            0.5 |
| LCK      |      0.8   |             3.007 |              0.5   |            0.5 |

To aggregate our data, we grouped our data by `league` and calculated the mean for `ckpm`, `killsat15`, `firstblood`, and `firstdragon`. This pivot table reveals the different levels of early-game statistics and intensity across Tier-1 leagues. Most importantly, our pivot table reveals that amongst the Tier-1 regions, LPL is not there, signifying that LPL does not record 15-minute interval statistics. **Our analysis will therefore not include LPL as a Tier-1 LoL League and will be referencing Tier-1 League as [`LCK`, `LEC`, `LCS`, `CBLOL`, `LCP`] for convenience unless specified otherwise.**

### Univariate Analysis
This plot shows the distribution of gold difference at the 15 minute mark for Tier-1 leagues. The distribution appears to be a normal distribution centered at zero, meaning that advantages are evenly distributed across red and blue side, though there are occasional outliers. (Note that LPL is not included in this anaylsis.)
<iframe src="assets/dist_of_gold_tier_one_histogram.html" width="850" height="550" frameborder="0"></iframe>


### Bivariate Analysis
These two scatter plots visualizes the win/loss result based on gold and kill difference across blue slide and blue side. Teams that have a positive gold and kill differences have a higher chance of winning that those that have a negative gold and kill difference.
<iframe src="assets/kill_lead_vs_gold_lead_scatter.html" width="1020" height="660" frameborder="0"></iframe>

## Assessment of Missingness
We believe 15-minute statistic (e.g. `golddiffat15`) column is **NMAR**. The missingness can be due to game trackers itself. For example, Oracle`s Elixer API might not be able to track gold differences at the 15-minute mark due to technical game structures varied by league.
Therefore, we completed permutation tests to analyze the dependency of missingness. Our threshold of significance will be a p-value of 0.05.

**Test 1: League (MAR)**
- **Null Hypothesis**: The missingness of `golddiffat15` does **not** depend on the `league`.
- **Alternative Hypothesis**: The missingness of `golddiffat15` **does** depend on the `league`.
* **Observed TVD**: 0.9810
* **P-Value**: 0.0000
Because our p-value (~0.0000) is far below our threshold of 0.05, we reject the null hypothesis, signifying that `golddiffat15` is heavily dependent on the league the game was played in. (MAR)
<iframe src="assets/missingness_plot.html" width="820" height="550" frameborder="0"></iframe>

**Test 2: Side (MCAR)**
- **Null Hypothesis**: The missingness of `golddiffat15` does **not** depend on the `side` (Blue/Red).
- **Alternative Hypothesis**: The missingness of `golddiffat15` **does** depend on the `side`.
* **Observed TVD**: 0.0000
* **P-Value**: 1.0000

Because our p-value (1.0000) is greater than our threshold, we fail to reject the null hypothesis, suggesting the missingness of `golddiffat15` does not depend on which side a team is playing on.
<iframe src="assets/missingness_plot2.html" width="820" height="550" frameborder="0"></iframe>

## Hypothesis Testing
We tested the question: "Is there any difference in kills between pro League of Legends leagues?"
* **Null Hypothesis**: The Mean Combined Kills per Minute (CKPM) in Tier 1 Leagues is the same as the mean CKPM of other Leagues.
* **Alternative Hypothesis**: The CKPM of Tier 1 Leagues is ***less*** than the mean CKPM of other leagues. 
* **Test Statistic**: Difference in Means (Tier-1 Mean CKPM - Other Leagues Mean CKPM)
* **Significance Level**: 0.05

<iframe src="assets/permutation_test_histogram.html" width="820" height="550" frameborder="0"></iframe>

**Results:**
* **Tier-1 Mean CKPM**: 0.8393
* **Other Leagues Mean CKPM**: 1.0041

* **Observed Difference**: -0.1648
* **P-Value**: 0.0000

Because our p-value is far below our significance level of 0.05, we reject the null hypothesis, suggesting that Tier-1 leagues matches tend to be more slower paced resulting in less overall kills compared to lower-tier leagues.

## Framing a Prediction Problem
Our goal is to predict the `result` (win/loss) of a tier-1 league game. This is a binary classification problem.

We are primarily focusing on the 15-minute mark + early game statistics of the game to predict the outcome of a game. Our model will be trained on early-game information such as `killsat15`, `firstdragon`, `golddiffat15`, and `firstdragon`.

We are using accuracy as our evaluation metric because we want to be able to predict the outcome of each match consistently. Because our dataset has data on both teams per match, we have a perfectly balance 50/50 ratio of wins and losses. This makes accuracy an effective metric.

## Baseline Model (Logistic Regression)
For our baseline model, we built a logistic regression classifier to predict tier-1 match results. Note that LPL is not included because we are using 15-minute interval data. 

**Features Used:**
* `golddiffat15` (Quantitative): The team`s gold difference compared to the opponent at the 15-minute mark.

**Results:**
<iframe src="assets/baseline_logistic_regression.html" width="940" height="550" frameborder="0"></iframe>
* **Train Accuracy**: 0.7155
* **Test Accuracy**: 0.7224

Our baseline mode achieved an accuracy of 0.7224 on testing data. We believe this is a great starting point since we are above the probability of a coin flip. This accuracy implies that a gold lead at 15 minutes helps a team to snowball to victory. However, this model is far too simplistic and does not consider other in-game factors that can aid a team to win.

**Logistic Regression in practice:**
<iframe src="assets/base_confusion_matrix.html" width="640" height="450" frameborder="0"></iframe>
From this simulation with 1466 games, our prediction model was able to predict 1059 out of 1466 games correctly. (%72.24 accuracy)

## Final Model (Random Forest With 5-fold Cross-Validation)
For our final predictive model, we upgraded from a simple Logistic Regression classifier to a Random Forest Classifier with 5-fold Cross-Validation, allowing us to test multiple early-game statistics: `golddiffat15`, `killsat15`, `firstblood`, `firstdragon`, and `firstherald`.

**Preprocessing:**
Using ColumnTransformer in our PipeLine, we pplied StandardScaler to `golddiffat15` and `killsat15` to prevent large gold values to overshadow smaller metrics. We kept booleans features like `firstblod`, `firstherald` and `firstdragon` as is.

**Tuning:**
To optimize our Random Forest and prevent overfitting to the training data, we utilized GidSearchCV with 5-fold cross-validation with `cv=5`. We tested multiple depths and estimators:
* `max_depth`: [3, 5, 7, 10]
* `n_estimators`: [50, 100, 200]

**Results:**
* **Best Hyperparameters**: `rf__max_depth`= 5; `rf__n_estimators`=100
* **Cross-Validation Accuracy**: 0.7189
* **Final Test Accuracy**: 0.7265

By including various early-game statistics such as first blood and first dragon status on top of economic success, our Random Forest model improved our ability to predict the match outcome by a slight amount. Our accuracy increased by 0.0047. This is likely due to gold data being an accumative measurement of all micro advantages a team has combined, therefore, not increasing our accuracy by a significant amount compared to our Baseline model (which predicted solely on `golddiffat15`).

**Feature Importances:**
Because of our Random Forest Model, we are able to see the importance (influences) of each game statistic:
<iframe src="assets/importance_bar_chart.html" width="1000" height="450" frameborder="0"></iframe>
`golddiffat15` has an importance of ~0.7519 (higher is better). Other early-game data has a lower importance, signifying lower influence to determine a team's victory/loss.

## Fairness Analysis
To evaluate the fairness of our Final Random Forest model, we utilized permutation testing to predict outcomes (win/loss) as accurately for teams that secure the first dragon as it does for teams that do not. We used accuracy parity as our evaluation metric to distinguish the difference in overal prediction accuracy for teams that do claim first dragon and for teams that don't. Our threshold of significance will be p-value > 0.05.

* **Group X**: Teams that secured `firstdragon` (Value = 1)
* **Group Y**: Teams that did not secure `firstdragon` (Value = 0)
* **Evaluation Metric**: Accuracy

* **Null Hypothesis**: Our model is fair. Its accuracy between teams that get first dragon and teams that don't is the same.
* **Alternative Hypothesis**: Our model is unfair. Its accuracy between teams that get first dragon is significantly different from its accuracy for teams that don't.
<iframe src="assets/fairness_permutation_test_histogram.html" width="1000" height="450" frameborder="0"></iframe>
Our observed difference in accuracy between the two groups was around 2.09%. After running the permutation test with 500 repititions, we calculated a p-value of 0.4200. Because our p-value of 0.4200 is greater than our threshold of significance (0.05), we fail to reject the null hypothesis. Therefore, we conclude that our model is fair and that there is no significant difference in predictive accuracy based on whether a team secures first dragon.