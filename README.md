# Timeless recipes: The Relationship Between Time and a Recipe's Rating

## Introduction

There are many qualities that a good recipe may have, but the question I plan to explore is, **Does the amount of time it takes to make a recipe correlate with how good it is?** Another interesting way we can connect time to a recipe is to ask if the date the recipe was made is a good predictor of its quality. Perhaps we have gotten better at making food so more recent recipes will be better.

This question is important because it provides insight into what makes a recipe good and lets us predict the quality without needing to know other people rated it. Also, it would be interesting if there was a certain range of years that had worse/better food than others. We may need to reconsider our cooking abilities or keep doing what is working! In any case, we are working with 2 datasets from [Food.com](https://www.food.com/?ref=nav). 

The first dataset, `recipes`, contains 83782 rows of data on the recipes from the website. Each row corresponds to one recipe.

| Column | Description | Type |
| ``name`` | Name of the recipe | string |
| ``id`` | Unique recipe ID | int |
| ``minutes`` | The minutes it takes to prepare | int |
| ``contributor_id`` | ID of the user who submitted the recipe | int |
| ``submitted`` | The date the recipe was submitted | string |
| ``tags`` | Any tags the user added to the recipe | string |
| ``nutrition`` | A list of nutritional information following the form:[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)] where PDV means percent of daily value | string |
| ``n_steps`` | The number of steps in the recipe | int |
| ``steps`` | The actual steps to follow for the recipe | string |
| ``description`` | Description of the recipe given by the user | string |
| ``ingredients`` | The ingredients used to make the recipe | string |
| ``n_ingredients`` | The number of ingredients the recipe calls for | int |

The next dataset is called `interactions` and it contains 731927 rows of data regarding the ratings of dishes. Each row corresponds to one review.

| Column | Description | Type |
| ``user_id`` | ID of the user who submitted the review | int |
| ``recipe_id`` | Unique recipe ID | int |
| ``date`` | The date the review was submitted | string |
| ``rating`` | Rating given by the user | int |
| ``review`` | The text of the review itself | string |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Both of these datasets have information about the recipe in common. I merged `recipes` and `interactions` into `full_recipes` so that there would be one row for each rating, but now there is added information about the recipe for which the rating was made.

Wherever the ``rating`` column is 0, that means the user did not leave a star rating on the recipe. This means that their review consisted only of text or maybe a picture. Because of this, all 0's are replaced with np.nans.

To deal with the fact that there may be multiple rows for a single recipe and no central rating for a single recipe, the star ratings for each recipe are averaged out and placed in a new column ``recipe_avg_rating``.

Some people decide not to add any tags to their recipe when they submit it, and the representation of that in the data is "['']" in the ``tags`` column. These are also replaced with np.nans.

It helps to have the various dates here converted to pandas Timestamps. This is done for both the ``date`` and ``submitted`` column. Furthermore, the ``submitted`` column is used to make another column called ``era``. This column categorizes dates into one of two categories, 2008-2012 or 2013-2018 depending on when the recipe was submitted. There is also a column called ``is_old_era`` which is full of booleans indicating if the recipe came from the 2008-2012 era.

The ``minutes`` column contains some ridiculously long recipes which were outliers for the sake of my analysis. One of these is actually a satire post about preserving a husband linked [here!](https://www.food.com/recipe/how-to-preserve-a-husband-447963) I decided that I only cared about recipes that took 100 hours or less to make, so I only kept recipes with a value of 6000 or less form the ``minutes`` column.

Since the ``nutrition`` is stored as a string instead of a list and the values aren't labeled, I created 7 new columns for each nutrition fact and then dropped ``nutrition``.

The final dataframe is made of 234151 rows and 26 columns! A description of the columns and a peek at a subset of the DataFrame are below.

| Column | Description | Type |
| ``name`` | Name of the recipe | string |
| ``id`` | Unique recipe ID | int |
| ``minutes`` | The minutes it takes to prepare | int |
| ``contributor_id`` | ID of the user who submitted the recipe | int |
| ``submitted`` | The date the recipe was submitted | string |
| ``tags`` | Any tags the user added to the recipe | string |
| ``n_steps`` | The number of steps in the recipe | int |
| ``steps`` | The actual steps to follow for the recipe | string |
| ``description`` | Description of the recipe given by the user | string |
| ``ingredients`` | The ingredients used to make the recipe | string |
| ``n_ingredients`` | The number of ingredients the recipe calls for | int |
| ``user_id`` | ID of the user who submitted the review | int |
| ``recipe_id`` | Unique recipe ID | int |
| ``date`` | The date the review was submitted | string |
| ``rating`` | Rating given by the user | int |
| ``review`` | The text of the review itself | string |
| ``recipe_avg_rating`` | The average rating of a recipe | float |
| ``era`` | Either 2008-2012 or 2013-2018 depending on submitted date | string |
| ``is_old_era`` | Boolean indicating whether this recipe came from 2008-2012 | boolean |
| ``calories`` | calories | string |
| ``total fat (PDV)`` | total fat (PDV) | string |
| ``sugar (PDV)`` | sugar (PDV) | string |
| ``sodium (PDV)`` | sodium (PDV) | string |
| ``protein (PDV)`` | protein (PDV) | string |
| ``saturated fat (PDV)`` | saturated fat (PDV) | string |
| ``carbohydrates (PDV)`` | carbohydrates (PDV) | string |

---

| name                                 |     id |   minutes | submitted           |   n_steps |   n_ingredients |          user_id |   rating |   recipe_avg_rating | era       | is_old_era   |   calories |   sugar (PDV) |   protein (PDV) |
|:-------------------------------------|-------:|----------:|:--------------------|----------:|----------------:|-----------------:|---------:|--------------------:|:----------|:-------------|-----------:|--------------:|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 | 2008-10-27 00:00:00 |        10 |               9 | 386585           |        4 |                   4 | 2008-2012 | True         |      138.4 |            50 |               3 |
| 1 in canada chocolate chip cookies   | 453467 |        45 | 2011-04-11 00:00:00 |        12 |              11 | 424680           |        5 |                   5 | 2008-2012 | True         |      595.1 |           211 |              13 |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |         6 |               9 |  29782           |        5 |                   5 | 2008-2012 | True         |      194.8 |             6 |              22 |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |         6 |               9 |      1.19628e+06 |        5 |                   5 | 2008-2012 | True         |      194.8 |             6 |              22 |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |         6 |               9 | 768828           |        5 |                   5 | 2008-2012 | True         |      194.8 |             6 |              22 |

### Univariate Analysis

Since we are concerned about time in this analysis, I plotted the distribution of minutes for the recipes. Despite my removing outliers, there is still a strong right tail to the data, but most of it is less than 100 minutes.

<iframe
  src="assets/minutes_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
### Bivariate Analysis

To learn more about the relationship between time and rating, we could plot the average ratings of all recipes on a boxplot conditioned by what era the recipe comes from. As we can see, the distribution of more recent recipes is slightly above!
<iframe
  src="assets/era_and_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
### Interesting Aggregates

When aggregating by rating (the possible values are 1-5), we see uniform stats across the board, although it seems that as the rating goes up, the minutes decreases slightly

|   rating |   minutes |   n_steps |   n_ingredients |
|---------:|----------:|----------:|----------------:|
|        1 |   79.1056 |  10.5423  |         9.17723 |
|        2 |   96.9319 |  10.7599  |         9.2509  |
|        3 |   75.9361 |   9.84942 |         9.18451 |
|        4 |   73.1998 |   9.79256 |         9.27946 |
|        5 |   72.5459 |  10.1221  |         9.18943 |


## Assesment of Missingness

### NMAR Analysis

I believe the ``review`` column is NMAR. The only columns with missingness are ``tags``, ``description``, and ``review``

#### tags
- Is likely only missing if it's a basic recipe or the author was lazy/forgot
- Proabable that it's MAR on ``minutes``, ``n_steps``, ``n_ingredients``

#### description
- Once again only left out if the recipe is that basic or if the author was lazy/forgot
- Arguably MAR on the same columns ``tags`` would be

#### review
- Only missing if the actual review was solely the star rating or a photo
- Since ``review`` is missing based on its actually value for the case of a photo, it supports the idea of NMAR
- If a user only leaves a star rating it likely means they didn't feel very strongly about a dish, people will tend to actually write when the recipe was very good or very bad

### Missingness Dependency
Now, I went to determine the missingness of ``description`` based on various other columns. 

**Missingness of description**

*Null Hypothesis*: Missingness of description is MCAR on n_ingredients

*Alternative Hypothesis*: Missingness of description is MAR on n_ingredients

*Test Statistic*: Absolute Difference in Means

*Significance Level*: 0.05

To determine the missingness, we consider the distributions of n_ingredients conditioned on the missingness of description. We run a permutation test to see if these distributions come from the same underlying distribution.

<iframe
  src="assets/ing_MAR.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
P-Value: 0.0

Conclusion: We reject the null in favor of the alternative, the missingness of description is MAR on n_ingredients

---

*Null Hypothesis*: Missingness of description is MCAR on minutes

*Alternative Hypothesis*: Missingness of description is MAR on minutes

*Test Statistic*: Absolute Difference in Means

*Significance Level*: 0.05

To determine the missingness, we consider the distributions of minutes conditioned on the missingness of description. We run a permutation test to see if these distributions come from the same underlying distribution.

<iframe
  src="assets/min_MCAR.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
P-Value: 0.622

Conclusion: We keep the null, the missingness of description is MCAR on minutes

## Hypothesis Testing

In order to learn more about the relationship between time and ratings, lets see if ratings from 2008-2012 follow the same distribution of ratings from 2013-2018.

*Null Hypothesis*: Ratings from 2008-2012 are the same as ratings from 2013-2018.

*Alternative Hypothesis*: Ratings from 2008-2012 are different from ratings from 2013-2018.

Test Statistic: Absolute Difference in Means

We run a permutation test here since we are curious if the two samples come from the same distribution. This is an important pair of hypotheses to test because it reveals if the era is significant at all to predicting rating.

<iframe
  src="assets/era_hyp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
P-value: 0.048

At the 0.05 significance level, we reject the null hypothesis and find that the quality of a rating **is different** for earlier and later recipes!

## Framing a Prediction Problem

We will be predicting the average ratings of recipes through classification. Although average ratings are a continuous category, we will round each one to the nearest whole number such that the only possible values are 1-5, which means we are doing multiclass classification.

Our response variable in this case is ratings since it is useful to know how good a recipe will be when you can't look at any ratings. Our metric is F1-Score since the distribution of ratings is skewed right. We might see a misleading accuracy if our model just predicts higher ratings. 

My models will use information from the following columns:
["minutes", "submitted", "n_steps", "n_ingredients", "calories", "sugar (PDV)", "protein (PDV)"]. These are all fine to use since you have these values before any ratings come in, they are all available at time of prediction.

## Baseline Model

Using a random forest classifier, my baseline model will use information from ``submitted`` and ``minutes``, where they are both transformed using a quanitle transformer. This is because there are plenty of outliers present in both columns. Both columns are quantitative.

After fitting the data, we tested the model's ability to generalize to unseen data and saw an F1-Score of approximately 0.7466. The current model is certainly better than guessing at random, but there is likely more to predicting the rating of a recipe!

## Final Model

In the final model, I added the features of ``n_steps`` and ``n_ingredients`` with a binarizer on each. The threshold was set to 8 and the idea is to split up recipes into long and short ones. More than 8 steps means the recipe is long, and so does more than 8 ingredients.

Furthermore, I added in the nutritional ratings of ``calories``, ``sugar (PVD)``, and ``protein (PDV)``. I felt that these would improve my model's accuracy since they are all things people give weight to when deciding whether to make a recipe.

Once again a random forest classifier was used, but employed optimal hyperparameters found through cross-validation. The optimal parameters were: [criterion='gini', max_depth=35, min_samples_split=50]

This model also performed well on unseen data, with an F1-Score of approximately 0.8161, a significant increase from the baseline model.

## Fariness Analysis

I will split my data into 2 groups, long recipes and short recipes. For our purposes a long recipe will be anything longer than 35 minutes since that was the median time.

*Null Hypothesis*: My final model performs the same on recipes shorter and longer than 35 minutes.

*Alternative Hypothesis*: My final model performs differently on recipes shorter and longer than 35 minutes.

*Test Statistic*: Absolute Difference in Means

*Significance Level*: 0.05

<iframe
  src="assets/fairness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*P-Value*: 0.0

At the 0.05 significance level, we reject the null hypothesis. Our model performs differently for recipes depending on how long they take to make!