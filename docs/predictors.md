# Predictors

edaviz enables you to quickly calculate the predictor scores for a given target column using `eda.predictors(df, "target_name")`. As always in edaviz, the calculation works with many different data types for the feature and the target because edaviz automatically adjusts the calculation.

The score is calculated training a Decision Tree with __1 feature__ trying to predict the target column. This means there are no interaction effects between the scores of various features. We chose to provide a univariate prediction score as a first quick hint on which variables are worth an initial exploration.

<img src="https://edaviz-assets.s3.eu-central-1.amazonaws.com/predictors_sample_titanic_survived_small.png" width=400 />

## Outline
- [General calculation](#general-calculation)
- [Learning algorithm](#learning-algorithm)
- [Data preprocessing](#data-preprocessing)
- [Score metrics](#score-metrics)
- [Normalized scores](#normalized-scores)
- [Predictive Power Score](#introducing-the-predictive-power-score-pps)
- [Visualizing predictor patterns](#visualizing-predictor-patterns)


## General calculation
Here is the summary of the most important calculation details:
- The score is calculated using only 1 feature trying to predict the target column. This means there are no interaction effects between the scores of various features. Note that this is in contrast to feature importance
- The score is calculated on the test sets of a 5-fold crossvalidation
- All rows which have a missing value in the feature or the target column are dropped
- In case that the dataset has more than 5,000 rows the score is only calculated on a random subset of 5,000 rows with a fixed random seed
- There is no grid search for optimal model parameters


## Learning algorithm
As a learning algorithm, we currently use a Decision Tree because the Decision Tree has the following properties:
- can detect any non-linear bivariate relationship
- good predictive power in a wide variety of use cases
- low requirements for feature preprocessing
- robust model which can handle outliers and does not easily overfit
- can be used for classification and regression
- can be calculated quicker than many other algorithms

We differentiate the exact implementation based on the data type of the target column:
- If the target column is numeric, we use the <a href="https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html" target="_blank">sklearn.DecisionTreeRegressor</a>
- If the target column is categoric, we use the
<a href="https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html" target="_blank">sklearn.DecisionTreeClassifier</a>


Please note that we prefer a general good performance on a wide variety of use cases over better performance in some narrow use cases. If you have a proposal for a better learning algorithm, please write us via info [at] edaviz [dot] com

However, please note why we actively decided against the following algorithms:
- Correlation or Linear Regression: cannot detect non-linear bivariate relationships without extensive preprocessing
- GAMs: might have problems with very unsmooth functions
- SVM: potentially bad performance if the wrong kernel is selected
- Random Forest/Gradient Boosted Tree: slower than a single Decision Tree
- Neural Networks and Deep Learning: slower calculation than a Decision Tree and also needs more feature preprocessing


## Data preprocessing
Even though the Decision Tree is a very flexible learning algorithm, we perform the following preprocessing steps if a column is categoric, which means that it has the pandas `dtype object`.
- If the __target column__ is categoric, we use the
<a href="https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.LabelEncoder.html" target="_blank">sklearn.LabelEncoder</a>
- If the __feature column__ is categoric, we use the
<a href="https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html" target="_blank">sklearn.OneHotEncoder</a>


## Score metrics
Based on the data type and cardinality of the target column, we use different scores for assessing the predictive power of the bivariate model.

__Important:__ Before explaining the scores in the different cases, you need to understand the following:

> You cannot evaluate model scores without comparison. You always need to compare your score to the best possible score and to a baseline score. Therefore, we will always describe the score of a perfect model and the score of a naive baseline model. Ideally, a new model will have a score in between those limits. This is especially important for the case of the F1 score described below.


### Regression - R²

In the case where the target column is numeric, we compute the R² as the score of the bivariate model. The R² is also referred to as [coefficient of determination](https://en.wikipedia.org/wiki/Coefficient_of_determination).
It has the following properties:
- The best possible score is 1. In this case the model perfectly predicts the target
- A constant model that always predicts the average value of the target, disregarding the input features, would get a R² score of 0, which is also the naive baseline as described below.
- A very bad model can have a value below 0 which is even worse than the naive baseline. A value below 0 is possible due to the technical calculation of the R² (please refer to the formula for more details). However, in order to reduce confusion, edaviz will set negative R² scores to 0 because the semantic insight is the same: the model does not have predictive power.

#### Baseline score for R²

> The baseline is always 0

As described above, the baseline model is a constant predictor that always predicts the average value of the target. This model will have an R² score of 0.

### Binary classification - ROC AUC

In the case where the target column is binary, we use the ROC AUC as the score of the bivariate model. ROC AUC stands for [Area Under the Receiver Operating Characteristic curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve). The ROC AUC score lies between 0 and 1, with 1 being the best possible score. A model that makes random predictions, disregarding the input, would get an AUC of 0.5.

#### Baseline score for ROC AUC

> The baseline is always 0.5

The baseline model is a random prediction model which has a score of 0.5.


### Multi-class classification - wF1

If the target has more than two classes, we compute the [weighted F1 score (wF1)](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html). The F1 score can be interpreted as a weighted average of the precision and recall, where an F1 score reaches its best value at 1 and worst score at 0. The relative contribution of precision and recall to the F1 score are equal. The weighted F1 takes into account the precision and recall of __all__ classes weighted by their support as described [here](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)

#### Baseline score for weighted F1

> The baseline is variable based on the target column

As a baseline score, we calculate the weighted F1 score of a model that always predicts the most common class of the target column. The score of this model has different values based on the skewedness of the value counts in the target column. For example, the baseline might be 0.2 for one dataset and 0.7 for another. Therefore, it is very important to always compare the F1 score of a new model to the baseline score for this target column because a value of 0.7 might be good for the first dataset but irrelevant for the second.


## Normalized scores
As described in the previous section on [Score metrics](#score-metrics), you always need to compare the score of a given model to 2 other scores:
- the score of a perfect model which is the upper limit
- the score of a naive baseline model which is the lower limit

If you compare your score to those 2 limits, you actually calculate a normalization in your head. You basically transform the score towards a range from 0 to 1.

Here are some examples for a normalized score. Please note that the upper limit is always 1 and therefore we don't explicitly mention it:
- For a numeric target:
    - let the model R² be 0.6
    - the baseline is 0
    - thus, the normalized score is also 0.6 = (0.6 - 0) / (1 - 0)
- For a binary target:
    - let the model ROC AUC be 0.7
    - the baseline is 0.5
    - thus, the normalized score is 0.4 = (0.7 - 0.5) / (1 - 0.5)
- For a multiclass target:
    - let the model wF1 be 0.85
    - let the baseline be 0.8
    - thus, the normalized score is 0.25 = (0.85 - 0.8) / (1 - 0.8)

Those normalized scores have an interesting property and they can be used in a new way. Therefore, we also call them Predictive Power Scores.


## Introducing the Predictive Power Score (PPS)

After the normalization, the scores have the same value range because they all range from 0 to 1.
- If the score is 0, the model does not have more predictive power than a naive baseline.
- If the score is 1, the model has perfect predictive power.

Due to this normalization, we abstract away the underlying details of the original model metrics. This enables us to have a uniform semantic interpretation of the normalized scores.

The score answers the question:
> How much predictive power does this model have?

Thus, we also call those normalized scores __Predictive Power Scores__.


## Visualizing predictor patterns

Since the Predictive Power Scores (PPS) all have the same value range, we can visualize them in a heatmap for multiple different targets. This enables us to detect novel relationships. You can try it on your own datasets but we will also create some more tutorials on this topic in the future.

> Please note that the heatmap enables you to visually compare PPS between various targets. However, only the PPS for the same target variable are comparable in a strict mathematic sense. The PPS between target variables are not comparable in a strict mathematic sense. Nevertheless, the visualization is useful because it enables new insights as long as the practitioner is aware of this restriction.



