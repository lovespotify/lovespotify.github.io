

# Playlist Generation
Our approach to the automatic playlist generation problem is as follows: 

1. We train a regression model to predict the similarity score between two songs by using Million Song Dataset similarity scores as the output. Each similarity score is calculated from Last.fm between two songs, and for these two songs, we set as our input the differences in their characteristic measurements. Characteristic measurements include the audio features scraped from the Spotify Web API, including `energy` and `liveliness`.

2. We set a base playlist as a concatenated playlist composed of 50 songs, extracted from the Million Playlist database. Using our trained model, we calculate similarity scores for all of the songs from the Million Song dataset by feeding in the differences between each of the characteristic values for all of the songs in the dataset and the average characteristic values of the base playlist. In other words, our base playlist serves as our point of reference from which we calculate the similarity scores of the rest of the Million Songs dataset. We then find and rank the 50 songs that are most similar to the base playlist based on the similarity scores predicted by our model.

3. We validate our model output by naively calculating the normalized average difference between features each song in our songs dataframe and the features of our base playlist. We find the songs that have the lowest feature differences and check our model recommendations against this naive recommendation system. 

## Playlist 1
