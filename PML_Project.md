# Practical Machine Learning: Weight Lifting Prediction with HAR Dataset
Gerald Cantor  
9/4/2017  


# Introduction
"Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset)."

This assignment uses the above dataset to construct a model used to predict the "classe" variable in the data set, using accelerometers data as the candidate predictors. The final model will be used to predict the "classe" variable of a supplied test data set.

## Loading and preprocessing the data


The training and test data are loaded and both data sets are inspected for any missing values. Columns that have an NA value are removed. In addition, the first 6 columns are removed, since these variables are not representative of accelerometer data. As the result of this initial cleanup, the training set contains 19622 observations of 53 variables and the testing set contains 20 observations of 53 variables

## Building Models and Determining the Best Model
In preparation for building the prediction model, the training data is partitioned into a training subset (60%) and a testing subset (40%). This approach facilitates cross validation, which allows model accuracy to be determined.

```r
set.seed(2112) # For all you Rush fans out there
training_subset <-createDataPartition(clean_training_data$classe, p = 0.6, list=FALSE)
training_subset_data <- clean_training_data[training_subset,]
test_subset_data <- clean_training_data[-training_subset,]
```
Due to the large number of attributes associated with the data set, utilizing feature plots to determine those attributes that would yield an more accurate model is a bit tedious for this assignment. Instead, we will generate two models using different machine learning algorithms and compare the accuracy of the generated models. The test subset of the training data will be utilized to compare the models. The following machine learning algorithms will be used: Gradient Boosting Machine (gbm) and Random Forest (rf). The classe response variable will be predicted using all accelerometer variables.

Now that we have two models available, two predictions are created against the test subset of data. From these two predictions, corresponding confusion matrix data is created. 

```r
pred_via_gbm <- predict(model_via_gbm, newdata=test_subset_data)
pred_via_rf <- predict(model_via_rf, newdata=test_subset_data, type="class")
gbm_cm <- confusionMatrix(pred_via_gbm, test_subset_data$classe)
rf_cm <- confusionMatrix(pred_via_rf, test_subset_data$classe)
overall.gbm.accuracy <- gbm_cm$overall['Accuracy']
overall.gbm.oose <- (1 - overall.gbm.accuracy)
overall.gbm.accuracy.pct <- overall.gbm.accuracy * 100
overall.gbm.oose.pct <- overall.gbm.oose * 100
overall.rf.accuracy <- rf_cm$overall['Accuracy']
overall.rf.oose <- (1 - overall.rf.accuracy)
overall.rf.accuracy.pct <- overall.rf.accuracy * 100
overall.rf.oose.pct <- overall.rf.oose * 100
```
The following table illustrates the accuracy percentage and out-of-sample error percentage for each model:

| Model | Accuracy | Out-of-sample error |
|--|-----------|--------------|
|GBM | 96.2401224 | 3.7598776 |
|Random Forest | 99.3882233 | 0.6117767  |

As the table illustrates, the model generated using the Random Forest machine learning algorithm has a higher accuracy and lower out-of-sample error. Therefore, this model will be used to predict the "classe" variable of the test data set.

## Final Prediction on Test Data
Using the Random Forest model, perform prediction on the test data.

```r
pred_on_test <- predict(model_via_rf, newdata=clean_test_data, type="class")
print(pred_on_test)
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```
