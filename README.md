# Identifying Fraud in Enron Emails

## Summary

### Aim
Given emails of a person, being able to predict if the person is person of interest (poi).

### Dataset
The Enron email dataset contains approximately 500,000 emails generated by employees of the Enron Corporation. It was obtained by the Federal Energy Regulatory Commission during its investigation of Enron's collapse.

__Some Inferences from exploring the dataset:-__ <br>
Total number of data points: __146__ (quite less) <br>
Total number of features: __21__ (most are financial features having numeric values) <br>
Total number of poi's in the dataset: __18__ <br>
Total number of non-poi's: __128__ <br>
So we can say that to train the algorithms, we have very few examples to learn from and hence it will be vital in choosing the way split the data. We will pay special attention to this in the coming sections.

### Why machine learning?
There are almost 5,00,000 emails that have been shared across the organization. There must be a lot of things that we have to check to make sure that a person is 'poi'. Right from finding out each and every feature to the relations between each of them to drawing insights about who might be a poi, everything can be done in a easy and better way if machine learning algorithms are used. Mining the dataset manually would consume a lot of time and effort.

### Outliers
- __NaN__: The dataset had some outliers that had to be cleant so as to make the analysis easier and the algorithms robust. Some of the features were high in NaN values, which made no sense and was pure noise. Thus these were removed. The threashold I set was if more than 50% are NaN, discard the feature. On the same lines, some data points had all of the values set to NaN; these were removed too. The features that were removed in the above procedure are **loan_advances, director_fees, restricted_stock_deferred, deferral_payments, deferred_income, long_term_incentive**
- __TOTAL__: This must have been a spreadsheet calculation. The value is the sum of all the total_payments, hence a lot larger and had to be removed. We can calculate the values anytime we want with inbuilt functions. This one was removed as well.
- __THE TRAVEL AGENCY IN THE PARK__: Since this is not a person, this data point was scrapped as well. I caught hold of this data point when I was checking with the keys in the data_dict which had the names of all the employees. In the poi_names.txt there were some names and I was trying to figure out of comparing that to the data_dict.keys() would give me any good results. Though comparing them was not of much use, printing the keys allowed me to see this key which was not a name of any person.
- __LOCKHART EUGENE E__: This person had NaN in almost all of the fields and hence not very useful, hence had to be removed from the dataset.

### Files
Please check the project directory which has the poi_id.py file which houses the code and **<star>.pkl** files in which the output has been dumped.

## Feature Engineering

### I engineered 3 features
Since this dataset is an example of supervised learning, we know the label 'poi', we can find some interesting features around the people who are considered pois. Below features explain the amount of emails between poi's which may be a factor in determining if people having sent a lot of emails be classified as poi.
1. fraction_of_messages_from_poi
2. fraction_of_messages_to_poi

3. total_assets
This feature was more of intuitive and interesting one. It is important to know who made the most money considering all the financial features present in the dataset. Added up 'salary', 'total_stock_value', 'exercised_stock_options' and 'bonus'

### Feature selection
**How did I select the value k=9**
I had the precision, recall and accuracy tested for all values ranging between 1 to 16 (final number of features). We had a total of 21 features out of which we removed 6 features in the outlier section. Hence we now have a total of 21-6=15 and a label 'poi'. <br>
The results showed that max precision/accuracy/recall were obtained at k=9, where features are: <br>
__label__: 'poi' <br>
__features__: 'salary', 'total_payments', 'total_assets', 'bonus', 'fraction_of_messages_to_pois', 'total_stock_value', 'shared_receipt_with_poi', 'exercised_stock_options', 'restricted_stock' <br>

```diff
+ Note: Our engineered features 'fraction_of_messages' and 'total_assets' have shown up in the top features. :)
```

Now, to stress the importance of feature selection, I had tuned the required parameters and done the validation, the scores without the top 9 features was as below:
```
Accuracy: 0.846043956044
Precision: 0.325
Recall: 0.4
```
The score had further improved a lot.Please check the **Validation** section to find the final scores. The scores have been improved significantly as the model had only the top 9 features to work on.

### Feature scaling
This part is essential when there is a huge margin of difference when comparing 2 features i.e. if one feature completely dominates the other due to its large value, then the features need to be scaled to bring them under the same roof. <br>
In the current dataset, I did not find any feature significatly dominating the other and hence I preferred not scaling them. 
That being said, to gap the minor differences between the salary, stock_option, etc if needed a scaling function can be applied. I felt it to be less useful.

## Algorithms
Initially I used 3 algorithms, by splitting the data into training and testing sets using the train_test_split function.
The 3 algorithms were:
knn, svm and decision trees.

Since the dataset is smaller, it is generally better if we can do a k-fold cross_validation so that there are different types of training and testing data sets and value is computed on their mean(s).

For tuning the Decision tree classifier I mentioned the below parameters:
max_depth=11, random_state=51

## Validation and Evaluation
**What is validation?** <br>
When we design a model, it is very essential that we verify thoroughly if it works in all cases and does not end up screwing results even with minor changes to dataset or does not act wierdy when it sees new data. To clear us of doubts about the smoth generalized functioning of our model, very validate it and derive results. These results are measures of probability usually which mention how much successful we are in predicting the labels.

**Why is validation important?** <br>
Initially when I was trying with almost all the classifiers I could, I got an accuracy of above 90%. However, the precision and recall were as low as 0.0 which was bothersome. <br>
It is important that we not get carried away with high accuracy and not check other parameters and especially in classification algos. 

For ex: <br>
In our case we had 18 pois, if our classifier only predicted the non-pois i.e. 128 out of 146 which is ~87%, is a high accuracy score, but this would allow the poi's to walk home freely which is terrible
In the other case, if classfier predicted the poi's i.e. 18/146 which is ~13%, the accuracy is very bad but still woould catch the non-poi's as poi's.

Hence, we we need to be pretty careful in validating if our algorithms are correct.

**What is parameter tuning?**
In simple terms, let's consider an example of a bike. <br>
For ex: If I want to ride my bike on a steep slove, I would want my bike to have good speed as well as acceleration, I would not care much about it's fuel efficiency. However on the contrary, if I want to go on a long ride, speed would not matter as much as fuel efficiency would. For these to happen, we tune some parts of the bike like the ignition system, rpm, etc. In the same way, when we want our machine learning algorithms to perform well under certain conditions, we tune some parameters which allow us to get better results. <br>

**Tuning of the parameters**
After tuning the parameters, the recall and precision crossed the 30% threshold mark. This can be done better and will do it in the next iterations as I dig more into tuning of the algorithm and evaluation.

__Tuned parameters__:
1. __max_depth__ = 11,
It works more like a depth first search that is used in graphs for traversal through them. For simplicity if our decision tree classifier is a series of if else statements, this parameter will decide the level of nesting of the else's within if's or if's within if's and so on.
2. __random_state__ = 51, 
This parameter keeps the output of the classifier constant. In case we do not mention this parameter, by default it is a np.random(any_number) and thus the splits may start from any point in the dataset. I had used GridSearchCV to create multiple iterations to find out that 51 suits me best.

**The benchmarks: (when the newly engineered features are used)**
```
Accuracy: 0.875567765568
Precision: 0.45
Recall: 0.45
```

**The benchmarks: (when newly created features are excluded)**
```
Accuracy: 0.834688644689
Precision: 0.0
Recall: 0.0
```
__Precision__ in our case is, in case the algorithm has guessed that a person is a poi, how much are we sure that the person is a poi. <br>
__Recall__ in our case is, is the probability of our algorithm to correctly predict that a person is a poi, provided the given person is a poi.

In mathematical terms, <br>
precision = tp / (tp + fp) <br>
recall = tp / (tp + fn) <br>
where tp = true positive <br>
fp = false positive <br>
fn = false negative <br>

## References
https://docs.google.com/document/d/12-vI18qm2R79xpjmwloy-lKe5GdHV_KQVpaweieOnXY/edit?usp=sharing <br>
http://scikit-learn.org/stable/modules/pipeline.html <br>
http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html <br>
http://chrisstrelioff.ws/sandbox/2015/06/25/decision_trees_in_python_again_cross_validation.html <br>
http://dataaspirant.com/2017/02/01/decision-tree-algorithm-python-with-scikit-learn/ <br>
http://machinelearningmastery.com/tune-number-size-decision-trees-xgboost-python/ <br>
https://discussions.udacity.com/t/selectkbest-best-value-for-k/235319/1 <br>
https://docs.google.com/document/d/12-vI18qm2R79xpjmwloy-lKe5GdHV_KQVpaweieOnXY/edit# <br>
https://www.quora.com/What-is-the-best-way-to-understand-the-terms-precision-and-recall
