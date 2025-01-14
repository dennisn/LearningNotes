  - From "Data Visualization with Python"@Coursera
  
# Overview
  - Formats: varies, from basic charts/grapth to interactive dashboards/infographics
  - Why: Ease of understand/communicate complex data, or spot trends/patterns
  
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