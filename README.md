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

#
# DANIELLE 
  LOOK >>>> # Using the aggregation method, produce the same summary statistics in a single line.
#

# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using Pandas.
# complete_df[complete_df['Drug Regimen'] == 'Ramicane'].plot.bar(x='Drug Regimen', y='Timepoint')
# complete_df.plot.bar(x="Drug Regimen", y="Timepoint")

for drug in complete_df['Drug Regimen'].unique():

# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using pyplot.

for drug in complete_df['Drug Regimen'].unique():
    
    drug_timepoint = complete_df[complete_df['Drug Regimen'] == drug]['Timepoint'].value_counts()
    x = drug_timepoint.index.tolist()
    y = drug_timepoint.values.tolist()
    
    plt.bar(x, y, width=2)
    plt.title(drug)
    plt.show()
    print(drug)
    
    # Generate a pie plot showing the distribution of female versus male mice using Pandas

complete_df['Sex'].value_counts().plot.pie()

# Generate a pie plot showing the distribution of female versus male mice using pyplot

complete_df['Sex'].value_counts().plot.pie()

mouse_gender = complete_df['Sex'].value_counts()
x = drug_timepoint.index.tolist()
y = drug_timepoint.values.tolist()

plt.pie(y, labels=x, autopct='%1.1f%%')
plt.show()

# Calculate the final tumor volume of each mouse across four of the treatment regimens:  
# Capomulin, Ramicane, Infubinol, and Ceftamin

final_tumor_vol = {'Capomulin': [],
'Ramicane': [],
'Infubinol': [],
'Ceftamin': [],
'Mouse ID': []}
                   
for mouse_id, time in complete_df.groupby('Mouse ID')['Timepoint'].max().iteritems():
    for drug in ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']:
        try:
        # print(mouse_id, time, drug)
            tumor_size = complete_df[(complete_df['Mouse ID'] == mouse_id) & (complete_df['Timepoint'] == time) & (complete_df['Drug Regimen'] == drug)]['Tumor Volume (mm3)'].values[0]
        except:
            tumor_size = None
        final_tumor_vol[drug].append(tumor_size)
    final_tumor_vol['Mouse ID'].append(mouse_id)
    
# Start by getting the last (greatest) timepoint for each mouse
complete_df.groupby('Mouse ID')['Timepoint'].max()

# Merge this group df with the original dataframe to get the tumor volume at the last timepoint
#create new df with calculations from above

final_tumor_vol = pd.DataFrame(final_tumor_vol)
print(final_tumor_vol)

final_tumor_vol.set_index("Mouse ID")

final_ram = final_tumor_vol[final_tumor_vol['Ramicane'].notnull()] ['Ramicane']
final_cap = final_tumor_vol[final_tumor_vol['Capomulin'].notnull()] ['Capomulin']
final_inf = final_tumor_vol[final_tumor_vol['Infubinol'].notnull()] ['Infubinol']
final_cef = final_tumor_vol[final_tumor_vol['Ceftamin'].notnull()] ['Ceftamin']

q1 = final_ram.quantile(.25)
q3 = final_ram.quantile(.75)
mask = final_ram.between(q1, q3, inclusive='both')
print(final_ram[mask])

# Put treatments into a list for for loop (and later for plot labels)

complete_df['Drug Regimen'].unique()


# Create empty list to fill with tumor vol data (for plotting)
tumor_vol = []

# Calculate the IQR and quantitatively determine if there are any potential outliers. 

    
    # Locate the rows which contain mice on each drug and get the tumor volumes
    
    
    # add subset 
    
    
    # Determine outliers using upper and lower bounds
    
    # Select the first quantile
q1 = df['col'].quantile(.25)

# Select the third quantile
q3 = df['col'].quantile(.75)

# Create a mask inbeetween q1 & q3
mask = df['col'].between(q1, q3, inclusive=True)

# Filtering the initial dataframe with a mask
iqr = df.loc[mask, 'col']
    complete_df[complete_df['Drug Regimen'] == drug]['Timepoint'].value_counts().plot.bar(title=drug, legend=True)
    plt.show()

