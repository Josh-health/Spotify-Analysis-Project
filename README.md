# Sportify Analysis Using Postgre.Sql

### Description
This project explores Spotify's music data using SQL to uncover patterns in song popularity, artist trends, and genre performance. By analyzing streaming data, song features, and user engagement, we extract valuable insights into what makes a song a hit!

![spotify_logo](https://github.com/Josh-health/Spotify-Analysis-Project/blob/main/spotify.png)

### Objectives
- Identify top-streamed songs and artists globally.
- Analyze the impact of song features (tempo, danceability, energy) on popularity.
- Find the most popular genres over time.
- Determine seasonal trends in music streaming.

### Dataset
The Dataset for this project is sourced from Kaggle Dataset
- **Dataset Link:** https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset

### Schema
```sql
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify(
	artist VARCHAR(255),
	track VARCHAR(255),
	album VARCHAR(255),
	album_type VARCHAR(255),
	danceability FLOAT,
	energy FLOAT,
	loudness FLOAT,
	speechiness FLOAT,
	acoustiness FLOAT,
	instrumentalness FLOAT,
	liveness FLOAT,
	valence FLOAT,
	tempo FLOAT,
	duration_min FLOAT,
	title VARCHAR(255),
	channel VARCHAR(255),
	views FLOAT,
	likes BIGINT,
	comments BIGINT,
	licensed BOOLEAN,
	official_video BOOLEAN,
	stream BIGINT,
	energy_liveness FLOAT,
	most_played_on VARCHAR(50)
	);
```
### Exploratory Data Analysis
```sql
SELECT COUNT(*) FROM spotify;
	SELECT COUNT(DISTINCT artist) FROM spotify;
	SELECT COUNT(DISTINCT album) FROM spotify;
	SELECT COUNT(DISTINCT album_type) FROM spotify;
	SELECT duration_min FROM spotify;
	SELECT MAX(duration_min)  FROM spotify;
	SELECT MIN(duration_min)  FROM spotify;
		SELECT * FROM spotify
		WHERE duration_min = 0;
		--- delete record with duration_min = 0
		DELETE FROM spotify
		WHERE duration_min = 0;
	SELECT DISTINCT channel FROM spotify;
	SELECT DISTINCT most_played_on FROM spotify;
```


