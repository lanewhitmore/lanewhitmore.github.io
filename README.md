# NATICUSdroid-Malware-Machine-Learning-Classification
Classifying malware or non-malware applications on Android OS. Data from Mathur, 2022 at the University of Toledo
A Machine Learning Approach for Identifying Malicious Android Applications Based on Application Permissions
 
Dingyi Duan
Roberto Cancel
Lane Whitmore
Shiley-Marcos Engineering Department, University of San Diego
August 15, 2022
 
 
 
 
 
 
 
 
 
 
 
Abstract
Android owns such a large market share due to its versatility and open-source framework, allowing many companies to implement it into their own devices. However, this has made the Android operating system vulnerable to malware attacks. The high propensity of malware applications has led to the need for an automated malware detection system. The NATICUSdroid Dataset was created by Mathur, Podila, Kulkarni, Niyaz, and Javaid of the University of Toledo to use application permissions on Android operating systems to identify whether or not an application is malicious. We evaluated using Multi-Layer Perceptron (MLP), Support Vector Machines (SVM), Decision Tree (DT), Extra Tree (ET), and Logistic Regression (LR).
Since the cost of false negatives outweighs the cost of false positives, models were optimized for precision with a minimum accuracy threshold of 93%. The RF yielded an accuracy of 0.969 and a precision of 0.973. Our findings indicated that the model correctly classified 4304 malware apps, misclassified 115 malware as non-malware, and misclassified 159 non-malware as malware. While the classes were balanced, we attempted further to optimize our precision scores with a weighted approach. The weighted ET reaches the highest precision of 99.81%, with an accuracy of 92.74%. We, therefore, chose the weighted RF with a precision of 99.47% and an accuracy of 94.88% as the optimal model. The weighted RF yielded only 21 false negatives, but the cost of increased false positives - 430 false positives. Again, comparing the results of the weighted RF to the remaining weighted models performs best when considering all scenarios.
Keywords: Android, malware, machine learning, classification, application security
 
 
 
Table of Contents
List of Tables
5
List of Figures
6
Introduction
7
Objective
7
Methodology
7
Characteristics
7
Exploratory Data Analysis
8
Data Pre-Processing
9
Data Splitting
9
Results
10
Modeling
10
                          Multi Layer Perceptron
10
                          Support Vector Machine
11
                          Decision Tree
12
                          Random Forest
12
                          Extra Trees
13
                          Logistic Regression
14
Discussion
15
Model Selection
15
Strengths and Limitations
18
References
19
Appendix
20


List of Tables
(1)   Final Model Metrics
15
 
 
 
 
 
 
 
 
 
 
 
 
 
 


List of Figures
(1)  Confusion Matrix of Weighted Random Forest
16
(2)  ROC Curves of Weighted Models
17
 
 
 
 
 
 
 
 
 
 
 
 

 







 
 
Introduction
The evolution of cellular phones has led to the rapid increase of the Android operating systems in smartphones; in fact, the Android operating system controls an estimated 81% share of the global smartphone market (Statista Research Department, 2022). Android owns such a large market share due to its versatility and open-source framework, allowing many companies to implement it into their own devices. However, this has made the Android operating system particularly susceptible to malware attacks. For instance, as recently as 2014, Forbes has reported that 97% of the total malware for mobile devices occurs on devices using Android operating systems (Kelly, 2014). Additionally, Android has dominated the technology news sector with headlines such as "New Android malware apps installed 10 million times from Google Play," published by Bill Toulas of  BleepingComputer LLC. The high propensity of malware applications has led to the need for an automated malware detection system. As a result, the NATICUSdroid Dataset was created by Mathur, Podila, Kulkarni, Niyaz, and Javaid of the University of Toledo to use application permissions on Android operating systems to identify whether or not an application is malicious (Mathur et al., 2021).  
Objective
This project aimed to correctly classify malware applications to be flagged. An emphasis was placed on finding a statistical relationship between the application permissions and malware application class to most effectively identify and flag applications that may be malware. Due to this, less focus will be placed on accurately classifying non-malicious applications.
Methodology
Characteristics
	The NATICUSdroid dataset contains 29,332 samples with 86 binary attributes and one  target variable. All of the binary attributes describe the types of permissions gained by the app. These are separated into two main groups: custom permission and native, or Android-created, permission and indicated by the value of “0” and “1”. The target variable “Result” simply identifies whether the app is identified as malware. The data was collected between 2010 and 2019. Through EDA below, we will explore the distribution of each feature for all the samples and aim to discover the inner relationship between them as well as with the target variable. We expect to disclose that malware apps follow a specific pattern to cause harm by acquiring certain permission within the android system.

Exploratory Data Analysis
To prevent data leakage, the dataset was immediately split into 70 % training set (n=20,532) and 30% test set (n=8,800) – as suggested by Mathur et al.’s (2021) documentation in the UCI repository. EDA was conducted on only the training set to ensure our test set did not inform our inferences and remained unseen. Examining the means of our binary attributes showed that only ten of the 86 attributes were present for more than 25% of observations - these were strictly native permissions and included: Internet, Access_network_state, write_external_storage, read-phone_state, access_wifi_state, wake_lock, access_coarse_location, receive_boot_completed, access_fine_location, and vibrate. All of these native permissions represent basic app functionality requirements.
Custom and native apps were segregated and explored to further analyze the data. Once segregated, their propensity for identified malware applications was examined. The analysis of apps identified as malware showed strong prevalences for certain custom permissions identified as malware, while other permissions were not present in malware. Conversely, all custom permissions were present in benign applications. The analysis of our native permissions showed that only three native permissions have no occurrences in malware apps. These include FOREGROUND_SERVICE, REQUEST_INSTALL_PACKAGES, READ_APP_BADGE, AND USE_FINGERPRINT. FOREGROUND_SERVICE requires user notification of performed app operations - notifications of operations would be detrimental to a malicious application. REQUEST_INSTALL_PACKAGES allows an application to request installing packages and is highly restricted by Android. READ_APP_BADGE is, again, a notification-oriented permission. Finally, USE_FINGERPRINT allows an app to use fingerprint hardware, allowing additional security to access or open the app. Only one permission is minimally represented, PROCESS_INCOMING_CALLS, since it was depreciated while the data was collected.Lastly, android.permission.android.permission.READ_PHONE_STATE is an error in the data since android.permission.READ_PHONE_STATE is a highly occurring permission for malware apps.
Data Pre-Processing
The initial data set provided by the University of Toledo is absent of the class issues that a data set would ordinarily contain as both classes are essentially a 50/50 split. Additionally, scaling or normalization processes were not required as all the features are binary. The dataset also contained no missing or null values.  Due to this, the primary focus of the pre-processing done for this project was committed to the feature selection for model construction. 
Feature Selection
	While the data set did not contain any null variables or class imbalance, there were issues with highly correlated and near-zero variance features. First, features with a variance of one percent or less were removed from the data set. In total, this process removed 11 features from the original 87. Removing zero variance and near-zero variance features will improve model performance later. These 11 features offered little to no variance for the model to learn a pattern and therefore are irrelevant. The remaining 76 features were then used to build a correlation matrix. Features with greater than 0.80 correlation were then removed from the data set to reduce multicollinearity concerns during modeling. This process removed 20 more features and left the final data set with 56 features. By removing these features, models that do not perform feature selection will only be presented with relevant features, and the multicollinearity of the models should be reduced. Reducing the multicollinearity of the models will benefit by reducing the potential of overfitting and ensuring the model results are not inflated by correlated features. 
Results
Modeling
Multi Layer Perceptron (MLP)
Multi Layer Perceptron (MLP) is a feed forward neural network consisting of three layers: the input, output layer, and hidden layer(s). We initially trained the MLP by optimizing the learning rate and comparing the Adam and SGD optimizer for precision performance. As we iterated through a list of learning rates from 0.00001 to 4.0, we noted the Adam optimizer offered the best performance with an accuracy of over 96% at a learning rate of 0.005. Once we chose the Adam optimizer, we further tuned through two additional hyperparameters, epsilon which is the stability for Adam, and epochs, to finalize our MLP model. With the final decision for learning rate of 0.005, epsilon of 0.001 and epochs of 8, the MLP model provided impressive performance: an accuracy score of 0.965, a precision score of 0.973, and an ROC score of 0.965. With nearly 9000 samples, MLP only has 117 false negatives and 195 false positives. 
Since we are more concerned with identifying malware rather than “escapes”, we decided to turn up a notch by adding a weighted version of the model to optimize the precision score even further. By gradually shifting the weight from the “non-malware” side, we achieved a precision score of a whopping 0.995 with only 18 false negatives. Even though our accuracy dropped, this may be more suitable from a business perspective.
Support Vector Machines
Support Vector Machines (SVM) are a supervised learning approach that takes the data points within a dataset and generates a hyperplane that best separates them by two classes. One of their primary advantages is their effectiveness in high-dimensional settings. Initially, the SVM was trained with varying cost, or regularization, values for the four kernel types: linear, polynomial, radial basis function (RBF), and sigmoid. We note that the radial basis function produced the highest precision of 0.969 at a cost value of 15 – indicating that a non-linear classifier performs best with our data. Then, using the RBF kernel, we again trained our model to identify a more precise cost – identified as 17 with a precision of 0.970. Gamma is a parameter of the RBF kernel and identifies the decision region. After training the SVM with a range of Gamma values, the final optimal SVM was identified using the RBF kernel, cost of 17, and Gamma of 0.35, resulting in a 0.967 accuracy and 0.971 precision. Our findings indicate that the final SVM yielded 124 false negatives and 161 false positives.
In keeping with the weighted approach in our MLP, the SVM was also weighted to optimize the precision score further. Interestingly, the weighted approach of {0: 0.95, 1: 0.05} yielded the best precision at 0.992; however, our accuracy was sacrificed and reduced to 0.950. Our findings indicate that the weighted SVM yielded only 30 false negatives but 414 false positives.
Decision Tree
	Decision Tree (DT), as a fast, easy-to-implement classification model that can handle both categorical and numeric values, is another strong contender for this task. While we expected it to perform well, we must also be aware of the overfitting issue.  For decision tree classification we start by iterating through the tree depth for both Gini and Entropy impurity with 10-fold cross validation. Using a base model, both impurities achieved very similar precision scores of 0.963. Since decision trees are known for their overfitting issue, we then proceeded to tune the cost complexity hyperparameter “CCP_alpha” for pruning. The results showed that after pruning,  “Gini” impurity outperformed “Entropy” with a precision score of 0.966 at “CCP_alpha” = 0.00005. Furthermore, the confusion matrix showed that the decision tree using “Gini” impurity with “tree_depth = 27” and “CCP_alpha” = 0.00005 only misclassified 132 non-malwares as malwares with an ROC score of 0.965, which is a decent result. 
	Once again, we added class_weight to our decision tree model and focused on catching escapes rather than false calls. With the optimal weight ratio of 0: 0.95 and 1: 0.05, the weighted decision tree model achieved a precision score of 0.992, a fairly high accuracy score of 0.935, and an ROC score of 0.934. The final confusion matrix gave a false positive of 30 and a false negative of 542.
Random Forest
	Random forest (RF) builds multiple decision trees on different samples and takes their majority vote for classification –  an ensemble of decision tree algorithms. RF uses bagging and random features when building each tree to create an uncorrelated “forest” of trees to create more accurate voted predictions than any individual tree. Furthermore, since Mathur et. al (2021) identified the RF as the best performing model in their study, it was selected as an approach in our study as well. It, like ET, has three hyperparameters: criterion, max depth, and CCP_alpha. The criterion represents the function used to measure split quality; Max_depth can be constrained to avoid overfitting; CCP_alpha is a complexity parameter used for pruning. Initially a range of max_depth values were used to train the model; however, CCP_alpha values attained a higher precision score, so it was used in place of max_depth for our final model using the gini criterion and ccp_alpha of 0.0005. This yielded an accuracy of 0.969 and precision of 0.973. Our findings indicate that the model correctly classified 4304 malware apps, misclassified 115 malware as non-malware, and misclassified 159 non-malware as malware. These are the best results for our models.
	Again, a weighted approach to optimize the precision score further was evaluated. A point of difference is that the weighted approach of {0: 0.90, 1: 0.10} yielded the best precision at 0.995; however, our accuracy was sacrificed and reduced to 0.949. The weighted RF yielded only 21 false negatives, but an astonishing 430 false positives. Again, when comparing the results of the weighted RF to the remaining weighted models, it performs best when considering all scenarios.
Extra Tree Classifier
Extremely Randomized Trees Classifier (ET) is an ensemble learning approach that aggregates the results of multiple decision trees collected in a “forest” to produce its classification result. The primary difference between RF and ET is that ET chooses the optimum split randomly - indicating ET adds randomization but still has optimization. It, like RF, has three hyperparameters: criterion, max depth, and CCP_alpha. The criterion represents the function used to measure split quality; Max_depth can be constrained to avoid overfitting; CCP_alpha is a complexity parameter used for pruning. First, we trained our ET with a max_depth range between two and 52 for the Gini and entropy criterion, resulting in similar precision scores. Since tuning to ccp_alphas yielded better precision results, we tuned our ET for ccp_alpha values for the Gini and entropy criterion, resulting in our final ET model using the Gini criterion with a ccp_alpha value of 0.00005, producing an accuracy of 0.967 and precision of 0.973. Our findings indicate that the model correctly classified 4303 malware apps, misclassified 116 malware as non-malware, and misclassified 172 non-malware as malware.
Again, a weighted approach to optimize the precision score further was evaluated. Again, the weighted approach of {0: 0.95, 1: 0.05} yielded the best precision at 0.998; however, our accuracy was sacrificed and reduced to .928. The weighted ET yielded only seven false negatives, but an astonishing 632 false positives.
Logistic Regression
Logistic Regression (LR) is an easy-to-implement classification approach that estimates the probability of an event occurring based on a given dataset of independent variables. The saga solver was chosen since it is the solver of choice for sparse multinomial LR and our dataset consisted of sparse binary attributes. L1 regularization produced a higher precision of .953 compared to L2 regularization’s precision of 0.951 – resulting in the final LR model with an accuracy of .959 and precision of .958. Unfortunately, LR was the worst performing model; therefore, its results will not be discussed further.
Discussion
Model Selection
The performance metrics for all models is available in Table 1. While our primary goal was correctly classifying malware, shareholders dictated an Accuracy threshold score of 0.930 to limit false positives. False positives indicate benign apps that are flagged as malware and the threshold was set to avoid discouraging developers see figure in the Android system and spamming users with potential false positives. Given this threshold and our stated goal, the weighted RF is the final selected model. We note that the weighted MLP outperforms the weighted RF in precision with a 0.998 compared to 0.995, respectively; however, the weighted MLP sacrificed Accuracy at 0.920 compared to the weighted RF at 0.949.
Table 1 
Final Model Metrics
Model
Accuracy
Precision
Recall
F1
Auc
MLP
0.9648
0.9680
0.9609
0.9645
0.9648
Weighted MLP
0.9200
0.9951
0.8434
0.9130
0.9197
SVM
0.9676
0.9715
0.9633
0.9673
0.9676
Weighted SVM
0.9676
0.9925
0.9055
0.9470
0.9494
Decision Tree
0.9650
0.9696
0.9598
0.9647
0.9650
Weighted DT
0.9350
0.9922
0.8763
0.9307
0.9347
Random Forest
0.9688
0.9733
0.9637
0.9685
0.9687
Weighted RF
0.9488
0.9947
0.9018
0.9460
0.9485
Extra Trees
0.9673
0.9732
0.9607
0.9669
0.9672
Weighted ET
0.9274
0.9981
0.8557
0.9215
0.9271
Logistic Regression
0.9593
0.9581
0.9603
0.9592
0.9593
Weighted LR
0.8856
0.9939
0.7749
0.8708
0.8851


When reviewing the weighted RF confusion matrix (see Figure 1), we note only 21 false negatives and 430 false positives. To provide context for the false positives, flagged apps would be identified as potential malware to users before download. The Android technical team will investigate them and remove them if they are determined to be malware. In reviewing the ROC Curve (see Figure 2), we note an AUC of 0.949. While the AUC for the unweighted RF was higher at 0.969, the number of false negatives was incrementally higher at 115. Again, the cost of false negatives far outweighs false positives in this scenario.
Figure 1
Confusion Matrix of Weighted Random Forest

Figure 2
ROC Curve of Weighted Models

	Finally, the weighted RF feature importance was calculated and plotted. We note that the two most important features, READ_PHONE_STATE and INSTALL_SHORTCUT, occurred frequently in malware and less frequently in non-malware apps. Conversely, com.google.android.c2dm.permission.RECEIVE predominantly occurred in non-malware apps. RECEIVE_BOOT_COMPLETE occurred more frequently in malware, but also occurred frequently in non-malware. From this preliminary analysis, we can see that the RF-based decision making approach assisted in correctly identifying each class since permissions existed for both classes and in varying combinations.

Strengths and Limitations
One major strength of this study is that all preprocessing and modeling steps are iterable, and results can be easily updated with a new data set of applications and permissions. Unfortunately, the data used in this study is dated since it was collected from 2010-2019 and limited in scope since it examined only 29,332 applications. As of June 2022, the Android ecosystem has more than 2.6 million applications (Ceci, 2022). As malware developers become increasingly sophisticated,  trends in permission for malware will continue to change. Additionally, production-ready code for implementation is not currently available but could be a part of future work. Once in production, the model would need to be constantly updated and recalibrated to capture malware shifts and trends.
 

References
Ceci, L. (2022, July 27). Google play store: Number of apps 2022. Statista. Retrieved August 12, 2022, from https://www.statista.com/statistics/266210/number-of-available-applications-in-the-google-play-store/
Kelly, G. (2014, May 24). Report: 97% of Mobile Malware is on Android. This Is the Easy Way You Stay Safe. Forbes. Retrieved August 11, 2022, from https://www.forbes.com/sites/gordonkelly/2014/03/24/report-97-of-mobile-malware-is-on-android-this-is-the-easy-way-you-stay-safe/?sh=159e7dba2d4f
Mathur, A., Podila, L. M., Kulkarni, K., Niyaz, Q., & Javaid, A. Y. (2021). NATICUSdroid: A Malware Detection Framework for Android Using Native and Custom Permissions. Journal of Information Security and Applications, 58, 102696. Retrieved From https://archive-beta.ics.uci.edu/ml/datasets/naticusdroid+android+permissions+dataset
Statista Research Department. (2022, August 11). Global Smartphone Market Share From 4th Quarter 2009 to 1st Quarter 2022. Statista. Retrieved August 11, 2022 from https://www.statista.com/statistics/271496/global-market-share-held-by-smartphone-vendors-since-4th-quarter-2009/
Toulas, B. (2022, July 16). New Android Malware Apps Installed 10 Million Times From Google Play. Bleeping Computer. Retrieved August 11, 2022 from https://www.bleepingcomputer.com/news/security/new-android-malware-apps-installed-10-million-times-from-google-play/
