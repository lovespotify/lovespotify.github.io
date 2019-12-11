# Conclusions

As suggested by our R^2 and prediction accuracy scores, our models were not very successful in predicting the similarity scores of song comaprisons. This was most likely because the features we got from Spotify API, while it made intuitive sense to contribute to how similar songs are, were not very relevant to the similarity score that the Million Songs dataset provided. 

Our best-performing model was the AdaBoosted trees, with testing accuracy of 26%. 

We also designed our own similarity metric instead to recommend songs. Our "similarity score" is based on minimizing the difference of each song's Spotify-scraped features and the average value of each feature for all the songs of the base playlist.
