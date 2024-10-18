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

Artists: Contains unique information about the artists.
Albums: Contains album-level data, including the album type and the artist who released it.
Tracks: Contains track-level data, including attributes such as danceability, energy, and tempo.
YouTubeVideos: Contains YouTube performance metrics for each track, such as views, likes, and comments.
StreamingStats: Contains streaming data and platform-specific metrics, such as total streams and which platform the track is most popular on.





