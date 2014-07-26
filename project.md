Prediction of Weight Lifting Exercises From Accelerometer Data
==============================================

## Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. 

This program analyzes data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. The participants were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). 

The model is then applied to twenty test cases to determine if the type of exercise performed can be determined from the accelerometer data.

## Data preparation

Read both the training and test data.

```r
training <- read.csv("pml-training.csv", header = TRUE, sep = ",")
test <- read.csv("pml-testing.csv", header = TRUE, sep = ",")
```


Remove any zero covariates.

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
nsv <- nearZeroVar(training)
training <- training[-nsv]
test <- test[-nsv]
```


Tidy missing data.

```r
training[is.na(training)] <- 0
test[is.na(test)] <- 0
```


Only use data that is numeric

```r
num_features_idx <- which(lapply(training, class) %in% c("numeric"))
training <- cbind(training$classe, training[, num_features_idx])
test <- test[, num_features_idx]
names(training)[1] <- "classe"
```


## Building the prediction model
A random forest model is built using the numerical variables provided.


```r
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
set.seed(123456)
model <- randomForest(classe ~ ., training)
```


## Training prediction results
The following is the predictions from the training set.  Note the in sample accuracy is 100% which indicates the model does not suffer from bias.


```r
pred_training <- predict(model, training)
confusionMatrix(pred_training, training$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 5580    0    0    0    0
##          B    0 3797    0    0    0
##          C    0    0 3422    0    0
##          D    0    0    0 3216    0
##          E    0    0    0    0 3607
## 
## Overall Statistics
##                                 
##                Accuracy : 1     
##                  95% CI : (1, 1)
##     No Information Rate : 0.284 
##     P-Value [Acc > NIR] : <2e-16
##                                 
##                   Kappa : 1     
##  Mcnemar's Test P-Value : NA    
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             1.000    1.000    1.000    1.000    1.000
## Specificity             1.000    1.000    1.000    1.000    1.000
## Pos Pred Value          1.000    1.000    1.000    1.000    1.000
## Neg Pred Value          1.000    1.000    1.000    1.000    1.000
## Prevalence              0.284    0.194    0.174    0.164    0.184
## Detection Rate          0.284    0.194    0.174    0.164    0.184
## Detection Prevalence    0.284    0.194    0.174    0.164    0.184
## Balanced Accuracy       1.000    1.000    1.000    1.000    1.000
```

```r
print(mean(pred_training == training$classe))
```

```
## [1] 1
```


## Test Prediction Results
Applying this model to the test data provided yields 100% classification accuracy on the twenty test observations.

```r
answers <- predict(model, test)
answers
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```


## Conclusion
Therefore it is possible to provide very good prediction of weight lifting style using accelerometers.

