# Models
As explained in the Playlist Generate page, the goal of our model is to predict the similarity score between two songs. In the sections below, we present the results from using all of the popular models studied in class: Linear Regression (with LASSO/Ridge regularization), Decision Trees, Random Forests, Boosted Trees, Neural Networks).

Our models work with the differences between features for pairs of songs, as shown below, to predict similarity scores which we intend to use to select songs. 

<img src="https://user-images.githubusercontent.com/22016387/70591975-0e7fd080-1ba6-11ea-960f-bccb64912ffe.JPG" width="700">
<img src="https://user-images.githubusercontent.com/22016387/70591976-0f186700-1ba6-11ea-93be-65ff1b8051d3.JPG" width="700">


## Contents
* [Linear Regression](#Linear-Regression)
* [Decision Trees](#Decision-Trees)
* [Random Forests](#Random-Forests)
* [Boosted Trees](#Boosted-Trees)
* [Neural Networks](#Neural-Networks)

## Linear Regression
We fit a multiple linear regression model to the training set using the `OLS` function to find a training set R^2 of 0.0051 and testing set R^2 of 0.046. 

<img src="https://user-images.githubusercontent.com/22016387/70593061-b8149100-1ba9-11ea-84a9-97780c652a90.JPG" width="500">
<img src="https://user-images.githubusercontent.com/22016387/70593062-b8ad2780-1ba9-11ea-8b54-fd3b3d005248.JPG" width="500">
<img src="https://user-images.githubusercontent.com/22016387/70593063-b8ad2780-1ba9-11ea-8d7b-377d0b65853f.JPG" width="500">

Interestingly, the R^2 score of testing set is greater than training set, which could be due to bias in the splitting. The top variables with the greatest magnitudes include "danceability," "duration," and "time signature," around 0.05-0.06, which is very low. Thus, based on the R^2 scores and examination of the coefficients, it appears that there is little correlation between the predictor variables and similarity score. 

In looking at the similarity score distribution, we see many lowly predicted similarity scores which may make choosing songs for our playlist difficult.

<img src="https://user-images.githubusercontent.com/22016387/70593712-12165600-1bac-11ea-8688-4e0d3f1c60b0.JPG" width = "500">
<img src="https://user-images.githubusercontent.com/22016387/70593713-12165600-1bac-11ea-8f6d-3086499e0527.JPG" width = "500">

We then try both Lasso and Ridge regularization and cross-validation with a range of alpha values from 0.1 to 100 to improve predictions and help features selection. We use `RidgeCV` function, yielding training set R^2 of 0.0033 and testing set R^2 of 0.

<img src="https://user-images.githubusercontent.com/22016387/70594576-02e4d780-1baf-11ea-8ddb-6231682a045b.JPG" width = "500">
<img src="https://user-images.githubusercontent.com/22016387/70594577-02e4d780-1baf-11ea-8e5e-eea1b22d427e.JPG" width = "500">

We use `LassoCV` function with , yielding training set and testing set R^2 of 0.



## Decision Trees
We use the `DecisionTreeRegressor` function to construct decision trees of various depth and compare their cross-validation score performance after 5-fold cross-validation. Similar to the Linear Regression results above, we found no correlation between the predictor variables from Spotify API and the similarity score provided by the Million Song Dataset. Our graph demonstrating the changes in accuracy for different decision tree depths is below:
![image](https://user-images.githubusercontent.com/16892763/70590487-da55e100-1ba0-11ea-87e9-01fc7ea33bd5.png)

We can see that while the test accuracy declined and training accuracy increased as expected as we increase the depth of the decision tree, the starting point for the accuracy score is near zero, which indicates that our decision tree model cannot predict the similarity score. 

## Random Forests

## Boosted Trees

## Neural Networks
