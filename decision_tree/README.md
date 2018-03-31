# Decision Tree

Decision Tree is a supervised learning model.

## Table of Contents

* [Decision Tree Summary](#decision-tree-summary)
* [Entropy](#entropy)
    * [Calculating Entropy](#calculating-entropy)
    * [Information Gain](#information-gain)
        * [Information Gain on Grade Feature](#information-gain-on-grade-feature)
        * [Information Gain on Bumpiness Feature](#information-gain-on-bumpiness-feature)
        * [Information Gain on Speed Limit Feature](#information-gain-on-speed-limit-feature)
* [Decision Tree Strengths and Weaknesses](#decistion-tree-strengths-and-weaknesses)
* [Terrain Classifier with Decision Tree](#terrain-classifier-with-decision-tree)
    * [Create and Train a Decision Tree Classifier](#create-and-train-a-decision-tree-classifier)
    * [Run the Classifier](#run-the-classifier)
    * [Terrain Classifier Result](#terrain-classifier-result)
* [Decision Tree Parameters](#decistion-tree-parameters)
    * [The min_samples_split Parameter](#the-min_samples_split-parameter)
* [Email Classifier with Decision Tree](#email-classifier-with-decision-tree)

## Decision Tree Summary

* Decision Tree can solve the non-linear problem by using multiple linear decision boundary.

![Multiple linear lines](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/slides/01_multiple_linear_lines.png)

![Multiple linear lines](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/slides/02_multiple_linear_lines.png)

* A high bias machine learning is one that practically ignores the data.
* A high variance machine learning is one that extremely perceptive to data. It can only replicate the stuff it's seen before.
* Tuning the trade-off between bias and variance is the art of machine learning.

## Entropy

* Entropy controls how the Decision Tree decides where to split the data.
* Entropy is a measure of impurity in the bunch of examples.
* When all of the examples are belong to the same class, the `entropy = 0`.
* When all of the examples are evenly split between classes, the `entropy = 1.0`.

### Calculating Entropy

Suppose we have three feature: `grade`, `bumpiness`, and `speed_limit`. And the labels is the `speed`. Let's find the entropy of the labels (parent entropy).

![Calculating Entropy](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/slides/04_calculating_entropy.png)

```txt
labels: ssff

Pslow = 2/4 = 0.5
Pfast = 2/4 = 0.5

Entropy = (-Pslow * log2(Pslow)) + (Pfast * log2(Pfast))
```

Calculate the entropy with Python:

```py
import math

entropy = -0.5 * math.log(0.5, 2) -0.5 * math.log(0.5, 2) # 1.0
```

### Information Gain

In order to find the feature to split on, Decision Tree will calculate the maximum information gain on each feature.

```txt
Information Gain = Entropy of Parent - weighted average of its children entropy
```

#### Information Gain on Grade Feature

Let's find the information gain if we split the data on the `grade` feature.

![Calculating children entropy for grade feature](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/slides/05_entropy_for_grade_feature.png)

```txt
Speed labels based on the grade:
STEEP => ssf
FLAT => f

STEEP NODE: ssf
Pslow = 2/3 = 0.67
Pfast = 1/3 = 0.33
Entropy = (-Pslow * log2(Pslow)) + (Pfast * log2(Pfast))
        = -0.67 * log2(0.67) -0.33 * log2(0.33)
        = 0.9184

The python code:
Entropy = -(2/3.0) * math.log((2/3.0), 2) - (1/3.0) * math.log((1/3.0), 2)

FLAT NODE: f
Pslow = 0
Pfast = 1
Entropy = 0 (only 1 class)

Children Entropy = (Steep proportion * Steep entropy) + (Flat proportion * Flat entropy)
                 = (3/4 * 0.9184) + (1/4 * 0)
                 = 0.6888

Information Gain = Parent Entropy - Children Entropy
                 = 1 - 0.6888
                 = 0.3112
```

#### Information Gain on Bumpiness Feature

Next find the information gain if we split the data on the `bumpiness` feature.

```txt
Speed labels based on the bumpiness:
BUMPY => sf
SMOOTH => sf

BUMPY NODE: sf
Entropy = 1 (both classes splitted evenly)

SMOOTH NODE: sf
Entropy = 1 (both classes splitted evenly)

Children Entropy = (Bumpy proportion * Bumpy entropy) + (Smooth proportion * Smooth entropy)
                 = (1/2 * 1) + (1/2 * 1)
                 = 1

Information Gain = Parent Entropy - Children Entropy
                 = 1 - 1
                 = 0
```

#### Information Gain on Speed Limit Feature

Now let's find the information gain if we split the data on the `speed_limit` feature.

```txt
Speed labels based on the speed_limit:
YES => ss
NO => ff

YES NODE: sf
Entropy = 0 (only 1 class)

NO NODE: ff
Entropy = 0 (only 1 class)

Children Entropy = (Yes proportion * Yes entropy) + (No proportion * No entropy)
                 = (1/2 * 0) + (1/2 * 0)
                 = 0

Information Gain = Parent Entropy - Children Entropy
                 = 1 - 0
                 = 1
```

From calculation above, we now that by splitting the data on `speed_limit` feature, we'll have the biggest information gain.

## Decision Tree Strengths and Weaknesses

* It's easy to understand and to use.
* We can build a bigger classifier with decision tree, like ensemble method.
* It prones to overfitting especially when we have a lots of features.

## Terrain Classifier with Decision Tree

### Create and Train a Decision Tree Classifier

We can use the [Decision Tree classifier](http://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) easily with scikit-learn:

```py
from sklearn import svm

# Create and train the classifier.
classifier = svm.SVC(kernel = "linear")
classifier.fit(features_train, labels_train)

# Predict the output labels.
clasifier.predict(features_test)
```

### Run the Classifier

Type the following command on your terminal to run the terrain classifier:

```shell
# Go to the project directory.
$ cd /path/to/intro-to-machine-learning

# And run it with Python
$ python decision_tree/terrain_classifier
```

### Terrain Classifier Result

The result from using the Decision Tree classifier to predict the vehicle's speed given a terrain condition.

```txt
🤖 Accuracy: 0.908
```

![Decision Tree decision boundary](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/terrain_classifier/plot.png)

## Decision Tree Parameters

* `min_samples_split*: The minimum number of samples required to split the node (default to `2`).

### The min_samples_split Parameter

The `min_samples_split` is the minimum number of samples required to split the node in order to build the decision tree. The smaller the number, the more branches we got, the decision boundary will be more complex which may lead to overfitting.

If the `min_samples_split = 2`, the branches will split until it only leave us with `1` node (leaf) at the end of the branch.

![Minimum samples split](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/terrain_classifier/plot.png)

Decision tree with `min_samples_split = 2`:

![Decision Tree min_samples_split2](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/terrain_classifier/min_samples_split2.png)

```txt
min_samples_split = 2 (default)

🤖 Accuracy: 0.908
```

Decision tree with `min_samples_split = 50`:

![Decision Tree min_samples_split2](https://raw.githubusercontent.com/risan/intro-to-machine-learning/master/decision_tree/terrain_classifier/min_samples_split50.png)

```txt
min_samples_split = 50

🤖 Accuracy: 0.912
```

## Email Classifier with Decision Tree

Type the following command on your terminal to run the email classifier:

```shell
# Go to the project directory.
$ cd /path/to/intro-to-machine-learning

# And run it with Python
$ python decision_tree/email_classifier
```

The result of using Decision Tree classifier with `min_samples_split = 40`:

```txt
# min_samples_split = 40
⏱ Training time: 76.246s
⏱ Predicting time: 0.043s
🤖 Accuracy: 0.9772468714448237

👦 Total emails from Chris: 867
👧 Total emails from Sarah: 891
```

The above results is from using 3,785 features (10% from all available features). Let's decrease the number of feature to 1% (379 features) to speed up the training.

```txt
# min_samples_split = 40
# features percentile = 1% (379)
⏱ Training time: 4.713s
⏱ Predicting time: 0.002s
🤖 Accuracy: 0.9670079635949943

👦 Total emails from Chris: 887
👧 Total emails from Sarah: 871
```

Decreasing the number of features to 1% drastically speeding up our training and predicting process. The accuracy is only drop 0.01, awesome!
