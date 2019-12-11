# Models
As explained in the Playlist Generate page, the goal of our model will be to predict the similarity score between two songs. In the sections below, we present the results from using all of the popular models studied in class: Linear Regression (with LASSO/Ridge regularization), Decision Trees, Random Forests, Boosted Trees, Neural Networks).

## Contents
* [Linear Regression](#Linear-Regression)
* [Decision Trees](#Decision-Trees)
* [Random Forests](#Random-Forests)
* [Boosted Trees](#Boosted-Trees)
* [Neural Networks](#Neural-Networks)

## Linear Regression

## Decision Trees
We use the `DecisionTreeRegressor` function to construct decision trees of various depth and compare their cross-validation score performance after 5-fold cross-validation. Similar to the Linear Regression results above, we found no correlation between the predictor variables from Spotify API and the similarity score provided by the Million Song Dataset. Our graph demonstrating the changes in accuracy for different decision tree depths is below:
![image](https://user-images.githubusercontent.com/16892763/70590487-da55e100-1ba0-11ea-87e9-01fc7ea33bd5.png)

We can see that while the test accuracy declined and training accuracy increased as expected as we increase the depth of the decision tree, the starting point for the accuracy score is near zero, which indicates that our decision tree model cannot predict the similarity score. 

## Random Forests

## Boosted Trees

## Neural Networks
