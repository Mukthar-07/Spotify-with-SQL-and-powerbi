# Spotify Data Analysis Using SQL and POWERBI

![spotify](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR6VtBWC1lONAiSvXN3A67wGUpge2HTIo6JIg&s)


📌 Project Overview

This project explores a Spotify dataset using PostgreSQL for data analysis and Power BI for building an interactive dashboard.
The dataset contains information about songs such as title, artist, genre, year, BPM, energy, danceability, loudness, speechiness, popularity, and duration.

The goal of this project is to:

Perform SQL-based data exploration and problem-solving.

Build a Power BI dashboard to visualize insights in an interactive way.

Schema of database
```
CREATE TABLE SPOTIFY(
s_no INT PRIMARY KEY,	
title VARCHAR(300),
artist	VARCHAR(255),
top_genre	VARCHAR (255),
year	INT,
bpm	INT,
nrgy	INT,
dnce	INT,
dB	INT,
live	INT,
val	INT,
dur	INT,
acous INT,
spch	INT,
pop INT
);
```
🛠️ SQL Problem Statements & Queries
```sql
-- BASIC EDA
SELECT * FROM SPOTIFY
```
```
SELECT COUNT(*) FROM SPOTIFY
```
--FIND THE ARTISTS AND THEIR NO OF SONGS IN SPOTIFY
```
SELECT ARTIST,COUNT(*) AS NO_OF_SONG
FROM SPOTIFY
GROUP BY ARTIST
ORDER BY NO_OF_SONG DESC
LIMIT 10
```
```
-- Calculate the average BPM (beats per minute) for all songs in the dataset.
SELECT AVG(BPM) AS AVY_BGM
FROM SPOTIFY
```
--Retrieve all columns for songs released in a specific year Eg:(2021)
```
SELECT * FROM SPOTIFY 
WHERE YEAR = '2019';
```

--Count the number of songs by each artist.
```
SELECT ARTIST,COUNT (*) AS NO_OF_SONG
FROM SPOTIFY
GROUP BY ARTIST
ORDER BY NO_OF_SONG DESC;
```
--Find the maximum and minimum values of the "nrgy" (energy) column.
```
SELECT MAX(NRGY) AS MAX_ENERGY,MIN(NRGY) AS MIN_ENERGY
FROM SPOTIFY
```

--WITH TITLE USING SUB QUERY
```
SELECT TITLE,NRGY
FROM SPOTIFY
WHERE NRGY=(SELECT MAX(NRGY) FROM SPOTIFY)
```

--WITH TITLE USING ODER BY
```
SELECT TITLE,NRGY
FROM SPOTIFY
ORDER BY NRGY ASC
LIMIT 1
```

-- Calculate the total duration of all songs in minutes.
```
SELECT (SUM(DUR)/3600) AS TOTAL_HOUR,
(SUM(DUR)%3600)/60 AS TOTAL_MINUTES
FROM SPOTIFY
```

--total duration of all songs in HOURS AND MINUTES
```
SELECT 
    CONCAT(
        FLOOR(SUM(dur) / 3600), ' hours ',
        FLOOR((SUM(dur) % 3600) / 60), ' minutes') AS total_duration
FROM SPOTIFY;
```

-- Identify the top 5 genres with the highest average danceability (dnce)
```
SELECT TOP_GENRE,AVG(DNCE) AS AVG_DANCE
FROM SPOTIFY
GROUP BY TOP_GENRE
ORDER BY AVG_DANCE DESC
LIMIT 5
```

-- Find the song with the highest energy (nrgy) value for each year.
```
SELECT year, title, nrgy
FROM spotify s
WHERE nrgy = (
    SELECT MAX(nrgy)
    FROM spotify
    WHERE year =  s.year
);
```
--Calculate the median value of the "val" column.
```
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY VAL) AS MEDIAN_VAL
FROM SPOTIFY
```
-- Determine the average speechiness (spch) of songs released in the 2010s.
```
SELECT AVG(SPCH) AS AVG_SPEECHINESS
FROM SPOTIFY
WHERE YEAR BETWEEN 2010 AND 2019
```            
--Identify the artist with the most songs having a BPM greater than 120.
```
SELECT ARTIST, COUNT(*) AS SONG_COUNT,BPM
FROM SPOTIFY
WHERE BPM > 120
GROUP BY ARTIST,BPM
ORDER BY BPM DESC
LIMIT 1
```

--Identify the artist with the most songs having a BPM greater than 120.
```
SELECT ARTIST,COUNT(*) AS SONG_COUNT,AVG(BPM) AS AVG_BPM
FROM SPOTIFY
WHERE BPM > 120
GROUP BY ARTIST
ORDER BY SONG_COUNT DESC
LIMIT 1
```
--Detect any outliers in the "dur" (duration) column using statistical methods.
```
WITH stats AS (
    SELECT 
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY dur) AS q1,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY dur) AS q3
    FROM spotify
),
bounds AS (
    SELECT 
        q1, q3, 
        (q3 - q1) AS iqr,
        (q1 - 1.5 * (q3 - q1)) AS lower_bound,
        (q3 + 1.5 * (q3 - q1)) AS upper_bound
    FROM stats
)
SELECT s.title, s.artist, s.dur
FROM spotify s, bounds b
WHERE s.dur < b.lower_bound OR s.dur > b.upper_bound;
```
-- Identify any correlations between BPM and energy levels across different genre
```
SELECT TOP_GENRE,AVG(BPM) AS AVG_BPM,AVG(NRGY) AS AVG_NRGY
FROM SPOTIFY
GROUP BY TOP_GENRE
ORDER BY AVG_NRGY DESC

--
WITH stats AS (
    SELECT 
        top_genre,
        COUNT(*) AS n,
        SUM(bpm) AS sum_bpm,
        SUM(nrgy) AS sum_nrgy,
        SUM(bpm * nrgy) AS sum_xy,
        SUM(bpm * bpm) AS sum_x2,
        SUM(nrgy * nrgy) AS sum_y2
    FROM spotify
    GROUP BY top_genre
)
SELECT 
    top_genre,
    (n * sum_xy - sum_bpm * sum_nrgy) /
    NULLIF(SQRT((n * sum_x2 - sum_bpm * sum_bpm) * 
                (n * sum_y2 - sum_nrgy * sum_nrgy)), 0) AS correlation
FROM stats;
```
--Determine the trend of speechiness over the years.
```
SELECT YEAR,AVG(SPCH) AS AVG_SPECHINESS
FROM SPOTIFY
GROUP BY YEAR
ORDER BY YEAR,AVG_SPECHINESS ASC
```sql
```
📊 Power BI Dashboard

The dashboard includes:

KPIs: Total songs, Avg BPM, Avg Energy, Total Duration, Top Artist

Line Charts: Trends of speechiness, BPM, popularity over the years

Bar Charts: Top artists by number of songs, songs per genre

Scatter Plot: Energy vs Danceability (bubble size = Popularity)

Histogram: Distribution of song durations

Slicers (Filters): Year, Artist, Genre, BPM range

👉 This allows dynamic filtering and exploration of music trends, like a mini Spotify Wrapped.

🚀 Tech Stack

Database: PostgreSQL

Query Language: SQL

Visualization: Power BI

Version Control: GitHub

📌 Key Insights

Genres like Pop dominate the dataset.

Songs have become slightly more speechy in recent years.

High energy and danceability are strongly correlated with song popularity.

Average BPM stays in the 100–130 range across decades.
```
📂 Repository Structure
├── SQL_Queries.sql        # All problem-solving SQL queries
├── PowerBI_Dashboard.pbix # Power BI dashboard file
├── README.md              # Project documentation
```
