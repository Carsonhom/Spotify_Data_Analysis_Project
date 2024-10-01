# Spotify Data Analysis Project


## Contents
- [Overview](#overview)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Conclusion](#conclusion)


## Overview
A spotify data analysis excercise using data from the top spotify songs as of Fri Sep 27. In this project I used statistical analysis methods to derive insights from the raw data as to what attributes might contribute to a top charting song. This project covers many of the key areas of data analysis from data aggregation to exploratory data analysis. The source of the data used for this project can be found at [kaggle](https://www.kaggle.com/datasets/asaniczka/top-spotify-songs-in-73-countries-daily-updated).

Tools used:
- Programming Language: Python
- Libraries: pandas, numpy, seaborn, matplotlib
- Environment: Jupyter Notebook


## Exploratory Data Analysis
This particular Spotify dataset is comprised of the daily top songs up to Friday September 27th and includes 25 features such as track name, artist name, daily ranking as well as audio features like speechiness, tempo, danceability, etc.
```
sp_tracks.head()
```
<img width="1029" alt="Screenshot 2024-09-30 at 4 52 30 PM" src="https://github.com/user-attachments/assets/3a20050a-e26e-4560-92a3-bf556cc751ee">

The pearson correlation coefficient is a value between -1 and 1 that indicates how strongly correlated two numerical features are and what direction the regression trend line follows (+ or -). By creating a heatmap measuring pcc between all numerical features we will be able to determine the next best steps for our eda.
```
# Pearson Correlation Coefficient (r) Heatmap
td = sp_top50.drop(['spotify_id', 'name', 'artists', 'country', 'snapshot_date', 'daily_rank', 'daily_movement', 'weekly_movement', 'key'], axis = 1).corr(method = 'pearson')
plt.figure(figsize = (9, 5))
hmap = sns.heatmap(td, annot = True, fmt = '.1g', vmin = -1, vmax = 1, center = 0, linewidths = 0.1, linecolor = 'black')
hmap.set_title('Pearson Correlation Heatmap')
hmap.set_xticklabels(hmap.get_xticklabels(), rotation = 90)
```
<img width="796" alt="Screenshot 2024-09-30 at 4 53 36 PM" src="https://github.com/user-attachments/assets/cb17336b-e9a8-428e-932c-3a4d1de9822b">
Based on an initial investigation of the correlation coefficient between song attributes, loudness and energy appear to be strongly correlated amongst the top 50 songs on spotify. Below is an analysis of the distributions of both the loudness and energy field.

```
# Distribution of Loudness
sns.histplot(sp_top50['loudness'], kde = True)
plt.title('Distribution of loudness')
plt.show()

# Distribution of Energy
sns.histplot(sp_top50['energy'], kde = True)
plt.title('Distribution of Energy')
plt.show()
```
<img width="455" alt="Screenshot 2024-09-30 at 4 56 10 PM" src="https://github.com/user-attachments/assets/2a1b48b8-87d5-4c7d-9466-b6676beb23f0">

As we can observe visually, the two fields appear to share a strong positive correlation. However an even better way to determine this is by modeling the linear regression between the two fields and observing their trend line.

```
# Regression plot of loudness & energy
plt.figure(figsize = (9, 5))
sns.regplot(data = sp_top50, y = 'loudness', x = 'energy', line_kws = {'color': 'red'}).set_title('Regression Plot of Energy vs. Loudness')
```
<img width="841" alt="Screenshot 2024-09-30 at 4 58 08 PM" src="https://github.com/user-attachments/assets/34934c78-39b2-4f9a-8065-47e6a60af1f4">


Another area of interest could be the length of popular songs, the current .csv file records the duration of songs in milliseconds but to improve readability we can convert this field to seconds before modeling the distribution. Below I have included the code snippet used to create a new field called "duration" that denotes each song's length in seconds.

```
# convert duration from ms to s
sp_top50['duration'] = sp_top50['duration_ms'].apply (lambda x : round(x/1000))
sp_top50.duration.head()
```

Using this new durration field we can model the distribution of song lengths.

```
sns.histplot(sp_top50['duration'], kde = True)
plt.title('Distribution of Duration')
plt.show()
```
<img width="625" alt="Screenshot 2024-09-30 at 5 03 13 PM" src="https://github.com/user-attachments/assets/3722359f-db8d-47a7-8288-2c7d2292dba9">

According to the distribution above, it appears as though the most popular songs tend to last around three minutes in length which lines up with the durration of most music on Spotify.

Continuing on we can also investigate the artists who have appeared in Spotify's top charting music the most within the last few weeks.
```
# List of artists who appeared in top 50
sp_artists = sp_top50['artists'].str.split(', ').explode()

# Top artists
top_artists = sp_artists.value_counts()

# Top 10 artists
top10_artists = top_artists.head(10)

plt.figure(figsize = (10, 6))
top10_artists.plot(kind = 'bar', color = 'green')
plt.title('Top 10 Most Popular Artists Among Trending Songs')
plt.xlabel('Artist')
plt.ylabel('Frequency')
plt.xticks(rotation = 45, ha = 'right')
plt.tight_layout()
```
<img width="1015" alt="Screenshot 2024-09-30 at 5 05 31 PM" src="https://github.com/user-attachments/assets/c758acf7-2ab0-42ce-9ff9-40e14276ee70">

## Conclusion
If you would like to view a more comprehensive data analysis please feel free to view the Jupyter notebook file itself.
