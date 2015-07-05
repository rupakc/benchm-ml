
#### How to Benchmark Your Tool of Choice with Minimal Work

#### aka The Absolute Minimal Benchmark

If your favorite software tool for machine learning (either open source or commercial) is not benchmarked here, 
you can get an idea of speed/accuracy with minimal work by following the instructions below.

Get the [training data of 100K records](https://s3.amazonaws.com/benchm-ml--main/train-0.1m.csv)
and the [test data](https://s3.amazonaws.com/benchm-ml--main/test.csv) (csv files).

I suggest you benchmark random forests, it's both easy to train (no much tuning needed) and provides
pretty good accuracy. Use `100` trees and max depth `20`. Keep the categorical
variables non-ordinal and do not do any feature engineering to improve accuracy.

Do the following (pseudocode) in your system:
```
train_data = read(train_data_file)
test_data = read(test_data_file)

model = train_random_forest(train_data, n_trees = 100, depth = 20)

predictions = predict(model, test_data_without_labels)
calculate_AUC(predictions, test_data_labels_only)
```

Once this works, try the larger [training data of 1M records](https://s3.amazonaws.com/benchm-ml--main/train-1m.csv)
and optionally next the [training data of 10M records](https://s3.amazonaws.com/benchm-ml--main/train-10m.csv).

Here are the results for `n = 1M` for a few software tools (on 1 EC2 instance 32 cores, 250GB RAM):

Tool          | Time (sec)  |  AUC
--------------|-------------|----------
scikit-learn  |   200       |  72.5
H2O           |   130       |  75.2
xgboost       |   30        |  74.9
Spark MLlib   |   250       |  62.5

If you have results for other software tool(s), contact me by submitting a github issue.
The main point would be to be able to train in a couple of minutes (and not crash out of memory), 
and get decent accuracy on a high-end commodity server/desktop (or in the cloud).
Please submit software name, training set size, hardware (number of cores, RAM), training time,
AUC on the test set (and number of trees/max depth if different from above).

**Why binary classification and random forests?** Because binary classification is the largest
use case in machine learning applications, while random forests is the most widely used tool 
to deal with that after logistic regression (but linear models are usually less accurate).

**Why a mix of categorical and numeric features and 1 million records?** Because most business
applications have categorical features, and while despite the big data hype 
a majority of users have actually smaller datasets, there are a decent amount of users around that size.

I would argue that you cannot have a decent out-of-the-box general machine learning tool 
(open source/commercial, command line/GUI-based, software/cloud service) that cannot do
binary classification with random forest (or maybe GBM) on data with a mix of categorical and numeric features on
1 million records with decent training time and decent accuracy.
