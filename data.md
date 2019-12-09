[SPOTIFY](https://lovespotify.github.io/) | [DATA COLLECTION](https://lovespotify.github.io/data) | [EXPLORATORY DATA ANALYSIS](https://lovespotify.github.io/eda) | [MODELS](https://lovespotify.github.io/models) | [CONCLUSION](https://lovespotify.github.io/conclusions)

[Data Collection](#Data-Collection)

[Million Song](#Million-Song)

# Data Collection
Our four main sources of data are: Million Playlist dataset, Million Song dataset, Spotify API data, and Lyrics Wiki. In the following sections we will describe our data cleaning procedures.

## Million Song
The Million Song dataset consists of close to a million tracks with tags and similarity scores between two tracks (~56 million track pairs). The format of the dataset is a series of json files, each representing a song and includes similarity information (a list of song IDs that are similar, along with a similarity score) and tags that pertain to the song. We read the information in the series of json files into 1) a similarity dataframe, in which each row represents a track pairing and its similarity score 2) a song dataframe, in which each row represents a unique song from the dataset 3) a tag dictionary, mapping each track Spotify uri to the list of tags given for each song. A challenge we faced when cleaning the Million Song dataset is reconciling the use of Song ID and track uri to identify each track. Since Song ID is unique to the Million Song dataset, we used the Spotify API to find the track uri for each song given the title and artist. Here is the head of our similarity dataframe (`artist_name`, `track_name`, and `track_uri` corresponds to the first song, while `song_id` corresponds to the second):
![image](https://user-images.githubusercontent.com/16892763/70465217-79d67f00-1a8e-11ea-98c9-9a2135f92f7a.png)
Here is the head of our song dataframe:
INSERT SONG DATAFRAME IMAGE
We then scraped the Spotify API (as described in the [Spotify API](#Spotify-API) section) to retrieve audio features (such as danceability, energy, etc.) and track info (such as whether the song has explicit content and how popular it is). Here is the head of our updated song dataframe:
INSERT SONG DATAFRAME WITH SPOTIFY API INFO
We will mainly be using the information gathered for each song in this dataframe as the variable basis for predictions. 
Given the huge size of the Million Song dataset, we decided to use only the test set of the data, but since the train-test split should be entirely random, this would not bias our modeling results. 

## Spotify API (Michelle)
