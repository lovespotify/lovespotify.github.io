# Playlist Generation
Our approach to the automatic playlist generation problem is as follows: 

1. First, we trained a model to predict the similarity score between two songs by using Million Song Dataset similarity scores as the output and the difference in characteristic measurements from the two songs as inputs.

2. Then, we inputted a base playlist (a concatenated Million Playlist playlist consisting of 50 songs) and used our model to output similarity scores for all of the songs from the Million Song Dataset by feeding in the difference between characteristic values for each song and the average characteristic values of the base playlist. We then found the 100 songs that are most similar to the base playlist based on the model predicted similarity scores.

3. To take into account user context/information, we then defined the characteristic mixes that correspond to a specific emotion/location/purpose and pick the top 50 songs from the playlist of the most similar songs that have the smallest characteristic differences from the target. For example, if the user context is studying, we may define the characteristic mix to be slow tempo and low danceability.

4. Finally, we want to ensure that we do not just have a playlist that includes many songs from the same artist or from the same album (we want to ensure some variation), so we will limit the number of songs from the same album/artist to 2 in the 100-song playlist by randomly taking out the extra songs and putting the next top-ranked song. 
