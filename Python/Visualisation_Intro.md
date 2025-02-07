  - From "Data Visualization with Python"@Coursera
  
# Overview
  - Formats: varies, from basic charts/grapth to interactive dashboards/infographics
  - Why: Ease of understand/communicate complex data, or spot trends/patterns
  - Common libaries: Matplotlib, Pandas, Seaborn, Folium, Plotly and PyWaffle
    - Matplotlib: general purpose plot library
	- Pandas: often use for data manipulation, but plotting built upon Matplotlib: good for exploratory analysis
	- Seaborn: also built upon Matplotlib, great for statistical visualizations
	- Folium: excel at geospatial data
	- Plotly & Dash: best for interactive, dashboard on the web
	- PyWaffle: visualize proportional representation using squares or rectangles  
	
# Type of plots

  - Line plot (line chart): a series of data points connected by straight lines
    - Good with continuous data --> showing trend over time
	- Need care of scale --> can cause misleading
  - Bar plot (bar chart): rectangular bars, where height/length represents the magnitude of data
    - Best for compare different categories/groups
	- Choices of axis scale/categories --> misleading
  - Scatter plot: Use Cartesian coordinates to display a collection of data points based on 2 variables
    - Good for investigate relationship of 2 variables: patterns/trends/correlation, clusters vs. outliers
	- NOTE: Outliers may hinder visibility of normal data points
  - Box plot (Box & whisker plot): consists of box for inter-quartile range (IQR), inside-line for median, and external whiskers for range. Outliers are points beyond the whisker
    - Good for compare the distribution (spread & skewness) of continuous data across different categories/groups
	- Need to handling outliers, or risk distort the data
  - Histogram: Showing frequency of values split into groups as a collection of bars, each bar height represents the data count of that group
    - Good to see shape of data distribution & concentration (semmetric vs. skew vs. bi-modal, variability, outliers)
	- need care in selecting bins

# Treemap and Pivot charts

## Treemap
  - Displays hierarchical data using nested rectangles
  - Run directly from `plotly.express`, not run from `pandas.DataFrame`, but accept dataframe as input
  - Pros:
    - Most useful for visualizing large datasets where the hierarchical structure is crucial
	- Display data in an intuitive and space-efficient way
  - Sample
  ```
  fig = px.tremap(
    df,							# input dataframe
	path = ['Parent', 'Child'],	# List of colum names, from the root category to the leaves
    values = 'Values_col',		# value column
	title = 'Title string'
  )
  fig.show()
  ```

## Pivot Charts
  - Plot `pivot_table` dataframe
  - Use `pivot_table` to summarise and analysis of summarise data

# Matplotlib

## Intro
  - Has 3 main layer: Backend, Artist and Scripting (Pyplot)
  - Backend has 3 main interfaces:
    - FigureCanvas: the area where figure is drawn
	- Renderer: how to draw the figure
	- Events: handles user inputs (keyboard, mouse clicks, etc.)
  - Artist layer: hierarchy of object, from composite (Figure, Axis, etc) to basic (Line2D, Rectangle, etc)
    - Artist object (especially composite one) contains other Artists, which may contain other Artists
  - Good to know: Anatomy of a plot in Matplotlib: https://matplotlib.org/stable/gallery/showcase/anatomy.html
  
## Basic
  - Plot a dataframe/series using plot() function with plot type: param 'kind'
	- plot types: line, bar/barh, box, hist, pie, area, scatter (DataFrame only)
	- can also do `plot().line()` (i.e. use the plot type as the function of plot)
  - Pie: most useful for percentage, may "explode" specific pieces
    - Param `explode`: need an arrays of values with same length as data. Where value > 0, explode that piece out
  - Scatter: need `x` & `y` params: specify the variables for x & y axis
    - `s` param: may use by a third variables to show weight
  - Vertical/Horizontal bar: `bar` & `barh`
  - Histogram: may combined with numpy histograms so can have `xticks` at precise edge
    - Example:
	```
	count, edges = np.histogram(series, bins=5)
	series.plot(kind='hist', xticks=edges)
	```
  - Package `matplotlib.pyplt` allows use to modify components of the last figure
    - Can also interact with the "artist" object return from `plot()` directly
	```
	ax = df.loc[row_name, columns].plot(kind='line')
	
	plt.title('Title of plot')     # use pyplt
	ax.set_title('Title of plot')  # use the "artist" object
	```
  
## Sample

```
# we are using the inline backend
%matplotlib inline 

import matplotlib as mpl
import matplotlib.pyplot as plt

#print(plt.style.available)
mpl.style.use(['ggplot']) # optional: for ggplot-like style

# Plot a series
df.loc[row_name, columns].plot(kind='line')

plt.title('Title of plot')
plt.ylabel('Vertical axis')
plt.xlabel('Horizontal axis')

plt.show() # need this line to show the updates made to the figure
```

# Seaborn
  - More reference: https://seaborn.pydata.org/generated/seaborn.regplot.html
  - Built on top of matplotlib -> more advance statistical graph
  - Sample: regression plot
	```
	sns.regplot(data=df, x="weight", y="acceleration")
	```

# PyWaffle
  - Documents: https://pywaffle.readthedocs.io/en/latest/examples/formats_of_values.html
  - Creating waffle graph from a data matrix
  - Sample: 
    ```
    plt.figure(
	  FigureClass=Waffle,
	  rows=5,
	  columns=10,		# One of "rows"/"columns" can be ommitted, as it can be worked out from the total values
	  values={'Cat1': 30, 'Cat2': 16, 'Cat3': 4},
	  legend={'loc': 'upper left', 'bbox_to_anchor': (1, 1)}
    )
    ```

# Cheat sheets

## Pandas

  - Load CSV data: `pd.read_csv('filename.csv')`
  - Display first/last "n" rows: `df.head(n)` or `df.tail(n)`
  - Drop rows with missing values: `df_can.dropna()`
  - Fill missing values with a specified value: `df_can.fillna(0)`
  - Removing Duplicates: `df.drop_duplicates()`
  - Renaming Columns: `df.rename(columns={'old_name': 'new_name'})`
  - Selecting Columns: `df['column_name']` or `df.column_name`
    - Select multiple columns: `df[['col_name_1', 'col_name_2']]`
  - Filtering Rows: `df[df['column'] > value]`
  - Applying Functions to Columns; `df['column'].apply(function_name)`
    - Example: `df_can['Age'].apply(lambda x: x + 1)`
  - Creating New Columns: `df['new_column'] = expression`
  - Grouping and Aggregating:  `df.groupby('column').agg({'col1': 'sum', 'col2': 'mean'})`
  - Sorting Rows: `df.sort_values('column', ascending=True/False)`
  - Checking for Null Values: `df.isnull()`
  - Selecting Row by Index: `df.iloc[index]`
    - `df.iloc[start:end]`: select rows in index range
  - Selecting Row by Label: `df.loc[label]`
    - `df.loc[start:end]`: Select rows in a specified label/index range	
  - Summary Statistics: `df.describe()`
  
## Visualization Libraries

| Library | Main Purpose | Key Features | Programming Language | Level of Customization | Dashboard Capabilities | Types of Plots Possible |
| --- | --- | --- | --- | --- | --- | --- |
| Matplotlib | General-purpose plotting | Comprehensive plot types and variety of customization options | Python | High | Requires additional components and customization | Line plots, scatter plots, bar charts, histograms, pie charts, box plots, heatmaps, etc. |
| Pandas | Fundamentally used for data manipulation but also has plotting functionality | Easy to plot directly on Panda data structures | Python | Medium | Can be combined with web frameworks for creating dashboards | Line plots, scatter plots, bar charts, histograms, pie charts, box plots, etc. |
| Seaborn | Statistical data visualization | Stylish, specialized statistical plot types | Python | Medium | Can be combined with other libraries to display plots on dashboards | Heatmaps, violin plots, scatter plots, bar plots, count plots, etc. |
| Plotly | Interactive data visualization | interactive web-based visualizations | Python, R, JavaScript | High | Dash framework is dedicated for building interactive dashboards | Line plots, scatter plots, bar charts, pie charts, 3D plots, choropleth maps, etc. |
| Folium | Geospatial data visualization | Interactive, customizable maps | Python | Medium | For incorporating maps into dashboards, it can be integrated with other frameworks/libraries | Choropleth maps, point maps, heatmaps, etc. |
| PyWaffle | Plotting Waffle charts | Waffle charts | Python | Low | Can be combined with other libraries to display waffle chart on dashboards | Waffle charts, square pie charts, donut charts, etc.