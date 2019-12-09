# Exploratory Data Analysis

## Feature Description and Distribution
Using the data collected via MillionSongs and the Spotify API, we arrive at a list of features related to the intrinsic audio quality of the song, such as key and acousticness, and features related to non-audio qualities, namely popularity and explicitness. We consider both types of variables as potential features of our final model. Most of the potential features are quantitative variables, with the exeption of explicit, which we convert to a binary variable. The complete list of 

*insert image of song.describe() table here and describe any interesting trends*

## Feature Distribution
It was important for us to analyze feature distributions because we have limited information on how each feature was calculated and scaled. We chose to use box plots to visualize features in order to better understand the spread of our data. We observe that our data tend to have high energy and loudness. Our data also appears to vary the most widely on the feature of acousticness, which reflects the highest spread. We note that there are many outliers for the song speechiness feature, which likely reflects the wide composition of our songs from different genres (ballads versus rap, for example). 

*insert images of boxplots generated here *

## Normalizing
We were motivated to normalize our features given that the duration varaible was recorded in milliseconds, putting it's values on a scale incomparable with other features. 

## Feature Relationship
In carrying out the EDA, we wanted to get a better understanding of the relationship between features (such as energy, speechiness, and popularity) as predictor variables that contribute to predicting a similarity score. The similarity score was chosen as the output variable for prediction by our baseline model because of its relevance to the goal of this project: to choose similar songs for generating a playlist. We decided not to focus on the lyrics/mood component for the model so far because of the time cost to scrape and save information for such a huge dataset. We cleaned the dataframe to convert categorical variables to quantitative values (such as the “explicit” boolean into 0 and 1) and dropped variables with descriptions that wouldn’t directly contribute (such as “track_name,” “artist_name,” and “track_uri”). 
