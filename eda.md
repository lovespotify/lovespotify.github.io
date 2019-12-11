# Exploratory Data Analysis

## Feature Description and Distribution
Using the data collected via MillionSongs, we arrive at a list of features related to the intrinsic audio quality of the song, such as "key" and "acousticness," and features related to non-audio qualities, namely "popularity" and "explicitness." We consider both types of variables as potential features of our final model. Most of the potential features are quantitative variables, with the exception of "explicit," which we converted from True/False to binary values during data cleaning. The basic statistics of the songs show different ranges of values for each feature, as examined in the next section. 

![songs-describe1](https://user-images.githubusercontent.com/22016387/70591835-81d51280-1ba5-11ea-8486-14fefe0da527.JPG)
![songs-describe2](https://user-images.githubusercontent.com/22016387/70591836-81d51280-1ba5-11ea-8ccb-3b37f5c46ba1.JPG)

## Feature Distribution
It is important for us to analyze feature distributions because we have limited information on how each feature was calculated and scaled for each song. We choose to use box plots to visualize features in order to better understand the spread of our data. We find that the majority of songs were in top 20 for popularity, which would suggest a more "pop"-based set of songs that would be more likely to be found during Spotify API scraping. The tempos of songs range from 100 to 140, which seem to be on the faster end of songs given units of "beats per minute." We note that there are many outliers for the song speechiness feature, which likely reflects the wide composition of our songs from different genres (ballads versus rap, for example). Some other interesting findings are the seemingly low scores for liveliness (which would intuitively oppose the finding of higher tempos) but higher levels of energy and general majority of non-explicit songs. Unfortunately, it is sometimes a bit difficult to interpret the values as there were no units provided in the MillionSongs dataset; for example, we would assume the key value would indicate the key signature, or major or minor, of the song, but that has a wider range of possibilites than the discrete 1 to 10 values as indicated in the dataset. 

![unnormalized_features2](https://user-images.githubusercontent.com/22016387/70591238-71239d00-1ba3-11ea-8912-277480d32151.JPG)

![unnormalized_features1](https://user-images.githubusercontent.com/22016387/70591261-826ca980-1ba3-11ea-93ba-aae7d0822908.JPG)

## Normalizing
We are motivated to normalize our features given a few variables that had very different values than others, including "duration" which appear to be recorded in milliseconds to result in values in 6th order of magnitude, as well as "loudness" which had negative values and "tempo" which were in the hundreds. Normalizing puts all values on a scale that is comparable to other features. Upon normalization, we observe that our data tend toward having high values for energy and loudness. Our data also appears to vary the most widely on acousticness and key, which reflects the highest spread. 

![all_boxplot](https://user-images.githubusercontent.com/22016387/70591324-c52e8180-1ba3-11ea-90d4-7fde1ab2c4a7.png)

## Feature Relationship
In carrying out the EDA, we want to get a better understanding of the relationship between features (such as energy, speechiness, and popularity) as predictor variables that contribute to predicting a similarity score. The similarity score, a value provided by the original dataset, was chosen as the output variable for prediction by our baseline model because of its relevance to the goal of this project: to choose similar songs for generating a playlist. We decided not to focus on the lyrics/mood component for the model because of the time cost to scrape and save information for such a huge dataset. We cleaned the dataframe to convert categorical variables to quantitative values (such as the “explicit” boolean into 0 and 1) and dropped variables with descriptions that wouldn’t directly contribute (such as “track_name,” “artist_name,” and “track_uri”). 

First, we graph the values of each predictor variable with each other, creating a scatter matrix. This would allow us to visualize the inter-dependencies among the list of predictors.

![scattermatrix](https://user-images.githubusercontent.com/22016387/70591243-741e8d80-1ba3-11ea-98e8-1a145e5e388e.png)

We find that there is generally random scatter between variables, suggesting little correlation. The lack of correlation means each variable should have independent contributions to predicting outputs. Exceptions to this seems to appear in positive correlation between "loudness" and "energy," which we are able to address with regularization in our models. For variable intersections where there is less randomness, we see that those variables have discrete values, such as “key,” “time signature,” “mode,” and “explicit,” which still have points in general uniform distributions across the other axis. 

We were initially interested in only examining entries with a high similarity score (e.g. above 0.9). However, an important finding of the EDA revealed that the distribution of similarity scores is heavily right-skewed, with a low proportion of songs with high similarity scores to each other. This observation, in conjunction with the fact that the sample size for our initial EDA and for the baseline model will be small, as explained above, there would be very few samples that yield a high similarity score. In fact, if we were to randomly select a sample of 1000 songs, only three entries in the similarity data frame correspond to similarity scores over 0.5 for any of the 1000 songs. 

![similarity_hist](https://user-images.githubusercontent.com/22016387/70591266-85679a00-1ba3-11ea-9a54-4e3e7addbf58.png)

Given this EDA finding, we instead decided to examine all similarity scores and formulate a model that would be trained to predict similarity scores. One potential drawback we were concerned about in this approach is that later when we add songs, we have to evaluate a similarity score for every song and keep the songs that yield a high similarity score. This may become very costly given we have to compute a score for every song. A more efficient solution would be generating cutoffs for each feature and selecting songs that match all cutoffs, and we may be able to accomplish this during Milestone 4 when we work with more samples.

