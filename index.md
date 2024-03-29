## Team 72: Michelle Chen, Rita Cui, Cathy Wang, Allison Zhang

CS 109a Fall 2019

![streaming](https://user-images.githubusercontent.com/58661788/70466232-ae4b3a80-1a90-11ea-807f-1a5ed4126462.png)

### Problem Description and Motivation

With the explosive rise in digital music distribution, people all over the world now have access to a nearly unlimited collection of music on an unprecedented scale. Spotify offers over 50 million songs to almost 250 million active monthly users, and Apple Music provides a library of over 60 million songs for over 60 million subscribers around the world. On-demand audio streams per year now numbers in the hundreds of billions, and now accounts for over half of total music consumption, surpassing all other ownership formats, including physical and digital album sales. With millions of songs at their fingertips, users can often feel overwhelmed by all the music that they could listen to with a single click. Thus, an efficient and effective music recommender system is necessary for the benefit of both the music service providers and the listeners.

We focus specifically on playlist generation, one of Spotify's primary methods for music recommendation. The essence of playlist generation is about finding a set of songs to recommend to the user to best enhance and extend the listener's experience. Playlists are extremely flexible and endlessly customizable, built for every mood or event. In Spotify-curated playlists, the providers attempts to find and offer the most relevant songs to the users to match their preferences, and playlists are the simplest and most direct way to communicate these recommendations.

Spotify creates and curates their playlists in a complex manner, which involves both human-led and computer-led processes. Our goal is to create a model for song discovery that compiles playlists almost entirely algorithmically. The discovery process begins with a base playlist of 50 songs that the user has listened to and we assume has enjoyed. Then the user would provide some context information that they would prefer to incorporate into the generated playlist--perhaps they want to feel energetic, or just want to study--and the generated playlist would take into account all of these factors. We hope that through this model, we can generate playlists that we ourselves will enjoy.

### Data Source Description

We retrieve our data from three different sources: the Million Songs dataset, Million Playlists dataset, and Spotify Web API. Ultimately, our model is trained on the songs from the Million Songs dataset, as well as characteristics of the songs scraped from the Spotify Web API. More details on how the data was extracted, cleaned, and organized is given in [Data Collection](https://lovespotify.github.io/data).

### Models

Our playlist generation involves training a regression model to calculate similarity scores between all of the songs in our dataset and the songs that we provide in our base playlist. We look at four different models: linear regression model (and LASSO-/Ridge regularized models), decision tree, random forest, and boosted tree. More details on how each model was implemented and the resulting output accuracy is provided in [Models](https://lovespotify.github.io/models).

### Conclusions

We found that tree-based models generally performed the best, with highest testing accuracy given by AdaBoosted trees, followed by our Random Forest model. After examining the performance of our models, we were motivated to create our own similarity score metric to allow for more intuition in how features contribute similarity scores. Under the playlist generation section, we put our models to use and generated recommendations based on real-life playlists found in the Million Playlist Dataset. Moving forward, we would be interested in developing a more rigorous understanding of how features in our dataset are calculated and in incorporating lyrics sentiment analysis into our models.

### Sources

* https://techcrunch.com/2018/01/04/on-demand-streaming-now-accounts-for-the-majority-of-audio-consumption-says-nielsen/
* https://cse.iitk.ac.in/users/cs365/2014/_submissions/shefalig/project/proposal.pdf
