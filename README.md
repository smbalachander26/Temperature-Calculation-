# Temperature-Calculation-
Calculates a risk factor using temperature data taken at the county level. The mean and standard deviation of each county is calculated and used in a z-score formula to compute the individual values. The z-scores are stored in an Excel spreadsheet alongside their respective county (represented by a FIPS code).

import numpy as np
import pandas as pd

# Load the Excel file into a DataFrame
df = pd.read_excel('DB4.xlsx')
print("Success!")
display(df.head())

df.rename(columns={'FIPS': 'fips_code'}, inplace=True)

# 'Fips_code' is treated as a string
df['fips_code'] = df['fips_code'].astype(str)

print("DataFrame after renaming FIPS column and converting fips_code to string:")
display(df.head())

# Calculates the mean of 'Avg_Temp' for each county
county_mean_temp_df = df.groupby('fips_code')['Avg_Temp'].mean().reset_index()
county_mean_temp_df.rename(columns={'Avg_Temp': 'county_mean_temp'}, inplace=True)

# Calculates the overall mean and standard deviation
overall_mean_of_county_means = county_mean_temp_df['county_mean_temp'].mean()
overall_std_of_county_means = county_mean_temp_df['county_mean_temp'].std()

# Calculates the Z-score for each county's mean temperature
county_mean_temp_df['z_score'] = county_mean_temp_df.apply(
    lambda row: (row['county_mean_temp'] - overall_mean_of_county_means) / overall_std_of_county_means
    if overall_std_of_county_means != 0 else 0, axis=1
)

print("DataFrame with calculated Z-scores for each county's mean temperature:")
display(county_mean_temp_df.head())

# Creates a new DataFrame with only fips_code and z_score
final_z_score_df = county_mean_temp_df[['fips_code', 'z_score']].copy()

# Saves the new DataFrame to a CSV file
final_z_score_df.to_csv('county_mean_temp_z_scores.csv', index=False)

print("New spreadsheet 'county_mean_temp_z_scores.csv' created successfully. Here's a preview:")
display(final_z_score_df.head())
