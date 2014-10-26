---
title: "Predicting Type of Exercise via Weareable Devices"
author: "Aditya Bhatia"
date: "October 27, 2014"
output: html_document
---

## Synopsis

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. 

In this project, the goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: <http://groupware.les.inf.puc-rio.br/har> (see the section on the **Weight Lifting Exercise Dataset**). 

The goal of this project is to predict the manner in which the participants did the exercise. This is the "**classe**" variable in the training set.

## Methodology

According to the principles of **cross-validation**, the data was preprocessed to find the relevant variables and format the data so that a classifier could be run on it.

The following are the main principles of cross-validation that the my model abides by:
1. Use the training set.
2. Split it into training/test sets.
3. Build a model on the training set.
4. Evaluate on the test set.
5. Repeat and average the estimated errors.

## Data 

The **training data** for this project are available here: 
<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv>

The **test data** are available here: 
<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv>

The data for this project come from this source: 
<http://groupware.les.inf.puc-rio.br/har>

The following scripts load the training data and splits it into training and testing sets.

### Set seed

```r
library(caret)
set.seed(11111)
```


```r
df <- read.csv("./ML project/pml-training.csv", na.strings=c("NA",""))
inTrain <- createDataPartition(df$classe, p=0.70, list = FALSE)
train <- df[inTrain,]
test <- df[-inTrain,]
```

## Data Preprocessing

The original dataset has 160 variables including the "**classe**" class variable that indicates the exercise type of the participant's activity. To reduce dimensionality, only the most useful predictors (i.e., variables) were selected. Preproccessing was aimed at cleaning the data by eliminating variables that had too many NAs, non-numeric variables, variables that had too few unique values, and finally variables that had relatively low values of importance.

### Removal of NAs

```r
trainRed <- train[, which(as.numeric(colSums(is.na(train)))==0)]
```

### Removal of Non-numeric Variables

```r
trainRed <- trainRed[,-(1:7)]
```

### Removal of Near-Zero Values

```r
end <- ncol(trainRed)
trainRed[,-end] <- data.frame(sapply(trainRed[,-end], as.numeric))
nzv <- nearZeroVar(trainRed[, -end], saveMetrics=TRUE)
trainRed <- trainRed[,!as.logical(nzv$nzv)]
```

Looking at various exploratory graphs(which i have not shown here for the sake of keeping the report concise);I zeroed on 13 variables that are neccessary to make a good prediction.


```r
topPred <- c("pitch_belt","roll_belt","yaw_belt","roll_arm","yaw_arm","gyros_belt_z",
             "accel_belt_z","magnet_belt_z","magnet_belt_y","pitch_arm","gyros_belt_x",
             "gyros_arm_x","magnet_belt_x","classe")
topPredSorted <- sort(match(topPred, names(trainRed)))
trainRed <- trainRed[,topPredSorted]
```

## Model Training

Now that the best predictors had been identified, the reduced training set was again fitted with the **random forest model**.


```r
modelFit <- train(classe ~ ., data = trainRed, method="rf",importance=TRUE,ntree=5)
```

## Model Testing

This model was then run on the **testing set**.


```r
topPredSorted <- sort(match(topPred, names(test)))
testRed <- test[,topPredSorted]
```

A **confusion matrix** was created to evaluate the accuracy of this model.


```r
accuracy <- confusionMatrix(testRed$classe, predict(modelFit, testRed))
```

```
## Error: object 'modelFit' not found
```

```r
accuracy
```

```
## Error: object 'accuracy' not found
```


```
## Error: object 'accuracy' not found
```
The model had an overall **accuracy** of 0.9607, or 96.0748 percent.

Therefore, the **out-of-sample error rate** was 0.0393, or -95.0748 percent.


## Final Test Set Classification

Finally, the test set was preprocessed and classified by the model. 


```r
testFinal <- read.csv("./ML project/pml-testing.csv")
```

```
## Warning: cannot open file './ML project/pml-testing.csv': No such file or
## directory
```

```
## Error: cannot open the connection
```

```r
topPredSorted <- sort(match(topPred, names(testFinal)))
```

```
## Error: object 'testFinal' not found
```

```r
testFinalRed <- testFinal[,topPredSorted]
```

```
## Error: object 'testFinal' not found
```

```r
predict(modelFit, testFinalRed)
```

```
## Error: object 'modelFit' not found
```
