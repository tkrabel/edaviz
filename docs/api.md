# Before you read the docs

We implemented edaviz in a way so that it is as user-friendly as possible. The best way to learn the api is to call `eda.plot(df)` and click through the tabs. For each module, we display the code that produced the output. Simply copy-paste the code into another Jupyter cell if you want to play around with the functions.

## edaviz.plot(df, x=None, y=None, target=None)

Shows either an overview of the whole data frame, a univariate or a bivariate plot.

#### Example

```python
import edaviz as eda
df = eda.get_titanic_df()

# overview case
eda.plot(df)                       # for general overview
eda.plot(df, target="Survived")    # for overview with focus on a target

# univariate summary
eda.plot(df, "Age")                # shows a univariate summary of the "Age" column

# bivariate plot
eda.plot(df, "Sex", "Survived")    # shows a bivariate plot of "Survived" against "Sex"
```

## eda.patterns(df)

Shows an interactive heatmap comparing all columns with each other. It is like a correlation matrix, but uses a [predictive power score](https://github.com/tkrabel/edaviz/blob/master/docs/predictive_power_score.md) instead.

#### Example

```python
import edaviz as eda
df = eda.get_titanic_df()

eda.patterns(df)
```

## eda.correlations(df)

'eda.correlations' shows an interactive correlation matrix. By clicking on an element of the matrix, you can visualize the bivariate relationship between two columns.

#### Example

```python
import edaviz as eda
df = eda.get_titanic_df()

eda.correlations(df)
```

## eda.predictors(df, target)

This function let's you inspect the best univariate predictors for a given target variable.

#### Example

```python
import edaviz as eda
df = eda.get_titanic_df()

eda.predictors(df, target="Survived")
```

## eda.columns(df)

`eda.columns()` provides you with a graphical interface that allows you to quickly look at univariate summaries of your columns.
Since it is a correlation matrix, columns of non-numeric types (such as strings) are excluded from the computation.

#### Example

```python
import edaviz as eda
df = eda.get_titanic_df()

eda.columns(df)
```
