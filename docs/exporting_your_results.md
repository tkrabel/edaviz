# How to export your results in Jupyter notebooks with edaviz widgets

There are two types of exports possible:
1. [static HTML export](#1-static-html-export) which is easy and fast
2. [interactive Jupyter Notebook export](#2-interactive-jupyter-notebook-export) which is interactive and powerful


## 1) Static HTML export
> PLEASE NOTE: currently .html exports are __only possible via Jupyter Notebook__ and not via Jupyter Lab because there are some dependency incompatibilities.

A static .html export can only render static content and it cannot perform background calculations because no Python Kernel is available. For example, this means:

- The edaviz histogram can be shown but the auto-rebinning does not work.
- The .html export only contains widgets which you already loaded. So, if you did not calculate the predictors for a given column, then this widget will not be available in the export.
- If the plotly widget is interactive and shows some tooltips, those will still work. But if the interaction needs some further calculation, this will only work with a full interactive jupyter notebook export (see section 2 for more info).

This export is helpful if you know which plots you want to share and if you already generated them. Sometimes, there might be small styling differences if some .css files are missing in the export.

Here is how you can generate a static .html export in 3 steps:


### 1.1) Install the latest version of nbconvert

You need to install an upgraded nbconvert library.
From the terminal, execute the following pip (or pip3) command:

```
pip install nbconvert==5.5.0
```


### 1.2) Create the .html export

Create your Notebook via the Jupyter Notebook interface. Unfortunately, this step is currently not possible with Jupyter Lab.

After you finished creating your notebook, go to the main menu "Widgets" and press "Save Notebook Widget State". Let's assume your notebook is called `my_notebook.ipynb`

Afterwards, from the terminal, execute the following command:
```
jupyter nbconvert --to html my_notebook.ipynb
```

This will generate a `my_notebook.html` file in your current directory.


### 1.3) Share the .html file

You can share the .html file to anyone. In order to view the file, they need 2 things:
1. a web browser
2. __an internet connection__ because the .html file has some dependencies which need to be downloaded from the internet

Also, please note that it might take around 2-10 seconds after opening the file until the widgets are displayed. This delay is the time until the browser downloads the needed javascript files. If your internet connection is fast, this might only take 1 second.


## 2) Interactive Jupyter Notebook export

This type of export provides a full Python Kernel. This means, that the observer can use the full power of Jupyter Notebooks and generate new calculations.

Since edaviz is build on top of ipywidgets, it is possible to generate an interactive export of your Jupyter Notebook as a live web app, using voila. Please note, that voila is still under development but you can already find tutorials on the repository.

https://github.com/QuantStack/voila

