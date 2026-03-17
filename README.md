# League Of Legends 15-Minute Match Predictor

**Name(s)**: Don Le

## Introduction
We are analyzing competitive League of Legends esports sourced from Oracle's Elixir data. This dataset provides post-match statistics from official professional leagues spanning from 2014 to 2026. To minimize the noise found in lower-tier professional play, we narrowed our scope strictly to Tier-1 leagues: CBLOL, LCK, LCP, LCS, LEC, and LPL. 

The central question we are investigating is: **"How much does early-game snowballing affect a team's win rate?"** Specifically, we want to know what drives a professional team to victory by the 15-minute mark. Readers should care about this dataset because it reveals whether early-game aggression and objective control mathematically secure victories in the most high-stakes, unforgiving esports environments in the world.

* **Total Rows**: 376,452 originally, filtered down to just the team-level summaries for Tier-1 leagues.
* **Relevant Columns**:
  * `result`: The match outcome (1 for a win, 0 for a loss).
  * `firstdragon`: A boolean/integer indicating if the team secured the first dragon.
  * `golddiffat15`: The team's gold difference compared to the opponent at the 15-minute mark.
  * `killsat15`: The total number of kills the team secured by the 15-minute mark.
  * `league`: The specific Tier-1 league the match took place in.

## Data Cleaning and Exploratory Data Analysis
To prepare the data, we took several cleaning steps directly related to the data-generating process:
1. **Filtered to Team Rows**: The dataset records 12 rows per game (10 players and 2 team summaries). We filtered the `position` column to `'team'` so we only analyzed overall team metrics.
2. **Restricted to Tier-1 Leagues**: We filtered the `league` column to only include `['LCK', 'LEC', 'LCS', 'CBLOL', 'LCP', 'LPL']`.
3. **Type Conversion**: We converted categorical boolean columns like `result`, `firstblood`, and `firstdragon` into integers (`1` and `0`) to easily calculate percentages and build predictive models.
4. **Handling Missingness**: We temporarily dropped NA values to maintain consistency during our initial EDA.

*(Paste your raw Markdown table output from `.to_markdown(index=False)` here!)*

### Univariate Analysis
<iframe src="assets/dist_of_gold_tier_one_histogram.html" width="800" height="500" frameborder="0"></iframe>
This plot shows the distribution of the gold difference at 15 minutes across Tier-1 leagues. The distribution is symmetric and centered at zero, meaning that advantages are evenly distributed between blue and red sides, though massive gold leads (outliers) do occur. Note that restricted APIs for leagues like the LPL result in missing data here.

### Bivariate Analysis
<iframe src="assets/bivariate_plot.html" width="800" height="500" frameborder="0"></iframe>
This bar chart visualizes the relationship between securing the first dragon and the match result. Teams that secure the first dragon demonstrate a consistently higher win rate than those that do not, highlighting the importance of early objective control.

### Interesting Aggregates
*(Paste your grouped pivot table Markdown output here)*
We grouped the data by `league` and calculated the mean for Combined Kills Per Minute (`ckpm`), Kills at 15, First Blood Rate, and First Dragon Rate. This pivot table reveals the varying levels of early-game aggression and intensity across different regions.

## Assessment of Missingness
### NMAR Analysis
We believe the `golddiffat15` column is **NMAR** (Not Missing At Random). The missingness is likely tied intrinsically to the game state itself. Specifically, the Oracle's Elixir API might fail to capture exact gold differences at the 15-minute mark due to specific match interruptions, such as server crashes, extended technical pauses, or remakes. If we were to obtain an additional data column, such as `api_error_flag` or `technical_pause_duration`, we could determine if the missingness is explained by these technical interruptions, thereby making the missingness MAR (Missing At Random).

### Missingness Dependency
<iframe src="assets/missingness_plot.html" width="800" height="500" frameborder="0"></iframe>
We performed a permutation test to determine if the missingness of `golddiffat15` depends on the `league`. 
* **Observed TVD**: *(Insert your observed TVD)*
* **P-Value**: *(Insert your calculated p-value)*
* **Conclusion**: Based on the p-value, we (reject / fail to reject) the null hypothesis, suggesting the missingness of 15-minute gold data (does / does not) depend on the league being played.

## Hypothesis Testing
We tested the following question: *"Is there any difference in kills between professional leagues?"*
* **Null Hypothesis**: The average number of kills at 15 minutes (`killsat15`) is the exact same across all Tier-1 leagues. Any observed differences are due to random chance.
* **Alternative Hypothesis**: The average number of kills at 15 minutes differs across Tier-1 leagues.
* **Test Statistic**: The variance of the mean kills at 15 minutes across the leagues.
* **Significance Level**: 0.05
* **P-Value**: *(Insert your p-value)*
* **Conclusion**: *(Insert your conclusion based on the p-value. Make sure to avoid absolute language like "proves")*.

## Framing a Prediction Problem
Our goal is to predict the **`result`** (whether a team wins or loses a match). This is a **binary classification** problem. 

At the "time of prediction," we are strictly standing at the 15-minute mark of the game. Therefore, our model will only be trained on features and information known exactly at 15 minutes (such as `killsat15`, `firstdragon`, and `golddiffat15`).

We are using **Accuracy** as our evaluation metric. Because every League of Legends match features exactly one winning team and one losing team, our dataset has a naturally perfectly balanced 50/50 split of wins and losses. This makes Accuracy a highly reliable and interpretable metric over F1-score or Precision.

## Baseline Model
Our baseline model is a *(Insert your classifier, e.g., Decision Tree)* that predicts the match result using two features:
1. `killsat15` (Quantitative)
2. `firstdragon` (Nominal - Boolean)

*(Describe your encodings here, e.g., We left the quantitative features as-is and used a OneHotEncoder for the nominal features).* The model achieved an Accuracy of *(Insert accuracy)* on the unseen testing data. We believe this model is *(good/not good)* because *(provide reasoning based on your baseline performance)*.

## Final Model
*(Fill in this section once you have engineered two new features and run your GridSearchCV for hyperparameters).*
* **Added Features**: 
* **Modeling Algorithm**: 
* **Best Hyperparameters**: 
* **Performance Improvement**: 

## Fairness Analysis
*(Fill in this section once you have completed your Step 8 Permutation Test)*
* **Group X**: 
* **Group Y**: 
* **Evaluation Metric**: Accuracy
* **Null Hypothesis**: Our model is fair. Its accuracy for Group X and Group Y are roughly the same, and any differences are due to random chance.
* **Alternative Hypothesis**: Our model is unfair. Its accuracy for Group X is lower than its accuracy for Group Y.
* **Test Statistic**: Difference in Accuracy.
* **P-Value & Conclusion**: