
# Introduction

This report is prepared as one of the requirement in Practical Machine Learning online course by Johns Hopkins University. The basic goal of this assignment is to predict the manner of the subject (6 participants) performed some exercise. For this assignment, in order to predict the manner of the subject did the exercise decision tree and random forest method will be performed to determine the best prediction. The best prediction is determined by the highest accuracy.

# Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har 

# Data
The training data for this project are available here:https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
The test data are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

The data for this project come from this source: http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har. If you use the document you create for this class for any purpose please cite them as they have been very generous in allowing their data to be used for this kind of assignment.


# Pre-Processing
This segment includes packages required and uploading data into R.
Packages needed to perform bagging, random forest and boosting are as follows:

# Load Libraries
```{r echo=TRUE}
 library(e1071)
 library('caret')
# Loading rquired package: lattice and ggplot2

 library(rpart)
 library(rpart.plot)
 library(ggplot2)
 library(randomForest)
library(corrplot)

```

# Download File

```{r echo=TRUE}
 
#locate downloaded dataset
 testing<-read.csv(file="pml-testing.csv",head=TRUE,sep=",")
 training<-read.csv(file="pml-training.csv",head=TRUE,sep=",")
 
```
 # Data processing and Data Cleaning
 
```{r echo=TRUE}
 # We need to define the same columns
# create a partition using caret with the training dataset on 70,30 ratio
sameColumsName <- colnames(training) == colnames(testing)
colnames(training)[sameColumsName==FALSE]

# Data Cleaning
training<-training[,colSums(is.na(training)) == 0]
testing <-testing[,colSums(is.na(testing)) == 0]

```
# Checking the column names of traning dataset

```{r echo=TRUE}
 head(colnames(training))
#The first 7 variables of the training data were deleted, 
training <- training[,8:dim(training)[2]]
testing <- testing[,8:dim(testing)[2]]

```
#Training, testing & validation data

```{r echo=TRUE} 
set.seed(123)
Seeddata1 <- createDataPartition(y = training$classe, p = 0.8, list = FALSE)
Seeddata2 <- training[Seeddata1,]
validation <- training[-Seeddata1,]
Training_data1 <- createDataPartition(y = Seeddata2$classe, p = 0.75, list = FALSE)
training_data2 <- Seeddata2[Training_data1,]
testing_data <- Seeddata2[-Training_data1,]

``` 
#Data exploration

```{r echo=TRUE} 

qplot(classe, fill = "2", data=training_data2, main="Distribution of Classes")
names(training_data2[,-53])

```
#Checking the Model Tree

```{r echo=TRUE} 

#decisionTreeModel <- rpart(classe ~ ., data = training_data2, method = "class")
#decisionTreePrediction <- predict(decisionTreeModel, testing_data, type = "class")


model_tree <- rpart(classe ~ ., data=training_data2, method="class")
prediction_tree <- predict(model_tree, testing_data, type="class")

#class_tree <- confusionMatrix(prediction_tree, testing_data$classe)

#class_tree

#rpart.plot(decisionTreeModel, main = "Decision Tree", under = T, faclen = 0)
#confusionMatrix(decisionTreePrediction, testing_data$classe)
library(rpart.plot)
rpart.plot(model_tree)

```
# Random Forest Model
#Final Prediction

```{r echo=TRUE}

#forest_model <- randomForest(classe ~ ., data=training_data2, method="class")
#prediction_forest <- predict(forest_model, testing_data, type="class")
#random_forest <- confusionMatrix(prediction_forest, testing_data$classe)

#prediction1 <- predict(forest_model, newdata=testing_data)
#confusionMatrix(prediction1, testing_data$classe)

#The Random Forest is a much better predictive model than the Decision Tree, which has a larger accuracy (99.91%).Therefore, we don't need to consider more important predictors for the Random Forest model

```

# Final Analysis

In this study, the characteristics of predictors for both traning and testing datasets (train and test) are reduced. These characteristics are the percentage of NAs values, low variance, correlation and skewness. Therefore, the variables of the data sets are scaled. The training dataset is splitted into subtraining and validation parts to construct a predictive model and evaluate its accuracy. Decision Tree and Random Forest are applied.The Random Forest is a much better predictive model than the Decision Tree, which has a larger accuracy (99.91%).

