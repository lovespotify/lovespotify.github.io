# Models
As explained in the Playlist Generation page, the goal of our model is to predict the similarity score between two songs. In the sections below, we present the results from using all of the popular models studied in class: Linear Regression (with LASSO/Ridge regularization), Decision Trees, Random Forests, Boosted Trees, Neural Networks).

Our models work with the differences between features for pairs of songs, as shown below, to predict similarity scores which we intend to use to select songs. 

<img src="https://user-images.githubusercontent.com/22016387/70591975-0e7fd080-1ba6-11ea-960f-bccb64912ffe.JPG" width="700">
<img src="https://user-images.githubusercontent.com/22016387/70591976-0f186700-1ba6-11ea-93be-65ff1b8051d3.JPG" width="700">


## Contents
* [Linear Regression](#Linear-Regression)
* [Decision Trees](#Decision-Trees)
* [Random Forests](#Random-Forests)
* [Boosted Trees](#Boosted-Trees)

## Linear Regression
We fit a multiple linear regression model to the training set using the `OLS` function to find a training set R^2 of 0.0051 and testing set R^2 of 0.046. 

<img src="https://user-images.githubusercontent.com/22016387/70593061-b8149100-1ba9-11ea-84a9-97780c652a90.JPG" width="400">
<img src="https://user-images.githubusercontent.com/22016387/70593062-b8ad2780-1ba9-11ea-8b54-fd3b3d005248.JPG" width="400">
<img src="https://user-images.githubusercontent.com/22016387/70593063-b8ad2780-1ba9-11ea-8d7b-377d0b65853f.JPG" width="400">

Interestingly, the R^2 score of testing set is greater than training set, which could be due to bias in the splitting. The top variables with the greatest magnitudes include `danceability`, `duration`, and `time_signature`, around 0.05-0.06, which is very low. Thus, based on the R^2 scores and examination of the coefficients, it appears that there is little correlation between the predictor variables and similarity score. 

In looking at the similarity score distribution, we see many lowly predicted similarity scores which may make choosing songs for our playlist difficult.

<img src="https://user-images.githubusercontent.com/22016387/70593712-12165600-1bac-11ea-8688-4e0d3f1c60b0.JPG" width = "400">
<img src="https://user-images.githubusercontent.com/22016387/70593713-12165600-1bac-11ea-8f6d-3086499e0527.JPG" width = "400">

We then try both Lasso and Ridge regularization and cross-validation with a range of alpha values from 0.1 to 100 to improve predictions and help features selection. We use `RidgeCV` function, yielding training set R^2 of 0.0033 and testing set R^2 of 0.

<img src="https://user-images.githubusercontent.com/22016387/70594576-02e4d780-1baf-11ea-8ddb-6231682a045b.JPG" width = "400">
<img src="https://user-images.githubusercontent.com/22016387/70594577-02e4d780-1baf-11ea-8e5e-eea1b22d427e.JPG" width = "400">

We use `LassoCV` function with 100000 iterations, yielding training set and testing set R^2 of 0.

<img src="https://user-images.githubusercontent.com/22016387/70596626-5e659400-1bb4-11ea-97cf-21262321e7cd.JPG" width = "400">
<img src="https://user-images.githubusercontent.com/22016387/70596625-5dccfd80-1bb4-11ea-9b78-ad2e3e3619a0.JPG" width = "400">

We see constant values in predictions for similarity score, and upon looking at the coefficient values, this observation is consistent because the feature selection ability of LASSO regularization reduced the coefficients for all predictors to 0. Unfortunately, this further suggests that there is minimal relationship between predictor variables and similarity score. 

<img src="https://user-images.githubusercontent.com/22016387/70596560-39712100-1bb4-11ea-9860-1bce0eebba77.JPG" width = "600">

## Decision Trees
We use the `DecisionTreeRegressor` function to construct decision trees of various depth and compare their cross-validation score performance after 5-fold cross-validation. Similar to the Linear Regression results above, we found no correlation between the predictor variables from Spotify API and the similarity score provided by the Million Song Dataset. Our graph demonstrating the changes in accuracy for different decision tree depths is below:
![image](https://user-images.githubusercontent.com/16892763/70590487-da55e100-1ba0-11ea-87e9-01fc7ea33bd5.png)

We can see that while the test accuracy declined and training accuracy increased as expected as we increase the depth of the decision tree, the starting point for the accuracy score is near zero, which indicates that our decision tree model cannot predict the similarity score. 

## Random Forests

The major consideration in fitting a Random Forest model was appropriately tuning the hyperparameters. In previous homeworks, when encountering tree models, we focused on tuning the maximum depth of the tree and selecting the depth from visualizing resulting CV scores. Due to the multiple hyperparameters that we have to tune, we decided to first use random Grid Search and cross validation to narrow down the range for the most optimal combination, and then visualize CV scores for parameters in that range to decide on the final model. Using random Grid Search, we included parameters that controlled for maximum number of trees, depth, features at each split, samples required to split a node, samples required to split a leaf node, and whether we wanted to Bootstrap or not. We created a wide grid that fitted 5 folds for each 100 candidates, totalling 500 fits. The combination that yeilded the highest CV score was the following.

```python
{'bootstrap': False,
 'max_depth': 45,
 'max_features': 'sqrt',
 'min_samples_leaf': 1,
 'min_samples_split': 5,
 'n_estimators': 56}
```

Given that we purposefully used a wide range of inputs random Grid Search, we were motivated to update our parameters given this new information. Using the new information to narrow down the range of optimal parameters for the final model, we fix the parameters for min_samples_leaf, min_samples_split, max_features, and bootstrap, while varying the depth of the tree and the number of trees. Specifically, we test for possible combinations with max_depths = (10,45,60) and n_estimators = range(35,55). 
From examining the above combinations of max depth of tree and number of estimators visually, we are able to eliminate max_depth = 10 with any n_estimators we tested for as the best model. 

<img width="1199" alt="randomforest_cv" src="https://user-images.githubusercontent.com/22043162/70597759-2875df00-1bb7-11ea-856e-db130c656ff1.png">

Running Grid Search over the reamining potential model parameters, we arrive at the following model hyperparameters that gave rise to highest accuracy

```python
{'bootstrap': False,
 'max_depth': 60,
 'max_features': 'sqrt',
 'min_samples_leaf': 2,
 'min_samples_split': 2,
 'n_estimators': 45}
```
 
Finally, running a Random Forest model with the above parameters on the training set gave training accuracy of 0.95 and testing set accuracy of 0.19.


## Boosted Trees

We use boosted decision trees in another ensemble method to attempt to use multiple decision trees in order to improve our model's performance. Essentially how boosting works is by building a model from the training data, and then building a second model to help correct the errors from the first model. We do so by using the `AdaBoostRegressor` function to build upon our `DecisionTreeRegressor` models. With our boosted trees, we have three main parameters that we want to adjust to produce the best results: the `depth` of each decision tree, the number of `estimators` (that is, the number of trees we want to combine), and the `learning rate`.

To find the optimal values for each of these three parameters, we calculate the accuracy on our training dataset for a set of different values. For the depth of the decision tree, we looked at maximum depths of 6, 8, 10, and 12:
<img width="1199" alt="boostdepth" src="https://user-images.githubusercontent.com/58661788/70595184-b4d0d380-1bb0-11ea-848f-3474fc4444bb.png">

For the number of estimators to be employed by `AdaBoost`, we looked at 50, 100, 200, and 300 estimators:
<img width="1199" alt="boostestimators" src="https://user-images.githubusercontent.com/58661788/70595195-be5a3b80-1bb0-11ea-83c4-495ca7e3569e.png">

For the value of the learning rate, we looked at 0.1, 0.3, 0.5, and 1:
<img width="1201" alt="boostlearningrate" src="https://user-images.githubusercontent.com/58661788/70595208-c914d080-1bb0-11ea-93a9-f3a392f5793d.png">

From the previous plots, we determined that the combination that appeared to give the best results for the test data were boosted trees with depth of 10, 200 estimators, and a learning rate of 0.5:
```python
fitted_ada = AdaBoostRegressor(
        base_estimator=DecisionTreeRegressor(max_depth=10),
        n_estimators=200,
        learning_rate=0.5).fit(X_train, y_train)
train_score = list(fitted_ada.staged_score(X_train, y_train))
test_score = list(fitted_ada.staged_score(X_test, y_test))

plt.plot(train_score, label='train')
plt.plot(test_score, label='test')
plt.xlabel("Iteration")
plt.ylabel("Accuracy")
plt.title("Accuracy of AdaBoost as Training Progresses")
plt.legend()
plt.show()
```
<img width="496" alt="boostfinal" src="https://user-images.githubusercontent.com/58661788/70595216-cf0ab180-1bb0-11ea-8aea-9c5f24e1681e.png">

With these values, our results gave us the accuracy values:
```python
print("final training dataset score:", train_score[-1])
print("final test dataset score:", test_score[-1])
```

```python
final training dataset score: 0.9397706363490239
final test dataset score: 0.258275052262645
```
