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

## The Best Ability Is Availability
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

## Regular Season Payroll-to-Win Ratio with Postseason Outcome
The key to MLB roster construction according to previous data visualizations is young players who play a large majority of the season. Veterans can also provide leadership and guide young players into quality major leaguers. Another way to supplement talent in lieu of developing players is by signing free agents. The more a team spends in MLB's luxury tax system the worse their draft pick and taxes are, so spending wisely is encouraged.

```python
import pandas as pd
import matplotlib.pyplot as plt
from collections import OrderedDict

# Define the color mapping for teams
team_colors = {
    'HOU': 'red',
    'PHI': 'yellow',
    'NYY': 'green',
    'SDP': 'green',
    'CLE': 'blue',
    'ATL': 'blue',
    'SEA': 'blue',
    'LAD': 'blue',
    'TBR': 'purple',
    'TOR': 'purple',
    'NYM': 'purple',
    'STL': 'purple',
    'ARI': 'gray',
    'BAL': 'gray',
    'BOS': 'gray',
    'CHC': 'gray',
    'CHW': 'gray',
    'CIN': 'gray',
    'COL': 'gray',
    'DET': 'gray',
    'KCR': 'gray',
    'LAA': 'gray',
    'MIA': 'gray',
    'MIL': 'gray',
    'MIN': 'gray',
    'OAK': 'gray',
    'PIT': 'gray',
    'SFG': 'gray',
    'TEX': 'gray',
    'WSN': 'gray'
}

# Define labels for the legend
legend_labels = {
    'red': 'Won World Series',
    'yellow': 'Lost World Series',
    'green': 'Lost Championship Series',
    'blue': 'Lost Division Series',
    'purple': 'Lost Wild Card Series',
    'gray': 'No Postseason Berth'
}

# Read the CSV file
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Payroll.csv'
df = pd.read_csv(file_path)

# Add a color column based on the team_colors mapping
df['Color'] = df['Team'].map(team_colors)

# Define the custom order for sorting
custom_order = ['red', 'yellow', 'green', 'blue', 'purple', 'gray']

# Convert the 'Color' column to a categorical data type with the custom order
df['Color'] = pd.Categorical(df['Color'], categories=custom_order, ordered=True)

# Sort the DataFrame first by color, then by decreasing total wins
df = df.sort_values(by=['Color', 'Total Wins'], ascending=[True, False])

# Create a new figure and axis
fig, ax = plt.subplots()

# Create an OrderedDict to store unique legend labels
legend_handles = OrderedDict()

# Plot the bars with specified colors and labels
for index, row in df.iterrows():
    dollars_per_win = row['Dollars/Win']
    total_wins = row['Total Wins']
    color = row['Color']
    team = row['Team']
    label = legend_labels[color]
    
    # Add the first occurrence of each color to the legend
    if color not in legend_handles:
        legend_handles[color] = ax.bar(dollars_per_win, total_wins, color=color, label=label)
    else:
        ax.bar(dollars_per_win, total_wins, color=color)

# Set axis labels and title
ax.set_xlabel('Dollars/Win')
ax.set_ylabel('Regular Season Win Total')
ax.set_title('Money Spent Per Regular Season Win with Postseason Outcome (2022)')

# Set y-axis scale to 5
ax.set_yticks(range(0, max(df['Total Wins']) + 6, 5))

# Rotate the x-axis labels vertically
plt.xticks(rotation=90)

# Add labels above each bar
for index, row in df.iterrows():
    dollars_per_win = row['Dollars/Win']
    total_wins = row['Total Wins']
    team = row['Team']
    ax.text(dollars_per_win, total_wins + 0.5, team, ha='center', va='bottom')

# Add the legend to the top right
ax.legend(handles=legend_handles.values(), labels=legend_labels.values(), loc='upper right')

# Display the plot
plt.show()
```

![Figure_4](https://github.com/seanh824/MLBProject/assets/140123586/34d888fc-2522-45f9-a37b-dd36c8b9b2f3)

Free agent spending is critical for not just regular season outcomes but the implication they have on postseason seeding. A team's postseason seed can ultimately change their route to a World Series, for better or worse. If a team's payroll influences their regular season outcome and subsequent postseason seeding, can teams buy a World Series? This was a question that plagued MLB for years and played a part in the luxury tax system we see today.

The biggest losers according to this graph are the White Sox, Red Sox, and Giants. They all failed to make the postseason while spending more per win than half the teams that made the postseason. The biggest winners are the Astros, Guardians, and Orioles. The Astros evidently won on multiple levels; they had the second-best record in the regular season, spent towards the bottom per win among postseason teams, and won the World Series. A cost-effective championship-caliber team that also dominated the regular season. The Orioles 2022 season was successful due to contending for the postseason with the lowest money spent per regular season win in the league. Among postseason teams, the Guardians spent the fewest per regular season win, making their ticket to the fall classic the most cost-effective. Teams with low payrolls are viewed as having a lower desire to win, so if they either contend for or even make the postseason their season is automatically a win. Throughout the Astros' last decade of postseason runs, their payroll has never been in the top three of the league. Some teams had average results in relation to spending and postseason outcomes. The Phillies had a confusing season, they played like a borderline playoff team in the regular season but spent like a team that went as far as they ended up going. Considering the Padres and Yankees' 2022 expectations and payroll, their postseason results were slightly underwhelming. They had some of the highest payroll-to-win ratios among postseason teams. All in all, buying championships may have been a thing of the past but that's all it is now - a thing of the past. Simply spending more than other teams is not the only way to construct a competent roster.

## Number of Players with OPS+ ≥ 100 by Team
While we previously highlighted the potential dangers of spending large amounts of money, there is a reason teams still do it, and it's mainly due to the roster depth increased spending facilities. Teams can spend money to fill holes and reduce the number of everyday starters with a below-average fundamental batting statistic in OPS+. Do better teams automatically have more 100+ OPS+ players than others? 

```python
import pandas as pd

# Read the CSV file into a pandas DataFrame
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
df = pd.read_csv(file_path)

# Filter the data based on the specified conditions (81 or more in 'G' and 100 or more in 'OPS+')
filtered_df = df[(df['G'] >= 81) & (df['OPS+'] >= 100)]

# Group the filtered data by the 'Tm' column and count the number of players for each team
team_counts = filtered_df['Tm'].value_counts().reset_index()
team_counts.columns = ['Team', 'Number of Players']

# Display the result
print(team_counts)
```

![Figure_5](https://github.com/seanh824/MLBProject/assets/140123586/e869f0be-1e27-475b-8dc8-ea83ac95625e)

Qualifying players must have played at least half the season or 81 games, so teams with four or fewer 100+ OPS+ players are expected to be poor, which is the case. Even though Juan Soto qualified as a National it's surprising to see Washington have 5 players with an OPS+ of 100+. The Blue Jays have the most 100+ OPS+ players in baseball (8) and the Red Sox are tied for second with 7 such players despite both not winning the AL East. The team that did win the division, the Yankees, had the fewest number of 100+ OPS+ players among AL East teams. This is more of a testament to the type of division the AL East is rather than a knock on the Yankees' division win.

