# Data Collection
Our four main sources of data are: Million Playlist dataset, Million Song dataset, Spotify API data, and Lyrics Wiki. In the following sections we will describe our data collection and cleaning procedures.

## Contents
* [Million Song Dataset](#Million-Song-Dataset)
* [Million Playlist Dataset](#Million-Playlist-Dataset)
* [Spotify API](#Spotify-API)

## Million Song Dataset
The [Million Song dataset](http://millionsongdataset.com/lastfm/) consists of close to a million tracks with tags and similarity scores between two tracks (~56 million track pairs). The format of the dataset is a series of json files, each representing a song and includes similarity information (a list of song IDs for songs that are similar to each other, along with a similarity score of the two songs between 0 and 1, 0 being least similar and 1 most similar) and tags that pertain to the song. Both the tags and the similiarity scores were retrieved from the Last.fm API.

We read the information in the series of json files into
1. a similarity dataframe, in which each row represents a track pairing and its similarity score
2. a song dataframe, in which each row represents a unique song from the dataset
3. a tag dictionary, mapping each track Spotify uri to the list of tags given for each song.

A challenge we faced when cleaning the Million Song dataset is reconciling the use of Song ID and Spotify track uri to identify each track. Since Song ID is unique to the Million Song dataset, we used the Spotify API to find the track uri for each song given the title and artist.

Our similarity dataframe looks like this (`artist_name`, `track_name`, and `track_uri` corresponds to the first song, while `song_id` corresponds to the second):
```python
similarity.head()
```
![image](https://user-images.githubusercontent.com/16892763/70465217-79d67f00-1a8e-11ea-98c9-9a2135f92f7a.png)

Our `songs` dataframe looks like this:
```python
songs.head()
```
![image](https://user-images.githubusercontent.com/16892763/70538947-ee61fa00-1b30-11ea-9f87-a6993ffdbc1d.png)

We then scrape the Spotify API (as described in the [Spotify API](#Spotify-API) section) to retrieve audio features (such as danceability, energy, etc.) and track info (such as whether the song has explicit content and how popular it is). Here is the head of our updated `songs` dataframe:
```python
songs.head()
```
![image](https://user-images.githubusercontent.com/16892763/70545162-2706d100-1b3b-11ea-8299-b178faf794be.png)
![image](https://user-images.githubusercontent.com/16892763/70545179-2a9a5800-1b3b-11ea-83e5-3c4ddb5d2606.png)

We mainly use the information gathered for each song in this dataframe as the variable basis for predictions. 
Given the huge size of the Million Song dataset, we use only the test set of the data, but since the train-test split should be entirely random, this would not bias our modeling results. 

## Million Playlist Dataset
Spotify currently hosts over 2 billion playlists. The [Million Playlist Dataset](https://recsys-challenge.spotify.com) consists of an immense database of one million playlists that have been created by Spotify users and the songs that are in each one. The database consists of a set of csv files, which we read into one consolidated dataframe. Here is the head of the `mplaylists` dataframe:

INSERT MPLAYLISTS DATAFRAME IMAGE

We ultimately decided not to use the Million Playlist data for our model because of the difficulty in consolidating the data from the Million Playlist dataset and the Million Song dataset. In particular, the songs found in the two datasets would often not overlap: in a test run with 1000 songs from the Million Song dataset, only 2 were found in the Million Playlist dataset. Further, our final strategy for our model revolved around similarity scores between songs, which are provided in the Million Songs dataset and not in the Million Playlist dataset.

## Spotify API

The Spotify Web API offers an extensive amount of data in the form of JSON metadata about music artists, albums, and tracks, directly from the Spotify Data Catalogue. Data resources in the API are accessed via standard HTTPS requests in UTF-8 format to an API endpoint. To simplify requests to the API, we use the `spotipy` library with allows us full access to all of the music data provided by the Spotify platform.

```python
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials

# create spotify api access
sp = spotipy.Spotify(client_credentials_manager=client_credentials_manager)
```

The key to accessing data in the Spotify Web API is the Spotify URIs, which are resource identifiers entered, for example, in the Spotify Desktop client’s search box to locate an artist, album, or track. However, the Spotify Web API URIs are entirely different from the song IDs provided for each track in the Million Song Dataset, so one major step in being able to combine our data was to properly associate each song from the Million Song Dataset to the corresponding data that we can retrieve from the Spotify API.

To do so, we search the Spotify API for the desired track given the song and artist name from the Million Song Dataset. Each song may have different versions, or tracks, in Spotify (e.g. a live version, acoustic version, or instrumental version), so we choose the first song from the search results. Then we can extract the corresponding track URI for that song, and connect the Spotify URI with the Song ID given in the Million Song Dataset. This process allows us to combine all of our data for each song in one place. We would occasionally encounter problems with making too many requests in a certain time period, causing errors, so we added pauses to our code to avoid this.

```python
def finduri(song, artist):
    # search spotify for the song and artist
    song_list = sp.search(song + " " + artist)
    if song_list['tracks']['items'] == []:
        return []
        
    # extract the info from the first song in list
    song = song_list['tracks']['items'][0]
    time.sleep(0.1)
    
    # return the uri
    return song['uri']
```

The Spotify Web API provides a dizzying amount of data for each track in the form of audio features and audio analysis. The audio analysis describes the track’s structure and musical content, including rhythm, pitch, and timbre. This data is very interesting, but is less directly applicable to our purpose, so we do not incorporate this data into our model. On the other hand, the various audio features provided by the API are more diverse and more directly applicable to our model. A table of short descriptions for each of the audio features we studied is given below.

Audio Feature | Description
------------ | -------------
`key` | The estimated overall key of the track.
`modality` | The modality (major or minor) of a track, the type of scale from which its melodic content is derived
`time_signature` | An estimated overall time signature of a track
`acousticness` | A confidence measure from 0.0 to 1.0 of whether the track is acoustic
`danceability` | How suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity
`energy` | A perceptual measure of intensity and activity, including dynamic range, perceived loudness, timbre, onset rate, and general entropy
`instrumentalness` | Predicts whether a track contains no vocals
`liveness` | Detects the presence of an audience in the recording
`loudness` | The overall loudness of a track in decibels (dB)
`speechiness` | Detects the presence of spoken words in a track
`valence` | A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track
`tempo` | The overall estimated tempo of a track in beats per minute (BPM)

We also use the API to retrieve information about the track, such as the album name and the Spotify URI for the artist, for later use. To help with our song filtering for deciding our playlists later on, we also retrieve `explicit` and `popularity` from the track info provided in the API, to see whether the track is child-friendly or how popular the track is.

```python
# extract info on each track from the songs dataframe
audiofeatures = ['danceability', 'energy', 'key', 'loudness', 'mode', 'speechiness', 'acousticness', 'instrumentalness', 'liveness', 'valence', 'tempo', 'duration_ms', 'time_signature']
trackinfos = ['explicit', 'popularity']
```

We create a temporary `numpy` array to which we would add all of the audio features and additional information extracted from the Spotify API.

```python
# temp numpy array
songinfo = list(songs.values)
```

```python
count = 0
for track in songs['track_uri']:
    
    # retrieve audio and track info
    trackaudio = sp.audio_features(track)
    trackinfo = sp.track(track)
    
    # add album name and artist uri to song dataframe
    songinfo[count] = np.append(songinfo[count],trackinfo['album']['release_date'])
    
    # add audio features and track info to song dataframe
    for feature in audiofeatures:
        songinfo[count] = np.append(songinfo[count],trackaudio[0][feature])
    for info in trackinfos:
        songinfo[count] = np.append(songinfo[count],trackinfo[info])
    time.sleep(0.1)
    count += 1
```

We then reconvert our array back into our `songs` dataframe for easier access:

```python
new_cols = np.append(songs.columns.values,['release_date']+audiofeatures+trackinfos)
songs = pd.DataFrame(data=songinfo, columns=new_cols)
```

This gives us our new `songs` dataframe:

```python
songs.head()
```

### Sources
* **Million Song Dataset:** Thierry Bertin-Mahieux, Daniel P.W. Ellis, Brian Whitman, and Paul Lamere. 
The Million Song Dataset. In Proceedings of the 12th International Society
for Music Information Retrieval Conference (ISMIR 2011), 2011.
