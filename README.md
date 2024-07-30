# Matplotlib-Challenge
---

### Analysis

Analysis Observations:

1. The highest number of mice in the study are treated using Capomulin. while the lowest number of mice in the study are treated using Propriva.
2. The Drug Ramicane shows the greatest reduction of tumor volume based on mean and final tumor volume.
3. In reference to the box plot across four regimens of interest & summary statistics table, the final tumor volume treated by Capomulin and Ramicane trended lower than the others. Ramicane demonstrated lower final tumor volume, however it was used with a lower treatment than that of Capomulin. Fair results require equal treatments and variables.
4. Capolmulin was used to treat Mouse ID l509 and the tumor volume seems to decrease with time. Sampling also charts that five mice treated with the same regimen supports the assumption that Capomulin could reduce the size of tumor volume.
5. The correlation between Capomulin with average weight and final tumor volume, the value is 0.84, meaning that there is a strong positive correlation between the two variables. The r-squared value of 0.71 indicates that the final tumor volume could be predicted by using the average weight of a mouse.
The Drug Ramicane shows the greatest reduction of tumor volume based on mean and final tumor volume.
6. There is a strong and statistically significant positive correlation between mouse weight and average tumor volume for mice treated with Capomulin.


# Dependencies and Setup
import matplotlib.pyplot as plt,pandas as pd,scipy.stats as st,os,pandas as 
from scipy.stats import linregress
# Study data files
mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

# Read the mouse data and the study results
mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)

# Combine the data into a single DataFrame
merged_data_df = pd.merge(mouse_metadata , study_results ,on = "Mouse ID")

# Display the data table for preview
merged_data_df.head()
# Checking the number of mice.
count_0f_mice=merged_data_df["Mouse ID"].value_counts()
# Our data should be uniquely identified by Mouse ID and Timepoint
# Get the duplicate mice by ID number that shows up for Mouse ID and Timepoint.

duplicate_ID= merged_data_df.loc[merged_data_df.duplicated(subset=['Mouse ID','Timepoint',]),'Mouse ID'].unique() 
# Optional: Get all the data for the duplicate mouse ID.
duplicates_ID_df= merged_data_df.loc[merged_data_df["Mouse ID"] == "g989", :] 
# Checking the number of mice in the clean DataFrame.
unique_mice_count = len(clean_df['Mouse ID'].unique())
# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen

# Use groupby and summary statistical methods to calculate the following properties of each drug regimen:
# mean, median, variance, standard deviation, and SEM of the tumor volume.
# Assemble the resulting series into a single summary DataFrame.
summary_stat_mean = clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].mean()
summary_stat_median = clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].median()
summary_stat_variance = clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].var()
summary_stat_sd = clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].std()
summary_stat_sem = clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].sem()
summary_stat_df=pd.DataFrame({"Mean":summary_stat_mean,"Median":summary_stat_median,"Variance":summary_stat_variance,"Standard Deviation":summary_stat_sd ,"Sem":summary_stat_sem})
summary_stat_df
# A more advanced method to generate a summary statistics table of mean, median, variance, standard deviation,
# and SEM of the tumor volume for each regimen (only one method is required in the solution)

# Using the aggregation method, produce the same summary statistics in a single line
summary_stat_agg=clean_df.groupby("Drug Regimen")["Tumor Volume (mm3)"].agg(["mean","median","var","std","sem"])
summary_stat_agg
# Generate a bar plot showing the total number of rows (Mouse ID/Timepoints) for each drug regimen using Pandas.
# Generate a bar plot showing the total number of rows (Mouse ID/Timepoints) for each drug regimen using pyplot.
drug_regime_counts=clean_df.groupby("Drug Regimen").count()["Mouse ID"]
drug_regime_counts.plot(kind="bar",figsize=(10,6),color="skyblue")
plt.title("Total Number of Rows for Each Drug Regimen")
plt.xlabel("Drug Regimen")
plt.ylabel("Number of Rows")
plt.show
# Generate a pie chart, using Pandas, showing the distribution of unique female versus male mice used in the study
# Get the unique mice with their gender
# Make the pie chart
sex_counts=clean_df["Sex"].value_counts()
sex_counts.plot(kind="pie",autopct="%1.1f%%",startangle=140,colors=["#66b3ff","#ff9999"])
plt.title("Distribution of Unique Female vs Male Mice")
plt.axis("equal")
plt.show()
# Calculate the final tumor volume of each mouse across four of the treatment regimens:
# Capomulin, Ramicane, Infubinol, and Ceftamin

# Start by getting the last (greatest) timepoint for each mouse

# Merge this group df with the original DataFrame to get the tumor volume at the last timepoint

final_tumor_vol = clean_df.groupby(['Drug Regimen', 'Mouse ID']).last()[['Timepoint', 'Tumor Volume (mm3)']]
final_tumor_df = final_tumor_vol.reset_index()
# Put treatments into a list for for loop (and later for plot labels)
regimen = ["Capomulin","Ramicane","Infubinol","Ceftamin"]

# Create empty list to fill with tumor vol data (for plotting)
final_tumor_vol_list =[]

# Calculate the IQR and quantitatively determine if there are any potential outliers.
for drug in regimen:

    # Locate the rows which contain mice on each drug and get the tumor volumes
    final_tumor_vol =  final_tumor_df.loc[final_tumor_df["Drug Regimen"] == drug, "Tumor Volume (mm3)"]

    # add subset
    final_tumor_vol_list.append(final_tumor_vol)
    quartiles = final_tumor_vol.quantile([0.25,0.5,0.75])
    lowerq = quartiles[0.25]
    upperq = quartiles[0.75]
    mean = final_tumor_vol.mean()
    iqr = upperq-lowerq
    lower_bound = lowerq -(1.5*iqr)
    upper_bound = upperq +(1.5*iqr)
    # Determine outliers using upper and lower bounds
    outliers = final_tumor_vol.loc[(final_tumor_vol < lower_bound)
    | (final_tumor_vol >upper_bound)]
    # Print out the result
    print(f"{drug} Regimen")
    print(f"Final tumor volume(mm3) for {drug} below {round(lower_bound,2)} could be outliers.")
    print(f"Final tumor volume(mm3) for {drug} above {round(upper_bound,2)} could be outliers.")
    print(f"Average tumor volume(mm3) for {drug} is: {round(mean,2)}")
    print(f"Number of outliers for Capomulin is: {outliers.count()}")
    if outliers.count() > 0:
        for i in outliers.index:
            print(f'Outlier final tumor volume(mm3) is {round(outliers[i], 2)}')
    print('----------------------------------------------------------')
    # Generate a box plot that shows the distribution of the tumor volume for each treatment group.
fig1, final_tumor_plot = plt.subplots()
final_tumor_plot.boxplot(final_tumor_vol_list, labels=regimen, flierprops={'markerfacecolor':'r','markersize': 10})

# Set x/y label, title
final_tumor_plot.set_title('Final Tumor Volume \n(Four Treatment Regimens)', fontweight='bold')
final_tumor_plot.set_xlabel('Drug Regimen', fontweight='bold')
final_tumor_plot.set_ylabel('Final Tumor Volume (mm3)', fontweight='bold')

# Save box plot graph to Images folder
output_directory="images"
output_path = os.path.join(output_directory, 'final_tumor_boxplot.png')
os.makedirs(output_directory,exist_ok=True)
plt.tight_layout()
plt.savefig(output_path)

# Generate a line plot of tumor volume vs. time point for a single mouse treated with Capomulin
# Filter the data for mice treated with Capomulin
capomulin_data = clean_df[clean_df['Drug Regimen'] == 'Capomulin']

# Select a single mouse treated with Capomulin
mouse_id = capomulin_data['Mouse ID'].unique()[0]
single_mouse_data = capomulin_data[capomulin_data['Mouse ID'] == mouse_id]

# Generate the line plot
plt.figure(figsize=(10, 6))
plt.plot(single_mouse_data['Timepoint'], single_mouse_data['Tumor Volume (mm3)'], marker='o')

