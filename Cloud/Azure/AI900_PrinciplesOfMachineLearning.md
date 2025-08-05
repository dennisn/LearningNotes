# AI-900: Fundamental Principles of Machine Learning on Azure

## Introduction to Machine Learning
  - Machine learnings: a technique that uses mathematics and statistics to create a model that can predict unknown values
  - Dataset: collections of historical data
  - A machine learning model: a file that has been trained with data and an algorithm to recognize patterns
    + Once trained, it can be deployed to ingest data and start making predictions
    + the ingested data for prediction: called "**features**"
    + the prediction data: called "**label**"
  - Azure automated machine learning: allow selection of a provided dataset, picking a label for prediction to train the model --> ready to deploy and use
  - Azure Machine Learning Designer: create a pipeline to train ML model with drag-and-drop interface, and pre-packaged modules

## Types of Machine Learning
  - Regression: historical data of features & label --> predict a numerical value
  - Classification: to predict which category or class an item belongs to
    + Binary classification: yes/no choice
    + Multi-class classification: multiple choices
  - Binary classification: also use historical data with features & label for training
  - Clustering: group similar items into clusters based on their features --> Un-supervised learning

### Supervised vs Un-Supervised Learning
  - Supervised Learning: trained from data with known label --> to predict something
  - Un-supervised learning: data doesn't have a known label set --> to group similar items

### Deep Learning
  - Uses a structure of artificial neural networks --> allows machines to train itself
  - Differentiate to Machine Learning:
    + Use large amount of datasets --> requires high-end machines
    + learns features and can create new features
    + Slow to train
    + Output: can have multiple formats
  - Usage:
    + identifying patterns in unstructured data
    + image caption generation
    + automatic text & speech translation

## Training, Validation, and Datasets
  - Data preparation: 
    + Cleaning data: handle missing or null values
    + Normalisation: convert values to be on the same scales (e.g. normally from 0-1) to avoid bias on large value features
  - Training our models: need to decide on the training algorithms, which are pre-packaged for user to choose
  - Validation: common practice to split your data into training & validation sets --> enable us to evaluate the model performance
  - Evaluation: using metrics about the errors, or matching of predicted values ==> used in Evaluate Model module in ML Designer

## Classification models

## Clustering models
