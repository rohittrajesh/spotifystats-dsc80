# Introduction

Music streaming platforms like Spotify provide a large amount of data about songs, artists, genres, and listener engagement. In this project, we are working with two datasets provided by Spotify: `music_tracks.csv`, which contains track-level information, and `artists.csv`, which contains artist-level information.

The tracks dataset includes information such as each song’s name, artist, album, popularity score, release date, genre, and audio features like `danceability`, `energy`, `loudness`, `tempo`, and `valence`. The artists dataset includes information such as each artist’s name, follower count, popularity score, and associated genre tags.

## Research Question

The question this project is centered around is the following:

> **Do global hip-hop artists find more success in their non-hip-hop songs compared to their hip-hop songs?**

In this project, “success” will mainly be measured using Spotify's track popularity metric, which is a score from 0 to 100 based on total plays and recency. This question is interesting because hip-hop artists often experiment with other genres, collaborate across genres, or release songs that are labeled outside of traditional hip-hop. By comparing hip-hop songs to non-hip-hop songs from hip-hop artists, we can investigate whether moving outside of hip-hop is associated with higher or lower popularity amongst a global audience!

## Why This Question Matters

This dataset and research question is important because music success is shaped by both artistic identity and genre flexibility. For artists, labels, and listeners, genre can influence how music is marketed, discovered, and received.

If non-hip-hop songs by hip-hop artists tend to be more popular, this may suggest that crossing genre boundaries can help artists reach larger audiences. If hip-hop songs are more popular, this may suggest that artists are most successful when staying closer to their original genre identity.

## Dataset Overview

The `music_tracks.csv` dataset contains **113,999 rows**, and the `artists.csv` dataset contains **1,162,081 rows**.

### Relevant Columns from `music_tracks.csv`

The columns most relevant to this project from `music_tracks.csv` are:

* `artists`
* `track_name`
* `popularity`
* `release_date`
* `explicit`
* `danceability`
* `energy`
* `loudness`
* `speechiness`
* `acousticness`
* `instrumentalness`
* `valence`
* `tempo`
* `track_genre`

The `artists` column identifies the artist or artists on each track, while `track_name` identifies the song. The `popularity` column is especially important because it will be used as the main measure of track success. The `track_genre` column will help classify songs as hip-hop or non-hip-hop. The audio feature columns, such as `danceability`, `energy`, `speechiness`, `acousticness`, `valence`, and `tempo`, may help explain how hip-hop and non-hip-hop songs differ musically.

### Relevant Columns from `artists.csv`

The columns most relevant to this project from `artists.csv` are:

* `name`
* `followers`
* `popularity`
* `genres`

The `name` column identifies each artist, while `followers` and `popularity` provide artist-level measures of success. The `genres` column is important because it can be used to identify artists associated with hip-hop and narrow the analysis to hip-hop artists.

Together, these two datasets allow us to connect artist identity, genre classification, track features, and popularity in order to investigate whether hip-hop artists perform better when releasing hip-hop or non-hip-hop songs.

# Data Cleaning and Exploring

## Data Cleaning Steps

Before analysis, the original `music_tracks.csv` and `artists.csv` datasets were cleaned and reorganized to answer the research question:

> **Do hip-hop artists find more success in their non-hip-hop songs compared to their hip-hop songs?**

First, unnecessary columns were removed. From `music_tracks.csv`, the columns `track_id`, `duration_ms`, `explicit`, `loudness`, `mode`, `acousticness`, `instrumentalness`, `tempo`, and `time_signature` were dropped because they were not central to the research question or planned analyses. From `artists.csv`, the `id` column was dropped because it was only an artist identifier. The remaining columns preserve the information needed to identify tracks and artists, measure popularity, classify genres, and use audio features such as `danceability`, `energy`, `speechiness`, `liveness`, and `valence`.

Before removing missing values, the number of missing values in each dataset was calculated and the names of columns containing missing values were stored. This step was important because missingness will be analyzed later as part of the project. After recording this information, rows with missing values were dropped from both datasets to create complete observations for visualizations, hypothesis testing, and regression.

The cleaned tracks and artists datasets were then merged using `tracks['artists']` and `artists['name']`. After merging, the track-level popularity column became `popularity_track`, while the artist-level popularity column became `popularity_artist`. This distinction matters because `popularity_track` measures the success of a specific song, while `popularity_artist` measures the overall popularity of the artist. The `genres` column was also renamed to `artist_genre` to make clear that it describes the artist’s genre tags, while `track_genre` describes the genre of the individual song. Redundant columns such as `Unnamed: 0` and `name` were removed after the merge.

To prepare for the main comparison, two Boolean columns were created. The column `is_hiphop_artist` identifies artists whose `artist_genre` tags include hip-hop-related terms such as hip hop, rap, trap, or drill. The column `is_hiphop_track` identifies whether the specific track’s `track_genre` is hip-hop-related. This allowed the data to be separated into hip-hop artists’ hip-hop songs and hip-hop artists’ non-hip-hop songs.

Finally, these two groups were combined into `hiphop_artist_music_comparison`, with a new `song_type` column labeling each row as either `"Hip-Hop Song"` or `"Non-Hip-Hop Song"`. This final cleaned DataFrame is organized for the project’s main visualizations, aggregate tables, hypothesis test, and regression analysis. Low-popularity songs and artists were not removed because popularity is the main outcome variable, and filtering by the outcome before analysis could bias the results.

## Univariate Analysis

<iframe
  src="assets/danceability-distribution.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
  
></iframe>

The distribution of `danceability` among songs by hip-hop artists is concentrated mostly between about **0.5 and 0.85**, with the highest counts around **0.7 to 0.8**. This suggests that songs by hip-hop artists in this dataset tend to be relatively danceable, which makes sense given the rhythmic structure often associated with hip-hop, rap, and related genres.

<br>

<iframe
  src="assets/nonzero-popularity-distribution.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

The distribution of nonzero track popularity is centered mostly around the **35 to 65** range, with fewer songs reaching very high popularity scores above **80**. This shows that most songs by hip-hop artists have moderate popularity, while extremely popular tracks are less common.

---

## Bivariate Analysis

<iframe
  src="assets/nonzero-popularity-by-song-type.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

The nonzero popularity box plot gives a cleaner comparison after removing tracks with a popularity score of `0`. In this version, hip-hop songs have a higher median popularity than non-hip-hop songs, suggesting that the large number of zero-popularity hip-hop tracks strongly affects the original comparison.

---

## Aggregate Analysis

| song_type        | number_of_songs | mean_track_popularity | median_track_popularity | mean_danceability | mean_energy | mean_valence |
| :--------------- | --------------: | --------------------: | ----------------------: | ----------------: | ----------: | -----------: |
| Hip-Hop Song     |             455 |                30.818 |                       3 |             0.754 |       0.679 |        0.558 |
| Non-Hip-Hop Song |            7447 |                35.199 |                      39 |             0.660 |       0.699 |        0.509 |

The aggregate table shows that non-hip-hop songs by hip-hop artists have a higher mean and median track popularity than hip-hop songs in this cleaned dataset, with a much larger number of non-hip-hop songs represented. The audio feature averages are fairly similar across both groups, suggesting that the main difference in this summary table is popularity rather than large differences in features like energy, speechiness, liveness, or valence.

# Assessment of Missingness

## NMAR Analysis

There may be some missingness in this dataset that is **NMAR**, or **not missing at random**. In particular, missing values in columns related to artist or track metadata, such as `artist_genre`, `track_genre`, `followers`, or `popularity`, may depend on the actual value that is missing.

For example, a smaller or less-established artist may be less likely to have complete genre tags, follower counts, or popularity information recorded on Spotify. In that case, the missingness would not just be caused by another observed column in the dataset; it would be related to the artist’s actual level of visibility or popularity.

This matters because the project focuses on comparing popularity between hip-hop artists’ hip-hop and non-hip-hop songs. If less popular artists or less popular tracks are more likely to have missing metadata, then dropping rows with missing values could make the cleaned dataset overrepresent more visible or established artists. That would make the missingness potentially **NMAR** because the reason a value is missing may be connected to the unobserved popularity, genre classification, or artist visibility itself.

Additional data that would help explain the missingness includes information about how Spotify collects genre and popularity data, whether missing values are more common for newer or less-streamed artists, and whether certain labels, countries, or independent artists have less complete metadata. If those additional variables explained the missingness, then the missingness might be better classified as **MAR** instead of **NMAR**.

---

## Missingness Dependency

To further investigate missingness, I performed permutation tests on the missingness of `followers`. The goal was to determine whether missingness in `followers` depended on other observed columns in the data.

---

### Missingness of `followers` and Multiple Artists

<iframe
  src="assets/multiple-artists-missingness.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

**Null hypothesis:** The missingness of `followers` does not depend on whether a track has multiple artists.

**Alternative hypothesis:** The missingness of `followers` does depend on whether a track has multiple artists.

**Test statistic:** Absolute difference in the proportion of multiple-artist tracks between rows where `followers` is missing and rows where `followers` is not missing.

**p-value:** `p < 0.001`

#### Result: The missingness of `followers` strongly depends on whether the track has multiple artists.

For this missingness dependency test, a temporary left-merged DataFrame was created by merging the tracks dataset with the artists dataset while keeping all tracks, even when artist-level information failed to match. The permutation test found an observed difference of about **0.909** with `p < 0.001`, so the missingness of `followers` strongly depends on whether a track has multiple artists.

This makes sense because tracks with multiple artists are harder to match exactly to a single artist name in the artists dataset, causing artist-level fields like `followers` to become missing after the merge.

---

### Missingness of `followers` and Track Popularity

<iframe
  src="assets/popularity_track_missingness.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

**Null hypothesis:** Missingness of `followers` does not depend on `popularity_track`.

**Alternative hypothesis:** Missingness of `followers` does depend on `popularity_track`.

**Test statistic:** Absolute difference in mean `popularity_track` between rows where `followers` is missing and rows where `followers` is not missing.

**p-value:** `0.44`

#### Result: The missingness of `followers` does not depend on `popularity_track`.

The permutation test produced a p-value of **0.44**, so there is not enough evidence to conclude that missingness in `followers` depends on `popularity_track`. This non-dependency makes sense because whether artist follower information fails to merge is more related to artist-name matching issues than to how popular the track itself is.

Since `followers` missingness does depend on `multiple_artists` but does not depend on `popularity_track`, this supports classifying the missingness as **MAR**, because the missingness can be explained by an observed column in the data.

# Hypothesis Testing

To answer the research question, a **one-sided permutation test** was used to compare the popularity of hip-hop artists’ hip-hop songs and non-hip-hop songs.

---

## Hypotheses

**Null hypothesis:** Among hip-hop artists, the popularity of hip-hop songs and non-hip-hop songs comes from the same distribution.

**Alternative hypothesis:** Among hip-hop artists, non-hip-hop songs tend to have higher popularity than hip-hop songs.

---

## Test Statistic

**Test statistic:** Mean popularity of non-hip-hop songs minus mean popularity of hip-hop songs.

**Significance level:** `0.05`

This test statistic is appropriate because the research question asks whether non-hip-hop songs by hip-hop artists are more successful than their hip-hop songs. Since success is measured using `popularity_track`, a positive test statistic means that non-hip-hop songs have higher average popularity than hip-hop songs. A permutation test is appropriate because the null hypothesis says the two groups come from the same distribution, meaning the `song_type` labels can be shuffled under the null.

---

## Permutation Test Visualization

<iframe
  src="assets/hypothesis-test.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

---

## Results and Conclusion

The observed test statistic was approximately **4.381**, meaning that non-hip-hop songs had an average popularity score about **4.381 points higher** than hip-hop songs. The resulting p-value was `0.0`, which should be interpreted as **p < 0.001** because none of the 1000 simulated test statistics were at least as large as the observed statistic.

Since the p-value is less than the significance level of `0.05`, the **null hypothesis is rejected**. There is evidence to suggest that, among hip-hop artists in this dataset, **non-hip-hop songs tend to have higher popularity than hip-hop songs**.

# Framing a Prediction Problem

For the prediction portion of this project, the goal is to predict a song’s `popularity_track` score using information about the song and its artist. This is a **regression problem** because the response variable, `popularity_track`, is numerical and ranges from **0 to 100**.

---

## Response Variable

The response variable is `popularity_track`, which represents the Spotify popularity score of a specific track. This variable was chosen because it directly connects to the main research question: whether hip-hop artists find more success in their non-hip-hop songs compared to their hip-hop songs.

Since the earlier hypothesis test used track popularity as the main measure of success, predicting `popularity_track` keeps the predictive model aligned with the rest of the project.

---

## Information Available at the Time of Prediction

At the time of prediction, the model would reasonably know information about the song’s genre label and audio features, such as:

* `song_type`
* `danceability`
* `energy`
* `speechiness`
* `liveness`
* `valence`

The model could also use artist-level information such as `popularity_artist` and `followers`, since these describe the artist’s general Spotify presence rather than the specific future popularity outcome of the track.

The model should not use information that directly leaks the answer, such as another version of the track popularity score.

---

## Evaluation Metric

The model will be evaluated using **root mean squared error**, or **RMSE**.

RMSE is appropriate because this is a regression problem and the goal is to measure how far the predicted popularity scores are from the true popularity scores. RMSE is preferred over accuracy because accuracy is meant for classification problems, not numerical prediction.

RMSE is also useful because it penalizes larger prediction errors more heavily, which matters when predicting a popularity score on a **0–100 scale**.

# Baseline Model

For the baseline model, I built a **regression model** to predict `popularity_track`, which represents the Spotify popularity score of a track. This is a regression model because the response variable is quantitative, ranging from **0 to 100**. The model was implemented using a single `sklearn` Pipeline that handled both preprocessing and model training.

---

## Features Used

The baseline model used four features:

* `song_type`
* `danceability`
* `energy`
* `popularity_artist`

The feature `song_type` is a **nominal categorical** feature because it labels each song as either a hip-hop song or a non-hip-hop song, with no natural ordering. This feature was encoded using `OneHotEncoder`.

The features `danceability`, `energy`, and `popularity_artist` are **quantitative** features and were passed through the pipeline without transformation.

Overall, the model used:

* **3 quantitative features**
* **1 nominal feature**
* **0 ordinal features**

---

## Model Choice

The model itself was a `LinearRegression` model. This was appropriate for a baseline model because it is simple, interpretable, and gives a clear starting point for comparison with the final model.

To evaluate generalization to unseen data, the data was split into training and test sets.

---

## Baseline Model Performance

The baseline model had a **training RMSE of about 25.05** and a **test RMSE of about 24.74**. The training and test `(R^2)` values were both about **0.03**, meaning the model explained only around **3%** of the variation in track popularity.

The baseline model was also compared to a simple mean-prediction baseline, which always predicts the average popularity from the training set. The mean baseline had a **test RMSE of about 25.09**, while the linear model had a **test RMSE of about 24.74**.

This means the baseline model performed slightly better than simply predicting the average track popularity, but the improvement was small.

---

### Baseline Model Results Table

| Dataset | RMSE | R² |
|:---|---:|---:|
| Train | 25.05 | 0.03 |
| Test | 24.74 | 0.03 |

### Mean Baseline Comparison

| Dataset | Mean Baseline RMSE | Linear Model RMSE |
|:---|---:|---:|
| Train | 25.39 | 25.05 |
| Test | 25.09 | 24.74 |


### Example Baseline Predictions

| Actual Popularity | Predicted Popularity |
|---:|---:|
| 0 | 30.37 |
| 0 | 31.87 |
| 61 | 38.08 |
| 46 | 40.52 |
| 58 | 31.31 |

---

## Overall Assessment

Overall, I do **not** consider the current baseline model to be a strong predictive model. Its RMSE is still high on a **0–100 popularity scale**, and its `(R^2)` value is very low.

However, it is a useful baseline because it satisfies the modeling requirements, generalizes similarly on the training and test sets, and gives a simple comparison point for the final model.

# Final Model

For the final model, I improved on the baseline model by adding more predictive features, engineering new features, and using a more flexible modeling algorithm. The response variable remained `popularity_track`, and the model was still evaluated using **RMSE** and `(R^2)` on the same train-test split structure as the baseline model.

---

## Features Used

The final model used the original features:

* `song_type`
* `danceability`
* `energy`
* `speechiness`
* `liveness`
* `valence`
* `popularity_artist`
* `followers`

In addition, I engineered two new features:

* `log_followers`
* `dance_energy_interaction`

The feature `log_followers` is a log-transformed version of `followers`, which is useful because follower counts are highly skewed. The difference between smaller artists and medium-sized artists is often more meaningful than the same raw follower difference among extremely large artists.

The feature `dance_energy_interaction` multiplies `danceability` and `energy`, which is useful because songs that are both highly danceable and highly energetic may behave differently from songs that are high in only one of those features.

---

## Modeling Algorithm

The final model used a `RandomForestRegressor`.

This algorithm was chosen because the baseline `LinearRegression` model could only capture linear relationships, while a random forest can capture nonlinear patterns and interactions between features. This is appropriate for music popularity prediction because popularity likely depends on combinations of song features and artist-level features rather than one simple linear relationship.

---

## Hyperparameter Selection

To select the best hyperparameters, I used `GridSearchCV` with **5-fold cross-validation** on the training data.

The hyperparameters tuned were:

* `n_estimators`
* `max_depth`
* `min_samples_leaf`

These were chosen because they control the complexity and stability of the random forest:

* `n_estimators` controls the number of trees.
* `max_depth` controls how deep each tree can grow.
* `min_samples_leaf` controls how small each leaf node can be.

The best-performing hyperparameters were:

| Hyperparameter     | Best Value |
| :----------------- | :--------- |
| `n_estimators`     | **200**    |
| `max_depth`        | **None**   |
| `min_samples_leaf` | **1**      |

---

## Final Model Performance

The final model performed much better than the baseline model.

| Model                      | Test RMSE | Test `(R^2)` |
| :------------------------- | --------: | -----------: |
| Baseline Linear Regression | **24.74** |     **0.03** |
| Final Random Forest        | **13.90** |     **0.69** |

The baseline linear regression model had a test RMSE of about **24.74** and a test `(R^2)` of about **0.03**. The final random forest model had a test RMSE of about **13.90** and a test `(R^2)` of about **0.69**.

This means the final model made substantially smaller prediction errors and explained much more of the variation in track popularity.

---

## Model Performance Visualization

<iframe
  src="assets/hypothesis-test.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

---

### Final Model Results Table

| Dataset | RMSE | R² |
|:---|---:|---:|
| Train | 7.47 | 0.91 |
| Test | 13.90 | 0.69 |


### Baseline vs. Final Model Comparison

| Model | Train RMSE | Test RMSE | Train R² | Test R² |
|:---|---:|---:|---:|---:|
| Baseline Linear Regression | 25.05 | 24.74 | 0.03 | 0.03 |
| Final Random Forest | 7.47 | 13.90 | 0.91 | 0.69 |


### Example Final Model Predictions

| Actual Popularity | Predicted Popularity |
|---:|---:|
| 0 | 9.24 |
| 0 | 11.65 |
| 61 | 49.70 |
| 46 | 49.41 |
| 58 | 47.44 |

---

## Overall Assessment

The final model did show some overfitting because its training RMSE was about **7.47**, while its test RMSE was about **13.90**. However, even on unseen test data, the final model was still a major improvement over the baseline model.

Overall, the final model is much stronger because it uses more relevant features, includes meaningful feature engineering, captures nonlinear relationships, and performs better on the test set.

# Fairness Analysis

For the fairness analysis, I compared the final model’s performance across two groups based on `song_type`.

---

## Groups Compared

**Group X:** `Hip-Hop Song`
**Group Y:** `Non-Hip-Hop Song`

The evaluation metric used was **RMSE** because this is a regression model predicting `popularity_track`. RMSE is appropriate because it measures the average size of the model’s prediction errors, with larger errors penalized more heavily.

---

## Hypotheses

**Null hypothesis:** The final model is fair across song type. Its RMSE for `Hip-Hop Song` tracks and `Non-Hip-Hop Song` tracks is roughly the same, and any observed difference is due to random chance.

**Alternative hypothesis:** The final model performs worse for `Hip-Hop Song` tracks than for `Non-Hip-Hop Song` tracks. In other words, the RMSE for `Hip-Hop Song` tracks is higher than the RMSE for `Non-Hip-Hop Song` tracks.

---

## Test Statistic

**Test statistic:** RMSE for `Hip-Hop Song` minus RMSE for `Non-Hip-Hop Song`.

**Significance level:** `0.05`

---

## Fairness Test Visualization

<iframe
  src="assets/fairness-test.html"
  width="800"
  height="400"
  frameborder="0"
  style="display: block; margin-bottom: 10px;"
></iframe>

---

## Results and Conclusion

The observed test statistic was approximately **0.595**, meaning the final model’s RMSE was about **0.595 points higher** for `Hip-Hop Song` tracks than for `Non-Hip-Hop Song` tracks. The resulting p-value was **0.329**.

Since the p-value is greater than the significance level of `0.05`, I fail to reject the null hypothesis. There is not enough evidence to conclude that the final model performs worse for `Hip-Hop Song` tracks than for `Non-Hip-Hop Song` tracks.

Based on this fairness test, the model does not show significant evidence of unfairness across these two song-type groups.

