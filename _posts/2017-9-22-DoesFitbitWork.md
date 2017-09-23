---
layout: post
date: 2017-09-22 -0500
title: Human Activity Recognition - Do fitbits work?
categories: project
---

My goal is to determine if it is feasible that wearable devices can be used to determine what activities you are doing, or if we are just being fed a nice story to sell us yet another useless piece of electronics.

The increase in living standards in our society has brought some mixed blessings. On one hand we have abundant food supplies (in some parts of the world) and we tend to live longer, but on the other hand, our sedentary lifestyle has lead to an obesity epidemic and seems linked to sharp decreases in quality of life during our golden years.

With the advent of wearable devices (i.e fitbit), the field of human activity recognition (HAR) has gotten a boost. Our hope is that accurate monitoring of human activity will lead us to better understanding of the link between a sedentary lifestyle and its effects on our health, and also we hope that it should allow us to gently nudge individuals towards a more active lifestyles by means of monitoring and positive reinforcement.

One of the many challenge is to be able to determine an activity is being performed across individuals. For example, we all run slightly differently, and given our difference in body sizes our range of motions are different.


# The Dataset

To test the theory that we can use the sensor signals in wearable devices to determine what activity we are performing I used a dataset that comprised the data from 18 different activities, performed by nine individuals wearing three inertial devices.

One of the inertial devices was worn on the ankle, another one on the wrist, and another around the chest area.


![alt text](/images/2017-9-22_post/human.png "Sensor Placement")


Information about this dataset can be found in the reference at the end of this article and here: http://archive.ics.uci.edu/ml/datasets/pamap2+physical+activity+monitoring


## Preparing the data

After importing the data, I dropped the features that included individual identifier information and information not pertaining to motion or inertia (i.e. subject, heart rate, temperature). I also dropped the information from a second accelerometer in each device that tended to saturate (it range of acceleration detected was to small for some high impact activities such as running), and I also dropped the information from a magnetometer deemed irrelevant by the researchers that collected the data.


## Exploring the data

The data comprised a total of approximately 1.9 million samples distributed accross 12 activities and 9 subjects.

With the exception of the data for subject 109 with only 7 thousand samples, the data doesn't seem to present huge imbalances regarding activity or subjects.

The number of sample per activity ranged from 47 thousand (jumping rope) to 230 thousand (ironing), and the number of samples per subject (with the exception of one subject with only 7 thousand samples) between 170 thousand and 270 thousand samples.


![alt text](/images/2017-9-22_post/SubjectSampleCount.png "Subject Sample Count")


![alt text](/images/2017-9-22_post/ActivitySampleCount.png "Activity Sample Count")

# The Process

## Establishing a baseline

Before doing any feature engineering I wanted to establish a baseline by measuring average accuracy of three different algorithms using 5-fold stratified cross validation across the entire dataset using all remaining sensor features.

I did this on three different classifiers: K-Nearest Neigborhs, NaiveBayes, and RandomForest.

I want to see if we could achieve greater than 90% accuracy across each activity, and if so, I wanted to check if could we achieve greater than 90% accuracy with one or two sensors instead of all three.

<style>
.tablelines table, .tablelines td, .tablelines th {
        border: 1px solid black;
        padding: 10px;
        }
</style>


|              | **Accuracy** | **f-1 Score** |
|--------------|--------------|---------------|
| KNN          | 99           | 99            |
| gaussNB      | 75           | 75            |
| RandomForest | 99           | 99            |
{: .tablelines}



Using the default settings for scikit-learn KNN uses 10 neighbors, and random forest algorithm depth is automatically determined so that all leaves in the trees end with one class. This can lead to overfitting, which I suspect is what is happening here with our default KNN and RandomForest classifiers.

Given the poor results obtained by the Naive Bayes classifier I decide to drop efforts of using it, and focus on the KNN and Random Forest classifiers.

To further check the possibility of overfitting the data, I separated my data into a training and test set using a 66/33 split, fitted the classifiers on the training set and evaluated the precision, recall and f1-score for each class on the test set. 

#### Results for KNN


|**class**|**precision**|**recall**|**f1-score**|
:-----:|:-----:|:-----:|:-----:|
1|1.00|1.00|1.00|
2|1.00|1.00|1.00|
3|1.00|1.00|1.00|
4|0.99|1.00|0.99|
5|1.00|1.00|1.00|
6|1.00|1.00|1.00|
7|0.99|0.99|0.99|
12|1.00|0.99|1.00|
13|1.00|0.99|1.00|
16|1.00|1.00|1.00|
17|1.00|1.00|1.00|
24|1.00|1.00|1.00|
avg/total|1.00|1.00|1.00|
{: .tablelines}


I obtained similar results for my Random Forest classifier. Having 100% precision and recall across all classes increases my suspicions that we are overfitting the training data, and decide to generate learning curves for both classifiers.

## Learning Curves


Using features from all sensors, I generated learning curves for my Random Forest classifier where I varied the maximum allowed depth to a value in the range 15 to 35, and did the same for the KNN classifier by varying the number of neighbors to a value in the range 15 to 45.

![alt text](/images/2017-9-22_post/RF_Accuracy_LearningCurve.png "Random Forest Accuracy Learning Curves")

Analyzing the learning curves of the Random Forest classifier we can see that for max tree depths above 15 levels, the accuracy of the classifier on the training data did not decrease as the amount of data increased confirming my suspicion that the baseline Random Forest classifier was overfit. As expected we can see that in all cases the accuracy of the classifier on the test data increased as the training sample size increased.

Decreasing our model complexity helped with our overfitting, but as expected came at the expense of reduced accuracy on our test set which now is showing about 94% accuracy when using all of our training data.

Unfortunately it seems that increasing the size of our training set won't provide us with much increase in accuracy as the slope of our learning curve seems to be tapering off and converging to its asymptotic values.

I also checked that the precision, recall and f1 score of the Random Forest classifier with max depth of 15 also showed behaviour consistent with a model that was not overfitting the data and was learning as the size of the training data increased.

![alt text](/images/2017-9-22_post/RF_MaxDepth15_LearningCurve.png "Random Forest - Depth: 15 - Precision/Recall/F1 Scores - Learning Curve")

### What about the learning curve for the K-Neighbors classifier?

On second thought I decided to not generate the learning curves given that the process was extremely time consuming and I was hitting a deadline. On top of that, it seemed to me that the KNeighbors Classifier code would be bigger than the code for the Random Forest classifier because it has to store the dataset it is trained on in order to make predictions, which would make it less desirable for wearable devices likely to have memory and processor constraints.


### What would the performance be if we used one sensor?

When using all sensors and a random forest with depth 15, we achieved an accuracy of 94% on the test set.

Given that a system involving multiple sensors worn on separate parts of the body (separate devices) is more uncomfortable to use, and present extra technical challenges such as synchronizing the data from the 3 separate devices, it would be interesting to find out if we could get away with using only one sensor and achieve a performance above 90%

I repeated the previous experiment and graphed learning curves for RF classifiers of different depths trained only one the data from one sensor (ankle, hand or chest), and found a similar behaviour as before (the classifiers tended to overfit for depths greater than 15) but with accuracies on the test data around 82-83%.

![alt text](/images/2017-9-22_post/RF_Hand_accuracy_LearningCurve.png "Random Forest - Hand Sensor - Accuracy - Learning Curves")

When using two sensors we observed a similar behaviour as before but with an accuracy between 88% and 91%.

![alt text](/images/2017-9-22_post/RF_Hand&Chest_accuracy_LearningCurve.png "Random Forest - Hand & Chest Sensors - Accuracy - Learning Curves")


### Would increasing the number of trees in the forest improve our accuracy?

Increasing the number of trees in the Random Forest is an effective way to reduce variance caused by model overfitting, but in our case our learning curves did not indicate that we were overfitting on our. I checked anyway just in case, and confirmed my initial thoughts. I trained several RFs of depth 15, with just the Hand sensors, with several number of estimators and did not achieve an increase in test set accuracy.


![alt text](/images/2017-9-22_post/RF_Hand_NumEstimators_LearningCurve.png "Random Forest - Hand Sensor - Accuracy - Vary Number of Estimators - Learning Curves")


## Is all hope lost? Can we improve accuracy of a single sensor classifier above 90%

### Try boosting! Bring allong our friend Gradient Boosting Classifier.

I decided to run a parameter grid search with 5-fold cross-validation training only on the hand sensor parameters. I chose the hand sensor given that individuals already wear wrist devices (watch) and it wouldn't be hard to incorporate an extra sensor in the electronics.

Since previously, forests with depth higher than 15 seemed to be prone to overfitting, I set up the grid depth to try the values: 5, 10, 15, and 30 just in case. Also given that we had no guidance on the learning rate I decided to try the default rate of 0.1 and double that amount. For the number of estimators, I decided to try few stages: 10, 15, and 20 given that the GBC is very time consuming to train.

Now... I will update this post with the results of the grid search and possible next steps when it finishes running. I killed my AWS instance and had to run this grid search on my laptop.

Stay tuned!


## References:
1. A. Reiss and D. Stricker. Introducing a New Benchmarked Dataset for Activity Monitoring. The 16th IEEE International Symposium on Wearable Computers (ISWC), 2012.
2. A. Reiss and D. Stricker. Creating and Benchmarking a New Dataset for Physical Activity Monitoring. The 5th Workshop on Affect and Behaviour Related Assistance (ABRA), 2012.