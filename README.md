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

Wherever the ``rating`` column is 0, that means the user did not leave a star rating on the recipe. This means that their review consisted only of text or maybe a picture. Because of this, all 0's are replace with np.nans.

To deal with the fact that there may be multiple rows for a single recipe and no central rating for a single recipe, the star ratings for each recipe are averaged out and placed in a new column ``recipe_avg_rating``.

Some people decide not to add any tags to their recipe when they submit it, and the representation of that in the data is "['']" in the ``tags`` column. These are also replace with np.nans.

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

---

### Bivariate Analysis

To learn more about the relationship between time and rating, we could plot the average ratings of all recipes on a boxplot conditioned by what era the recipe comes from. As we can see, the distribution of more recent recipes is slightly above!

<iframe
  src="assets/era_and_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

### Interesting Aggregates

When aggregating by rating (the possible values are 1-5), we see uniform stats across the board, although it seems that as the rating goes up, the minutes decreases slightly

|   rating |   minutes |   n_steps |   n_ingredients |
|---------:|----------:|----------:|----------------:|
|        1 |   79.1056 |  10.5423  |         9.17723 |
|        2 |   96.9319 |  10.7599  |         9.2509  |
|        3 |   75.9361 |   9.84942 |         9.18451 |
|        4 |   73.1998 |   9.79256 |         9.27946 |
|        5 |   72.5459 |  10.1221  |         9.18943 |


