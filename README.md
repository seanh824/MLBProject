# 2022 MLB Season in Review: Player & Payroll Analysis
In the ever-evolving world of Major League Baseball, a comprehensive analysis of player statistics is essential to understanding the dynamic performance trends of the game. In this data-driven project, I delve into the statistical prowess of MLB players during the 2022 season. Leveraging a rich dataset comprising player performance metrics such as BA, OPS, and OBP, to pitching statistics like ERA, WHIP, and K%, I aim to unravel the standout performers and key insights that defined the season, while accounting for each team's playoff result. To explore the complete dataset, please click [here.](https://www.kaggle.com/datasets/vivovinco/2022-mlb-player-stats) The data was additionally validated to match its source - [baseballamerica.](https://www.baseball-reference.com/) 

## Batting Stats by Age
In recent years, Major League Baseball has witnessed a transformative shift towards a game that emphasizes youth and athleticism. Teams are increasingly focusing on scouting and developing young talents who bring speed, agility, and dynamic athleticism to the field. This evolution not only enhances the overall excitement of the sport but also underscores the importance of adaptability in a rapidly changing baseball landscape, where younger, more athletic players are driving the future of America's pastime. In order to understand fundamental statistics among differing stages of a player's career the following code was written:
  
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
Like batters, pitchers also experience changes in their performance as they age. Young pitchers tend to struggle with control even if their '"stuff" is MLB level, so generally walk rate and WHIP should improve as one ages.

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Read the CSV file into a DataFrame
csv_file = "/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Pitching.csv"
df = pd.read_csv(csv_file)

# Define the age groups and filter the data
age_groups = {
    '20-24': (20, 24),
    '25-29': (25, 29),
    '30-34': (30, 34),
    '35-40+': (35, 42)  
}

# Define age groups and only include players with 20 or more IP
filtered_data = {}
for group_name, (min_age, max_age) in age_groups.items():
    filtered_data[group_name] = df[(df['Age'] >= min_age) & (df['Age'] <= max_age) & (df['IP'] >= 20)]

# Calculate the average ERA, WHIP, Strikeout to Walk Ratio, and Walks Per 9 Innings for each age group
avg_era = [filtered_data[group]['ERA'].mean() for group in age_groups]
avg_whip = [filtered_data[group]['WHIP'].mean() for group in age_groups]
avg_so_bb_ratio = [filtered_data[group]['SO/BB'].mean() for group in age_groups]
avg_bb9 = [filtered_data[group]['BB9'].mean() for group in age_groups]

# Create the grouped bar graph
fig, ax = plt.subplots(figsize=(12, 6))
bar_width = 0.2
index = np.arange(len(age_groups))

bar1 = ax.bar(index, avg_era, bar_width, label='ERA')
bar2 = ax.bar(index + bar_width, avg_whip, bar_width, label='WHIP')
bar3 = ax.bar(index + 2*bar_width, avg_so_bb_ratio, bar_width, label='Strikeout to Walk Ratio')
bar4 = ax.bar(index + 3*bar_width, avg_bb9, bar_width, label='Walks Per 9 Innings')

# Set the x-axis labels
ax.set_xticks(index + 2*bar_width)
ax.set_xticklabels(age_groups.keys())

# Labeling
ax.set_xlabel('Age Groups')
ax.set_ylabel('Average')
ax.set_title('Pitching Statistics by Age in 2022 MLB Season (min. 20 IP)')

# Annotate bars with actual values
def annotate_bars(bar_group):
    for bar in bar_group:
        height = bar.get_height()
        ax.annotate(f'{height:.3f}',  # Format the value to three decimal places
                     xy=(bar.get_x() + bar.get_width() / 2, height),
                     xytext=(0, 3),  # 3 points vertical offset
                     textcoords="offset points",
                     ha='center', va='bottom')

annotate_bars(bar1)
annotate_bars(bar2)
annotate_bars(bar3)
annotate_bars(bar4)

# Move the legend up and to the right
ax.legend(loc='upper right', bbox_to_anchor=(1.25, 1))

# Set the y-axis ticks in increments of 0.2
y_ticks = np.arange(0, max(max(avg_era), max(avg_whip), max(avg_so_bb_ratio), max(avg_bb9)) + 0.2, 0.2)
ax.set_yticks(y_ticks)

# Show the plot
plt.tight_layout()
plt.show()
```

![Figure_2](https://github.com/seanh824/MLBProject/assets/140123586/63f75171-4b76-4f74-887f-e9a3cd35f355)

There is an innings-pitched requirement for all players involved in the graph to combat outlier data while maintaining sufficient data for analysis. As expected, walks per 9 innings decreased with age across the board. Pitchers aged 35-40+ have a walks per 9 innings rate that is approximately 2/3 of a walk lower than pitchers aged 20-24. Over the course of a season, that rate decrease will make a noticeable difference. More traffic on the bases means more bullpen usage. Older pitchers' velocity may not age like wine, but generally speaking, their walk rate will. An unexpected result of this data visualization is that WHIP from all age groups did not change greatly. Similarly to how BA and OPS change over age, WHIP improves slightly from early 20s to late 20s and again from early 30s to late 30s. Overall, the theme of this graph suggests that in order to still be a viable pitcher late in your career you must limit the free passes. Not only does that help lower arguably the most important pitcher's stat in ERA, but it also allows pitchers to be more efficient and pitch deeper into games.

## Availability Is the Best Ability?
It is often said that a player who can remain on the field will play better due to finding their groove with routine reps. This ideology is often used by fans to will their teams into playing promising prospects day in and day out. To grasp fundamental statistics among players with varying games played the following code was produced:

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load the CSV file
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
data = pd.read_csv(file_path)

# Define the bins for the 'G' column
bins = [0, 30, 60, 90, 120, 150, 162]
labels = ['0-30', '30-60', '60-90', '90-120', '120-150', '150-162']

# Create a new column 'Game_Group' based on the 'G' column
data['Game_Group'] = pd.cut(data['G'], bins=bins, labels=labels)

# Calculate the average for 'BA', 'OBP', and 'SLG' for each group
grouped_data = data.groupby('Game_Group')[['BA', 'OBP', 'SLG']].mean().reset_index()

# Create the grouped bar chart
fig, ax = plt.subplots(figsize=(10, 6))

# Define colors for the bars
colors = ['#1f77b4', '#ff7f0e', '#2ca02c']

# Create bars for each metric ('BA', 'OBP', 'SLG') for each group
for i, metric in enumerate(['BA', 'OBP', 'SLG']):
    ax.bar(
        grouped_data.index + i * 0.2,  # Shift bars for different metrics
        grouped_data[metric],
        width=0.2,
        label=metric,
        color=colors[i]
    )

# Add values above each bar
for i, v in enumerate(grouped_data.index):
    for j, metric in enumerate(['BA', 'OBP', 'SLG']):
        value = grouped_data.at[v, metric]
        ax.text(v + j * 0.2, value + 0.005, f'{value:.3f}', ha='center')

# Set y-axis labels with 2 trailing zeros
ax.get_yaxis().set_major_formatter(plt.FuncFormatter(lambda x, loc: f"{x:.3f}"))

# Set x-axis labels
ax.set_xticks(grouped_data.index + 0.2)
ax.set_xticklabels(grouped_data['Game_Group'])

# Set plot labels and title
ax.set_xlabel('Games Played')
ax.set_ylabel('Batting Statistic')
ax.set_title('Batting Statistics by Games Played (2022)')

# Add legend
ax.legend()

# Show the plot
plt.tight_layout()
plt.show()
```

![Figure_3](https://github.com/seanh824/MLBProject/assets/140123586/f556706b-3978-48be-99ac-686cc35ad899)

The games played groups can be thought of as months played in the season, from less than 1 month to all 6 months. Ultimately the three metrics, BA, OBP, and SLG, all increased steadily for all games played groups. The 0-30 games played group's BA, OBP, and SLG are head and shoulders below the other 5 groups' averages, potentially a sign of skewed player data due to the small sample size. From group 30-60 games played to group 150-162 games played BA, OBP, and SLG all increased by ~0.10-0.20 for every group. Excluding the 0-30 games played group, the difference in statistical averages from 30-60 and 150-162 games played is the same difference between a replacement-level player and an above-average player. The message of this visualization is clear - baseball is a very routine-dependent sport with comfortability and confidence gained through playing time.
