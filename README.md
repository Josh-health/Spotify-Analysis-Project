# Spotify Analysis Using Postgre.Sql

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
### Questions and Findings

1. Retrieve the names of all tracks that have more than 1 bilion streams.
```sql
 	SELECT 
		track
	FROM spotify
	WHERE stream > 1000000000;
```

2. List all albmus along with their respective artists.
```sql
	SELECT 
		DISTINCT album,
		artist
	FROM spotify
	ORDER BY 1;
```

3. Get the total number of comments for tracks where licenced = TRUE
```sql
	SELECT * FROM spotify
	WHERE licensed = 'true';
```
```sql
	SELECT 
		sum(comments) AS total_comments
	FROM spotify
	WHERE licensed = 'true';
```
 
4. Find all the tracks that belong to the album type single.
```sql
	SELECT 
		track
	FROM spotify
	WHERE album_type = 'single';
```

5. Count the total number of tracks by each artist
```sql
	SELECT
		artist,
		COUNT(track) AS total_num_songs
	FROM spotify
	GROUP BY 1
	ORDER BY 2 DESC;
```

6. Calculate the danceability of tracks in each album
```sql
	SELECT * FROM spotify;
	SELECT
		album,
		avg(danceability) as avg_danceability
	FROM spotify
	GROUP BY 1
	ORDER BY 2 DESC;
```
	
7. Find the top 5 tracks with the highest energy values
```sql
	SELECT
		track,
		AVG(energy) -- using avg because there are tracks with more than one record.
	FROM spotify
	GROUP BY 1
	ORDER BY 2 DESC
	LIMIT 5;
```

8. List all the tracks along with their views and likes where official_video = TRUE
```sql	
 	SELECT
		track,
		SUM(views) AS total_num_views,
		SUM(likes) AS total_num_likes
	FROM spotify
	WHERE official_video = 'true'
	GROUP BY 1
	ORDER BY 2 DESC;
```

9. For each album, calculate the total views of all associated tracks
```sql
	SELECT
		album,
		track,
		SUM(views) AS total_views
	FROM spotify
	GROUP BY 1,2
	ORDER BY 3 DESC ;
```

10. Retrieve the track name that has been streamed on Spotify more than Youtube.
```sql
	SELECT * FROM spotify;
```
```sql	
SELECT * FROM
    (SELECT
        track,
        -- most_played_on,
        COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END),0) as streamed_on_youtube,
		COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END),0) as streamed_on_spotify
	FROM spotify
	GROUP BY 1
	) AS t1
	WHERE 
		streamed_on_spotify > streamed_on_youtube
	AND 
		streamed_on_youtube <> 0;
```
	
11. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
	SELECT 	
		artist,
		track,
		SUM(views) AS total_views
	FROM spotify
	GROUP BY 1, 2
	ORDER BY 1, 3 DESC;
	
	-- track with the highest view for each artist (we need top)
	-- dense rank
	SELECT 	
		artist,
		track,
		SUM(views) AS total_views,
		DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views)DESC) AS rank
	FROM spotify
	GROUP BY 1, 2
	ORDER BY 1, 3 DESC;
```

```sql	
 	WITH ranking_artist
	AS
 	(SELECT 	
		artist,
		track,
		SUM(views) AS total_views,
		DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views)DESC) AS rank
	FROM spotify
	GROUP BY 1, 2
	)
	SELECT * FROM ranking_artist
	WHERE rank <= 3;
	ORDER BY 1, 3 DESC;
```

12 Write a query to find where the liveness score is above average

```sql
	SELECT AVG(liveness) FROM spotify; -- 0.19
	
		-- find the tracks that has livess score > 0.19
	SELECT 
		track,
		artist,
		liveness
	FROM spotify
	WHERE liveness > (SELECT AVG(liveness)FROM spotify);
```

 13. Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album
```sql
	SELECT 
		album,
		MAX(energy) AS highest_energy,
		MIN(energy) AS lowest_energy
	FROM spotify
	GROUP BY 1;
```
```sql
	WITH cte
	AS(
	SELECT 
		album,
		MAX(energy) AS highest_energy,
		MIN(energy) AS lowest_energy
	FROM spotify
	GROUP BY 1
	)
	SELECT 
		album,
		highest_energy - lowest_energy AS energy_diff
	FROM cte
	ORDER BY 2 DESC;
```
 14. Find tracks where there energy-to-liveness ratio is greater than 1.2
```sql	
 	WITH CTE
	AS(
	SELECT
		track,
		energy,
		liveness,
		energy/liveness AS energy_to_liveness 
	FROM spotify
	WHERE liveness <> 0
	)
	SELECT *
	FROM CTE
	WHERE energy_to_liveness > 1.2;
```

15. Calculate the cummulative sum of likes for tracks 
-- ordered by the number of views, using the window function
```sql
	SELECT * FROM spotify;
	SELECT 
		track,
		views,
		SUM(likes) OVER (ORDER BY views) AS cumulative_likes
	FROM spotify
	ORDER BY 2;
```

### Insight and Conclusions:
- Only a few tracks surpass 1 billion streams, indicating that extreme popularity is rare and concentrated among a small group of songs.
- Certain tracks are streamed more on Spotify than on YouTube, which may reflect differences in user preferences across platforms.
- Some albums have consistently higher danceability scores, which may indicate a focus on creating upbeat, dance-friendly music.
- Licensed tracks tend to have a high number of comments, suggesting that licensing might correlate with better engagement and visibility.



