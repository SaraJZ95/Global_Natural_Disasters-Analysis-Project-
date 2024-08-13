
# Analyzing Global Natural Disasters: A Historical Perspective

![Natural Disaster](Images/Disaster-2023.jpg)

## Introduction:
Our study aims to analyze global natural disasters, spanning several decades, to understand their frequency, impact, and distribution. By leveraging historical data on various types of disasters, we aim to draw insights that can aid in better preparedness and response strategies.

## Data Overview:
The dataset we are working with is a comprehensive collection of disaster events from different countries over the years. Each row represents a unique disaster event, with columns capturing a range of information including the year, type, subtype, and impact metrics such as deaths, injuries, and economic damages. Here are some of the key columns in the dataset:
- **Year:** The year the disaster occurred.
- **Disaster Type:** Categorization of the disaster (e.g., Flood, Storm).
- **Total Deaths:** Number of deaths caused by the disaster.
- **Total Damages ('000 US$):** Economic impact measured in thousands of US dollars.
- **Total Affected:** Number of people affected by the disaster.

## Technology: 
* We applied Python libraries such as Pandas for data manipulation, Matplotlib for visualizations, and hvPlot for interactive plotting. Also, we used the Geoapify API key for geocoding to improve our geographic analysis.

## Data Cleaning and Preparation:
To ensure the dataset is ready for analysis, several steps were taken:
1. **Dropping Unnecessary Columns:** 
* We removed columns that were not needed for the analysis. This helps focus on the relevant data and improves processing speed.
* Columns such as 'Glide', 'Seq', 'Disaster Subtype', 'Event Name', etc., were removed because they were not necessary for the planned visualizations and analyses.

2. **Handling Missing Values:**
* Missing values in 'Total Damages' and 'Total Effective' were converted to numeric, setting errors to NaN. This is crucial for performing any sum or aggregation operations on these columns.

3. **Converting Data Types:** 
* Ensured that the 'Total Damages' and 'Total Effective' columns are numeric, which is essential for aggregation functions used in determining the most destructive disasters.

4. **Feature Engineering:** 
* Created new features where necessary, such as 'Latitude' and 'Longitude' for geografical plotting.

5. **Ensuring Data Integrity:**
* Checking for any duplicates in the dataset that could skew the results, especially when counting occurrences of disasters.
* Ensuring the consistency of categorical data, like the names of disasters, to avoid different labels for the same type of disaster.


## Exploratory Data Analysis:
### 1. Temporal Analysis:
* We started by analyzing the total count by disater type over all decades. A Bar Chart was created to visualize the total number of each disater type.

```python
# Plotting using matplotlib for each Disaster Type
plt.barh(disaster_counts['Disaster Type'], disaster_counts['total'], color=plt.colormaps.get_cmap('viridis')(range(len(disaster_counts))))
plt.xlabel('Total Disaster Count')
plt.ylabel('Disaster Type')
plt.title('Total Count by Disaster Type')

# Add text annotations for each bar
for index, value in enumerate(disaster_counts['total']):
    plt.text(value, index, str(value), ha='left', va='center', fontsize=8)

# Save the figure
plt.savefig("output_data/Fig1.png", bbox_inches='tight')

# Show figure
plt.show()
```

## Next, we focus on the three most significant disasters.

* and also plotting the frequency of disasters over the years to understand trends and patterns. A Cluster bar Chart was created to visualize the number of disasters occurring each year.

```python
#plot a cluster bar chart over the years against count of disater with different colors in each column
#which disaster was most frequent
disaster_counts = clean_disaster_metadata.groupby(['Year', 'Disaster Type']).size().unstack(fill_value=0)

# Plotting
fig, ax = plt.subplots(figsize=(12, 7))

# Create a stacked bar chart
disaster_counts.plot(kind='bar', stacked=True, ax=ax, colormap='tab20')

# Add labels and title
ax.set_title('Disaster Counts Over the Years')
ax.set_xlabel('Year')
ax.set_ylabel('Number of Disasters')
ax.legend(title='Disaster Type')
ax.set_xticklabels(disaster_counts.index, rotation=90)

# Save the figure
plt.savefig("output_data/Fig2.png", bbox_inches='tight')

# Show the plot
plt.tight_layout()
plt.show()
```
* We went further and check the frequency of disaster over the months

### 2. Distribution of Disaster Types:
* Next, we examined the distribution of different types of disasters in different Continents. A Line chart was created to show the frequency of disasters in continents.

```python
#plot line chart for total number of disasters over years against continents

# Group by Year and Continent to get the total number of disasters
disasters_per_year_continent = clean_disaster_metadata.groupby(['Year', 'Continent']).size().unstack(fill_value=0)

# Plotting the data
plt.figure(figsize=(12, 8))

for continent in disasters_per_year_continent.columns:
    plt.plot(disasters_per_year_continent.index, disasters_per_year_continent[continent], label=continent)

plt.xlabel('Year')
plt.ylabel('Total Number of Disasters')
plt.title('Total Number of Disasters Over Years by Continent')
plt.legend(title='Continent')
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig3.png", bbox_inches='tight')
plt.show()
```

### 3. Impact Analysis:
* We analyzed the impact of these disasters in terms of total deaths and economic damages. Bar Charts were created to visualize the number of people affected and the total economic damage.

```python
#which disaaster was most destructive (based on number of total effective and Total Damages )
selected_columns = ['Disaster Type', 'Total Affected', "Total Damages ('000 US$)"]

# Filter the DataFrame to keep only the selected columns
filtered_df = clean_disaster_metadata[selected_columns]

# Drop rows with NaN values in the selected columns
cleaned_df = filtered_df.dropna(subset=['Total Affected', "Total Damages ('000 US$)"])
cleaned_df
disaster_impact = cleaned_df.groupby('Disaster Type').agg({
    'Total Affected': 'sum',
    "Total Damages ('000 US$)": 'sum'
}).reset_index()

# Plotting Total Affected
plt.figure(figsize=(12, 6))
plt.bar(disaster_impact['Disaster Type'], disaster_impact['Total Affected'], color='blue')
plt.xlabel('Disaster Type')
plt.ylabel('Total Affected')
plt.title('Total Affected by Disaster Type')
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig("output_data/Fig8.png", bbox_inches='tight')
plt.show()

# Plotting Total Damages
plt.figure(figsize=(12, 6))
plt.bar(disaster_impact['Disaster Type'], disaster_impact["Total Damages ('000 US$)"], color='coral')
plt.xlabel('Disaster Type')
plt.ylabel('Total Damages (\'000 US$)')
plt.title('Total Damages by Disaster Type')
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig("output_data/Fig9.png")
plt.show()
```

## Conclusion:
From our analysis, several key insights emerged:
1. **Increasing Frequency:** There has been an observable increase in the frequency of disasters over the years, which could be attributed to climate change and increased reporting.
2. **Predominant Disaster Types:** Storms and floods are the most frequent types of disasters, suggesting a need for targeted preparedness measures for these events.
3. **Significant Impact:** The economic damages from natural disasters are substantial, often running into billions of dollars, highlighting the need for robust economic safeguards and recovery plans.
4. **Geographical Hotspots:** Certain regions are more prone to specific types of disasters, underscoring the importance of localized disaster management strategies.
5. **Post-2021 Challenges (Not Covered in Our Data):** Although not captured in our dataset, the period after 2021 has been marked by unprecedented global challenges, including the COVID-19 pandemic, which compounded the impact of natural disasters. The simultaneous occurrence of health crises and environmental catastrophes has stressed the need for integrated disaster response and recovery systems that can handle multiple concurrent emergencies.

By understanding these patterns and impacts, we can better prepare for future disasters and mitigate their effects on communities worldwide. This analysis serves as a foundation for further research and action in disaster management and preparedness.


