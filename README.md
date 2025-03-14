# Timeless recipes: The Relationship Between Time and a Recipe's Rating

## Introduction

There are many qualities that a good recipe may have, but the question I plan to explore is, **Does the amount of time it takes to make a recipe correlate with how good it is?** Another interesting way we can connect time to a recipe is to ask if the date the recipe was made is a good predictor of its quality. Perhaps we have gotten better at making food so more recent recipes will be better.

This question is important because it provides insight into what makes a recipe good and lets us predict the quality without needing to know other people rated it. Also, it would be interesting if there was a certain range of years that had worse/better food than others. We may need to reconsider our cooking abilities or keep doing what is working! In any case, we are working with 2 datasets from [Food.com](https://www.food.com/?ref=nav). 

The first dataset, `recipes`, contains various pieces of data on the recipes from the website. Each row corresponds to one recipe.

| Column | Description |
| `name` | Name of the recipe |
| `id` | Unique recipe ID |
| `minutes` | The minutes it takes to prepare |
| `contributor_id` | ID of the user who submitted the recipe |
| `submitted` | The date the recipe was submitted |
| `tags` | Any tags the user added to the recipe |
| `nutrition` | A list of nutritional information following the form:[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)] where PDV means percent of daily value |
| `n_steps` | The number of steps in the recipe |
| `steps` | The actual steps to follow for the recipe |
| `description` | Description of the recipe given by the user |
| `ingredients` | The ingredients used to make the recipe |
| `n_ingredients` | The number of ingredients the recipe calls for |

