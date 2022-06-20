# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import scipy.stats as st

# Study data files
mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

# Read the mouse data and the study results
mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)

# Combine the data into a single dataset
# Display the data table for preview
complete_df = pd.merge(mouse_metadata, study_results, how = "left", on = ["Mouse ID"])
complete_df

# Checking the number of mice.
len(mouse_metadata ['Mouse ID'].unique())

# Getting the duplicate mice by ID number that shows up for Mouse ID and Timepoint. 
complete_df[complete_df.duplicated(subset=['Mouse ID', 'Timepoint'])]

# Optional: Get all the data for the duplicate mouse ID. 

# Create a clean DataFrame by dropping the duplicate mouse by its ID.
complete_df.drop_duplicates(subset=['Mouse ID', 'Timepoint'], keep='first', inplace=True)

# Checking the number of mice in the clean DataFrame.
complete_df

# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen

# Use groupby and summary statistical methods to calculate the following properties of each drug regimen: 
# mean, median, variance, standard deviation, and SEM of the tumor volume. 

complete_mean = complete_df.groupby(by='Drug Regimen')['Tumor Volume (mm3)'].mean()
complete_std = complete_df.groupby(by='Drug Regimen')['Tumor Volume (mm3)'].std()
complete_sem = complete_df.groupby(by='Drug Regimen')['Tumor Volume (mm3)'].sem()
complete_variance = complete_df.groupby(by='Drug Regimen')['Tumor Volume (mm3)'].var()
complete_median = complete_df.groupby(by='Drug Regimen')['Tumor Volume (mm3)'].median()

# Assemble the resulting series into a single summary dataframe.

complete_stats = pd.DataFrame([complete_mean, complete_std, complete_sem, complete_variance, complete_median])
complete_stats
print(complete_mean)
print(complete_stats)
complete_stats.index = ['mean', 'std', 'sem', 'var', 'median']
complete_stats

# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen
complete_df.groupby('Drug Regimen')['Tumor Volume (mm3)'].agg(['mean', 'std', 'var', 'sem', 'median']).T

# Using the aggregation method, produce the same summary statistics in a single line
