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
  height="600"
  frameborder="0"
></iframe>

The distribution of `danceability` among songs by hip-hop artists is concentrated mostly between about **0.5 and 0.85**, with the highest counts around **0.7 to 0.8**. This suggests that songs by hip-hop artists in this dataset tend to be relatively danceable, which makes sense given the rhythmic structure often associated with hip-hop, rap, and related genres.

<br>

<iframe
  src="assets/nonzero-popularity-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of nonzero track popularity is centered mostly around the **35 to 65** range, with fewer songs reaching very high popularity scores above **80**. This shows that most songs by hip-hop artists have moderate popularity, while extremely popular tracks are less common.

---

## Bivariate Analysis

<iframe
  src="assets/nonzero-popularity-by-song-type.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The nonzero popularity box plot gives a cleaner comparison after removing tracks with a popularity score of `0`. In this version, hip-hop songs have a higher median popularity than non-hip-hop songs, suggesting that the large number of zero-popularity hip-hop tracks strongly affects the original comparison.

---

## Aggregate Analysis

| song_type        | number_of_songs | mean_track_popularity | median_track_popularity | mean_danceability | mean_energy | mean_valence |
| :--------------- | --------------: | --------------------: | ----------------------: | ----------------: | ----------: | -----------: |
| Hip-Hop Song     |             455 |                30.818 |                       3 |             0.754 |       0.679 |        0.558 |
| Non-Hip-Hop Song |            7447 |                35.199 |                      39 |             0.660 |       0.699 |        0.509 |

The aggregate table shows that non-hip-hop songs by hip-hop artists have a higher mean and median track popularity than hip-hop songs in this cleaned dataset, with a much larger number of non-hip-hop songs represented. The audio feature averages are fairly similar across both groups, suggesting that the main difference in this summary table is popularity rather than large differences in features like energy, speechiness, liveness, or valence.
