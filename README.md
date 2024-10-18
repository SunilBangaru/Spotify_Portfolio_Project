# Spotify Data Analysis Using SQL

![Spotify Logo](https://github.com/SunilBangaru/Spotify_Portfolio_Project/blob/main/Spotify_Logo.PNG)

## Overview and Objective
The objective of this project is to draw insights based on the data available from Spotify and YouTube regarding artists, albums/tracks & some metrics through performing various SQL queries. The database consists of details such as track aspects (energy, danceability), streaming data, and YouTube video stats (collaboration views enjoyed comments). Here is an overview of how to write SQL queries and approaches at each complexity level for the goals in projects described earlier.

I have categorized it by three different levels of SQL query difficulty inside the project:

Beginner: Basic joins, some filters, and aggregations functions.

Intermediate: Complex queries on multiple tables and conditions.

Advanced: Analytical queries, e.g., performance metrics or rankings and multi-level aggregations.

## Entity Relationship Diagram (ERD)

![ERD](https://github.com/SunilBangaru/Spotify_Portfolio_Project/blob/main/ERD_Spotify_Final.png)

## Dataset

[Reference Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

The dataset consists of Spotify streams, track features (energy, danceability, temp etc), YouTube views/likes/comments, and performance on different platforms for each track.

[Updated_Dataset](https://github.com/SunilBangaru/Spotify_Portfolio_Project/blob/main/Final_Spotify_Dataset.xlsx)

The dataset has been divided into five relational tables to allow for efficient querying and normalization:

Below is the overview of the dataset which has been updated from reference dataset:

 - Artists: Contains unique information about the artists.
 - Albums: Contains album-level data, including the album type and the artist who released it.
 - Tracks: Contains track-level data, including attributes such as danceability, energy, and tempo.
 - YouTubeVideos: Contains YouTube performance metrics for each track, such as views, likes, and comments.
 - StreamingStats: Contains streaming data and platform-specific metrics, such as total streams and which platform the track is most popular on.

## Database Schema

The project uses five main tables:

**Artists**: Contains information about music artists.
 - `ArtistID`: Unique identifier for each artist.
 - `ArtistName`: Name of the artist.
 - `Albums`: Holds information about music albums.

**AlbumID**: Unique identifier for each album.
 - `ArtistID`: References the Artists table.
 - `AlbumName`: Name of the album.
 - `AlbumType`: Type of the album (e.g., album, single).
   
**Tracks**: Details about individual tracks.
 - `TrackID`: Unique identifier for each track.
 - `AlbumID`: References the Albums table.
 - `TrackName`: Name of the track.
 - `Danceability`: A measure of how suitable the track is for dancing.
 - `Energy`: Energy level of the track.
 - `Loudness`:The overall loudness of the track (in dB).
 - `Speechiness`:A measure of the presence of spoken words in the track.
 - `Acousticness`: The likelihood that the track is acoustic.
 - `Instrumentalness`: Whether the track contains no vocals.
 - `Liveness`: A measure of how "live" the track sounds.
 - `Valence`: The musical positiveness of the track.
 - `Tempo`: The tempo of the track (in BPM).
 - `DurationMin`: Duration of the track (in minutes).

**YouTubeVideos**: Contains information about YouTube videos associated with tracks.
- `VideoID`: Unique identifier for each video.
- `TrackID`: References the Tracks table.
- `Title`: Title of the YouTube video.
- `Channel`: The YouTube channel that published the video.
- `Views`: Number of views the video has.
- `Likes`: Number of likes the video has.
- `Comments`: Number of comments on the video.

**StreamingStats**: Stores streaming statistics for tracks.
 - `StreamID`: Unique identifier for each streaming record.
 - `TrackID`: References the Tracks table.
 - `Streams`: The total number of streams the track has on the respective platform.
 - `MostPlayedOn`: The platform where the track is most played (e.g., Spotify, YouTube).

## SQL Schema

```sql
DROP TABLE IF EXISTS Artists
CREATE TABLE Artists (
    ArtistID NUMBER PRIMARY KEY,
    ArtistName VARCHAR2(255) NOT NULL
);

DROP TABLE IF EXISTS Albums
CREATE TABLE Albums (
    AlbumID NUMBER PRIMARY KEY,
    ArtistID NUMBER,
    AlbumName VARCHAR2(255) NOT NULL,
    AlbumType VARCHAR2(50),
    FOREIGN KEY (ArtistID) REFERENCES Artists(ArtistID)
);

DROP TABLE IF EXISTS Tracks
CREATE TABLE Tracks (
    TrackID NUMBER PRIMARY KEY,
    AlbumID NUMBER,
    TrackName VARCHAR2(255) NOT NULL,
    Danceability NUMBER,
    Energy NUMBER,
    Loudness NUMBER,
    Speechiness NUMBER,
    Acousticness NUMBER,
    Instrumentalness NUMBER,
    Liveness NUMBER,
    Valence NUMBER,
    Tempo NUMBER,
    DurationMin NUMBER,
    FOREIGN KEY (AlbumID) REFERENCES Albums(AlbumID)
);

DROP TABLE IF EXISTS YoutubeVideos
CREATE TABLE YouTubeVideos (
    VideoID NUMBER PRIMARY KEY,
    TrackID NUMBER,
    Title VARCHAR2(255),
    Channel VARCHAR2(255),
    Views NUMBER,
    Likes NUMBER,
    Comments NUMBER,
    FOREIGN KEY (TrackID) REFERENCES Tracks(TrackID)
);

DROP TABLE IF EXISTS StreamingStats
CREATE TABLE StreamingStats (
    StreamID NUMBER PRIMARY KEY,
    TrackID NUMBER,
    Streams NUMBER,
    MostPlayedOn VARCHAR2(50),
    FOREIGN KEY (TrackID) REFERENCES Tracks(TrackID)
);
```

## Business Problems and Solutions

### 1, List all albums along with their respective artist names.

```sql
SELECT DISTINCT AlbumName, ArtistName
FROM Albums A
JOIN Artists AR ON A.ArtistID = AR.ArtistID;
```

### 2, Get the total views for each track's YouTube video.

```sql
SELECT DISTINCT T.TrackName, SUM(YT.Views) AS TotalViews
FROM Tracks T
JOIN YouTubeVideos YT ON T.TrackID =YT.TrackID
GROUP BY T.TrackName;
```

### 3, Find the average duration of tracks(Round the decimals to 2).
```sql
SELECT ROUND(AVG(DurationMin),2) AS AvgDuration
FROM Tracks;
```
### 4, List all top official music videos along with their views.
```sql
SELECT DISTINCT Title, Views
FROM YouTubeVideos
WHERE Views IS NOT NULL
ORDER BY Views DESC;
```
### 5, Find the top tracks with the highest total number of streams, excluding tracks with zero or missing streams.
```sql
SELECT DISTINCT T.TrackName, SUM(S.Streams) AS TotalStreams
FROM Tracks T
JOIN StreamingStats S ON T.TrackID = S.TrackID
WHERE Streams > 0
GROUP BY T.TrackName
ORDER BY TotalStreams DESC;
```

### 6, Find the top 5 tracks with the highest number of streams.
```sql
SELECT DISTINCT T.TrackName, S.Streams
FROM Tracks T
JOIN StreamingStats S ON T.TrackID = S.TrackID
ORDER BY S.Streams DESC
FETCH FIRST 5 ROWS ONLY;
```
### 7, Get the total likes and comments for each artist's YouTube videos.
```sql
SELECT DISTINCT AR.ArtistName, SUM(YT.Likes) AS TotalLikes, SUM(YT.Comments) AS TotalComments
FROM Artists AR
JOIN Albums AL ON AR.ArtistID = AL.ArtistID
JOIN Tracks T ON AL.AlbumID = T.AlbumID
JOIN YouTubeVideos YT ON T.TrackID = YT.TrackID
GROUP BY AR.ArtistName;
```
### 8, Find the most popular track (based on streams and views).
```sql
SELECT DISTINCT T.TrackName, SUM(S.Streams) + SUM(YT.Views) AS Popularity
FROM Tracks T
JOIN YouTubeVideos YT ON T.TrackID = YT.TrackID
JOIN StreamingStats S ON T.TrackID = S.TrackID
GROUP BY T.TrackName
ORDER BY Popularity DESC
FETCH FIRST 1 ROWS ONLY;
```
### 9, Get all tracks that belong to a specific genre of albums (e.g., "album" type).
```sql
SELECT DISTINCT T.TrackName
FROM Tracks T
JOIN Albums A ON T.AlbumID = A.AlbumID
WHERE A.AlbumType = 'album';
```
### 10, Find the artist with the highest total streams across all albums.
```sql
SELECT DISTINCT AR.ArtistName, SUM(S.Streams) AS TotalStreams
FROM Artists AR
JOIN Albums AL ON AR.ArtistID = AL.ArtistID
JOIN Tracks T ON AL.AlbumID = T.AlbumID
JOIN StreamingStats S ON T.TrackID = S.TrackID
GROUP BY AR.ArtistName
ORDER BY TotalStreams DESC
FETCH FIRST 1 ROWS ONLY;
```
### 11, Compare YouTube views to Spotify streams for all tracks.
```sql
SELECT DISTINCT T.TrackName, YT.Views, S.Streams
FROM Tracks T
JOIN YouTubeVideos YT ON T.TrackID = YT.TrackID
JOIN StreamingStats S ON T.TrackID = S.TrackID
```
### 12, Find the track with the highest "Energy" value.
```sql
SELECT DISTINCT TrackName, Energy
FROM Tracks
WHERE Energy = (SELECT MAX(Energy) 
                FROM Tracks);
```
### 13, Identify the artist whose tracks have the highest average energy.
```sql
SELECT AR.ArtistName, AVG(T.Energy) AS AvgEnergy
FROM Artists AR
JOIN Albums AL ON AR.ArtistID = AL.ArtistID
JOIN Tracks T ON AL.AlbumID = T.AlbumID
GROUP BY AR.ArtistName
ORDER BY AvgEnergy DESC
FETCH FIRST 1 ROWS ONLY;
```
### 14, Get the distribution of track danceability across albums.
```sql
SELECT DISTINCT A.AlbumName, AVG(T.Danceability) AS AvgDanceability
FROM Albums A
JOIN Tracks T ON A.AlbumID = T.AlbumID
GROUP BY A.AlbumName;
```
### 15, Find which track is played most on YouTube compared to Spotify.
```sql
SELECT DISTINCT T.TrackName, S.MostPlayedOn
FROM StreamingStats S
JOIN Tracks T ON S.TrackID = T.TrackID
WHERE S.MostPlayedOn = 'Youtube';
```

### Conclusion

The project provides a detailed analysis of the Spotify dataset by embedding SQL queries with gradually increasing complexity in order to derive more relevant information. We have analyzed track features, streaming data, and YouTube performance metrics, offering insights into music industry trends and track performance.

