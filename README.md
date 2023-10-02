# 2022 MLB Season in Review: Player & Payroll Analysis
In the ever-evolving world of Major League Baseball, a comprehensive analysis of player statistics is essential to understanding the dynamic performance trends of the game. In this data-driven project, I delve into the statistical prowess of MLB players during the 2022 season. Leveraging a rich dataset comprising player performance metrics such as BA, OPS, and OBP, to pitching statistics like ERA, WHIP, and K%, I aim to unravel the standout performers and key insights that defined the season, while accounting for each team's playoff result. To explore the complete dataset, please click [here.](https://www.kaggle.com/datasets/vivovinco/2022-mlb-player-stats) The data was additionally validated to match its source - [baseballamerica.](https://www.baseball-reference.com/) 

## Batting Stats by Age
In recent years, Major League Baseball has witnessed a transformative shift towards a game that emphasizes youth and athleticism. Teams are increasingly focusing on scouting and developing young talents who bring speed, agility, and dynamic athleticism to the field. This evolution not only enhances the overall excitement of the sport but also underscores the importance of adaptability in a rapidly changing baseball landscape, where younger, more athletic players are driving the future of America's pastime. In order to understand fundamental statistics among differing age groups the following code was written:
  
```python 
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Load the CSV file into a DataFrame with the appropriate encoding
file_path = "/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv"
data = pd.read_csv(file_path, encoding='latin-1')  # Use 'latin-1' encoding or another appropriate encoding

# Define the age groups and filter the data
age_groups = {
    '20-24': (20, 24),
    '25-29': (25, 29),
    '30-34': (30, 34),
    '35-40+': (35, 42)
}

filtered_data = {}
for group_name, (min_age, max_age) in age_groups.items():
    filtered_data[group_name] = data[(data['Age'] >= min_age) & (data['Age'] <= max_age) & (data['PA'] >= 100)]

avg_ba = [filtered_data[group]['BA'].mean() for group in age_groups]
avg_obp = [filtered_data[group]['OBP'].mean() for group in age_groups]
avg_ops = [filtered_data[group]['OPS'].mean() for group in age_groups]

# Create the grouped bar graph
fig, ax1 = plt.subplots(figsize=(12, 6))
bar_width = 0.2
index = np.arange(len(age_groups))

bar1 = ax1.bar(index, avg_ba, bar_width, label='Batting Average')
bar2 = ax1.bar(index + bar_width, avg_obp, bar_width, label='On Base Percentage')
bar3 = ax1.bar(index + 2*bar_width, avg_ops, bar_width, label='On Base Plus Slugging')

# Set the x-axis labels
ax1.set_xticks(index + bar_width)
ax1.set_xticklabels(age_groups.keys())

# Labeling for the left y-axis
ax1.set_xlabel('Age Groups')
ax1.set_ylabel('Average')
ax1.set_title('Batting Statistics by Age in 2022 MLB Season (min. 100 PA)')

# Move the legend to the right of the right y-axis
ax1.legend(loc='upper left', bbox_to_anchor=(1.05, 1))

# Set the y-axis ticks for the left y-axis in increments of 0.025
y_ticks = np.arange(0, max(max(avg_ba), max(avg_obp), max(avg_ops)) + 0.025, 0.025)
ax1.set_yticks(y_ticks)

# Annotate bars with actual values
def annotate_bars(bar_group):
    for bar in bar_group:
        height = bar.get_height()
        ax1.annotate(f'{height:.3f}',  # Format the value to three decimal places
                     xy=(bar.get_x() + bar.get_width() / 2, height),
                     xytext=(0, 3),  # 3 points vertical offset
                     textcoords="offset points",
                     ha='center', va='bottom')

annotate_bars(bar1)
annotate_bars(bar2)
annotate_bars(bar3)

# Show the plot
plt.tight_layout()
plt.show()
```

![Figure_1](https://github.com/seanh824/MLBProject/assets/140123586/e659dafb-74cd-45c9-8377-849511c92383)

It's important to note that these averages are from players with at least 100 plate appearances, or around a month's worth of games. This threshold attempts to eliminate outlier player data that could skew results, particularly in the younger age groups. While the OBP between all age groups remained constant, what didn't was BA and OPS. Both BA and OPS decreased slightly from early 20s to late 20s but saw an increase in early 30s essentially back to early 20s numbers before dropping to a new low in the 35 plus age group. This could suggest that once players turn 35 they must make readjustments to their swing in order to maintain a career. This is seen quite often with long contracts that began when the player was in their 20s. The quality of such contracts is generally determined by how well and often the player performs in their final years. The ability to simply stay healthy becomes paramount for older players. Albert Pujols, whose final year was in 2022, is a perfect example of a player who had an otherworldly start to his career but tapered off during his late 30s.

## Pitching Stats by Age
Like batters, pitchers also experience changes in their performance as they age. Young pitchers tend to struggle with...
