# edaviz
edaviz - Python library for Exploratory Data Analysis and Visualization in Jupyter Notebook or Jupyter Lab

- [Installation](#Installation)
- [Usage](#Usage)
- [API](#API)

# Installation

> Currently, we are in private access mode. You will receive the initial `pip install` command and your license directly from us when you have been chosen for early access.

The installation consists of 4 steps:

## 1) pip install
From the terminal, you can install edaviz with the pip command that you received from us via mail.


## 2) setup jupyter extensions
### Jupyter Notebook
From the terminal, you need to setup the Jupyter Notebook extensions via the following commands:
```
jupyter nbextension enable --py widgetsnbextension
jupyter nbextension enable --py qgrid
```
If this worked for you, you can __continue with step 3__.

### Jupyter Lab
> Small tip: The setup process with Jupyter Notebook is faster and easier than with Jupyter Lab. Also, JupyterLab 1.0.1 does not work yet, because there are still dependency incompatibilities. However, it works with the latest JupyterLab version before 1.0

If you use Jupyter Lab, you need to use the following commands from your terminal. The Jupyter Lab Extension manager will not work because the version numbers cannot be fixed yet.

> Important: Jupyter Lab extensions require that you have nodejs installed on your computer.
<a href="https://jupyterlab.readthedocs.io/en/stable/user/extensions.html" target="_blank">Official install guide for Jupyter Lab Extensions</a>

```
jupyter labextension install @jupyter-widgets/jupyterlab-manager@0.38 --no-build
jupyter labextension install plotlywidget@0.11.0 --no-build
jupyter labextension install @jupyterlab/plotly-extension@0.18.2 --no-build
jupyter labextension install qgrid@1.1.1 --no-build
jupyter lab build
```

If this worked for you, you can __continue with step 3__.

If you have troubles with the correct display of the edaviz widgets, please validate again, that you have the right versions of the pip dependencies installed because some version combinations are incompatible:

From the terminal, you need to enter: (maybe you use pip3?)
```
pip install jupyterlab==0.35.6
pip install ipywidgets==7.4.2
pip install plotly==3.10.0
```

## 3) start edaviz in Jupyter
From the terminal, start your Jupyter Notebook (or Jupyter Lab):
```
jupyter notebook
```

From a Jupyter cell, you need to start edaviz via the following Python code:
```
import edaviz as eda
df = eda.get_titanic_df()  # sample pandas dataframe
df
```


## 4) enter your license
If everything works fine, edaviz asks you for a license.
Please enter the license key which you received from us.


# Usage

First, start your Jupyter Notebook from the terminal:
```
jupyter notebook
```

Then, execute the following code in a Jupyter cell:
```
import edaviz as eda
df = eda.get_titanic_df()  # sample pandas dataframe
df
```
> Info: edaviz only works within Jupyter Notebook or Jupyter Lab. Currently, you cannot use it on other platforms, e.g. via the command line or in PyCharm etc

# API

You will be able to discover most of the library functions via the `eda.plot(df)` widget.
If you want to dig deeper into our docs, you can find them [here](https://github.com/tkrabel/edaviz/blob/master/docs/api.md).

## 5 most important functions:
- The best API is no API. Just execute `df` (or the name of your pandas dataframe object) and see what happens. If you imported edaviz, it will add some interactive visualizations to your pandas dataframe
- `eda.plot(df)` shows an overview widget of a given dataframe. This is the main entry point of the edaviz library
- `eda.plot(df, x)` shows a suitable univariate summary of a given column, e.g. `eda.plot(df, "Age")`
- `eda.plot(df, x, y)` shows a suitable bivariate plot of the two columns, e.g. `eda.plot(df, "Survived", "Age")`.
- `eda.patterns(df)` answers the question: _"between which columns exist predictive patterns?"_

The big advantage of `eda.plot` is that you do not have to specify the chart type because a best practice visualization is inferred based on the column data types.