# 2022 MLB Season in Review: Player & Payroll Analysis
In the ever-evolving world of Major League Baseball, a comprehensive analysis of player statistics is essential to understanding the dynamic performance trends of the game. In this data-driven project, I delve into the statistical prowess of MLB players during the 2022 season. Leveraging a rich dataset comprising of every single player and their batting metrics such as BA, OPS, and OBP, as well as their pitching statistics like ERA, WHIP, and K%, I aim to unravel the standout performers and key insights that defined the season, while accounting for each team's playoff result. To explore the complete dataset, please click [here.](https://www.kaggle.com/datasets/vivovinco/2022-mlb-player-stats) The data was additionally validated to match its source - [baseballamerica.](https://www.baseball-reference.com/) 


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

## Batting Stats by Division
It's well reported that the best division is the AL East, but how do other divisions compare? How do weak divisions with one or two great teams average out? That is quite an interesting proposition because what makes the AL East, nicknamed the "AL Beast", such a strong division is that all teams are either average or very good. Other divisions like the AL Central have average teams and very, very bad teams. The fundamental BA, OBP, and OPS metrics were averaged out per each division using the 'Tm' column's team code and mapping it to the team's respective division label:   

```python
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter
import numpy as np

# Load the dataset
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
data = pd.read_csv(file_path)

# Define the team-to-division mapping
division_mapping = {
    'NYY': 'AL East',
    'BOS': 'AL East',
    'TBR': 'AL East',
    'TOR': 'AL East',
    'BAL': 'AL East',
    'NYM': 'NL East',
    'WSN': 'NL East',
    'ATL': 'NL East',
    'MIA': 'NL East',
    'PHI': 'NL East',
    'CLE': 'AL Central',
    'MIN': 'AL Central',
    'DET': 'AL Central',
    'KCR': 'AL Central',
    'CHW': 'AL Central',
    'MIL': 'NL Central',
    'CHC': 'NL Central',
    'CIN': 'NL Central',
    'PIT': 'NL Central',
    'STL': 'NL Central',
    'HOU': 'AL West',
    'TEX': 'AL West',
    'SEA': 'AL West',
    'LAA': 'AL West',
    'OAK': 'AL West',
    'LAD': 'NL West',
    'ARI': 'NL West',
    'SFG': 'NL West',
    'SDP': 'NL West',
    'COL': 'NL West'
}

# Map the teams to divisions
data['Division'] = data['Tm'].map(division_mapping)

# Group the data by division and calculate the average for 'BA', 'OBP', and 'OPS'
division_stats = data.groupby('Division')[['BA', 'OBP', 'OPS']].mean()

# Create a column chart
fig, ax = plt.subplots(figsize=(12, 6))
n = len(division_stats)
width = 0.2
x = np.arange(n)
colors = ['b', 'g', 'r']
index = 0

for stat in ['BA', 'OBP', 'OPS']:
    ax.bar(x + width * index, division_stats[stat], width=width, label=stat)
    index += 1

# Define a custom y-axis formatter to add 1 trailing zero
def custom_formatter(x, pos):
    return f'{x:.2f}0'

# Apply the custom formatter to the y-axis
ax.yaxis.set_major_formatter(FuncFormatter(custom_formatter))

# Add the actual values above each bar
for i in range(n):
    for j, stat in enumerate(['BA', 'OBP', 'OPS']):
        value = division_stats.at[division_stats.index[i], stat]
        ax.text(i + width * j, value + 0.01, f'{value:.3f}', ha='center', va='bottom', fontsize=10)

ax.set_xlabel('Division')
ax.set_ylabel('Batting Statistic Value')
ax.set_title('BA, OBP, OPS by Division (2022)')
ax.set_xticks(x + width)
ax.set_xticklabels(division_stats.index)
ax.legend(title='Statistics', loc='upper right')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

![Figure_9](https://github.com/seanh824/MLBProject/assets/140123586/f32b6a17-7420-4025-b92f-499c629cdf75)

This data does not have a qualifying PA or AB metric, so the BA, OBP, and OPS for each division are all much lower than normal and cannot be taken out of context. However, by not filtering outliers from any group, it ensures that each group is subjected to the same data processing steps, which can make the comparing divisions' stats fair and unbiased. The AL East is confirmed as MLB's best division top to bottom. The AL East's average OPS was 0.040 points higher than the next closest division (NL Central). That is unexpected in and of itself - the NL Central has the second-best average OPS of all divisions. What is expected is what division had the worst average OPS, the AL Central, almost 0.100 points lower than the AL East. Each division can be summarized like so: the AL Central is average to dreadful, the AL East is by far the best division in the league, the AL West and NL Central are both decent with a mix of competitive teams and consistent tankers, the NL East has an even more dramatic difference between its good and bad teams, which is even more pronounced in the NL West as the Dodgers are the only well above average team.

## The Coors Field Effect
Playing at Coors Field is portrayed as a batter's dream due to the ball traveling further. Players on the Rockies can be viewed from a different prism because half of all their games are played with a ball that has significantly less drag. Coors Field's deep outfield and high fences help reduce the number of home runs hit, but with a larger outfield comes more ground to cover for outfielders. This turns fly-outs into bloop singles and doubles into triples. I would expect to see the Rockies with much higher averages for all three metrics analyzed: BA, OBP, and OPS. 

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load the data from the CSV file
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
data = pd.read_csv(file_path)

# Filter the data for 'COL' team and PA >= 100
col_data = data[(data['Tm'] == 'COL') & (data['PA'] >= 100)]

# Calculate averages for 'BA', 'OBP', and 'OPS' for 'COL' team
col_avg = col_data[['BA', 'OBP', 'OPS']].mean()

# Filter the data for non-'COL' and non-'TOT' teams and PA >= 100
mlb_without_col_data = data[(data['Tm'] != 'COL') & (data['Tm'] != 'TOT') & (data['PA'] >= 100)]

# Calculate averages for 'BA', 'OBP', and 'OPS' for non-'COL' and non-'TOT' teams
mlb_without_col_avg = mlb_without_col_data[['BA', 'OBP', 'OPS']].mean()

# Create a grouped bar chart
categories = ['BA', 'OBP', 'OPS']
col_values = col_avg.values
mlb_without_col_values = mlb_without_col_avg.values
width = 0.35
x = range(len(categories))

fig, ax = plt.subplots()
rects1 = ax.bar(x, col_values, width, label='COL')
rects2 = ax.bar([i + width for i in x], mlb_without_col_values, width, label='MLB (w/o COL)')

# Set the y-axis ticks and limit
ax.set_yticks([i * 0.025 for i in range(0, 33)])
ax.set_ylim(0, 0.725)

# Annotate bars with actual values
for rects in [rects1, rects2]:
    for rect in rects:
        height = rect.get_height()
        ax.annotate(f'{height:.3f}', xy=(rect.get_x() + rect.get_width() / 2, height),
                    xytext=(0, 3), textcoords='offset points', ha='center', va='bottom')

# Set labels and title
ax.set_xlabel('Metrics')
ax.set_ylabel('Average')
ax.set_title('Batting Statistics of Colorado Rockies vs. Rest of MLB (2022)')
ax.set_xticks([i + width / 2 for i in x])
ax.set_xticklabels(categories)
ax.legend()

plt.tight_layout()
plt.show()
```

![Figure_7](https://github.com/seanh824/MLBProject/assets/140123586/3a72241c-23cf-471d-8bb9-1e5d1fdbe7fa)

The hypothesized difference is not nearly as large as the actual result, this can be attributed to the fact that the Rockies were one of the worst teams in 2022. For a rebuilding team, the Rockies can only benefit so much from playing half their games with 20% less atmospheric pressure. Even still the Rockies' average OBP and OPS were ever so slightly higher than the average for the rest of MLB (w/o the Rockies) combined. The MLB average BA (excluding the Rockies) was around 0.010 points lower than the Rockies team BA, which is not _nothing_.

## Relationship Between SO:BB Ratio & WHIP
Generally, one of the best ways to limit damage as a pitcher is through the strikeout, which is especially true at Coors Field. The higher a pitcher's WHIP the higher the odds those runs score, inflating their ERA. Evidently, walks are part of the WHIP statistic, but not necessarily is SO:BB ratio. A player can have a low SO:BB ratio and also a low WHIP, they simply may just not be a strikeout pitcher. With that being said, a high SO:BB walk ratio is usually accompanied by a low WHIP. A pitcher with a lot of strikeouts inherently lowers their opponent's chances of getting a walk or hit. To determine how correlated SO:BB ratio is with WHIP the following code was written:  

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Define the path to your CSV file
csv_file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Pitching.csv'

# Load the CSV data into a Pandas DataFrame
df = pd.read_csv(csv_file_path)

# Filter rows with 'IP' values of 20 or more
df = df[df['IP'] >= 20]

# Filter out rows with missing or invalid data in 'SO/BB' and 'WHIP' columns 
df = df.dropna(subset=['SO/BB', 'WHIP'])

# Extract the 'SO/BB' and 'WHIP' columns
so_bb = df['SO/BB']
whip = df['WHIP']

# Calculate the correlation coefficient
correlation_coefficient = so_bb.corr(whip)

# Create a scatter plot
plt.figure(figsize=(8, 6))
plt.scatter(so_bb, whip, label=f'Correlation = {correlation_coefficient:.2f}')
plt.xlabel('SO:BB Ratio')
plt.ylabel('WHIP')

# Calculate the line of best fit
slope, intercept = np.polyfit(so_bb, whip, 1)
plt.plot(so_bb, slope * so_bb + intercept, color='red', linestyle='--', label='Line of Best Fit')

# Add a legend
plt.legend()

# Show the plot
plt.title('Relationship Between SO:BB Ratio and WHIP in 2022 MLB Season (min. 20 IP)')
plt.grid(True)
plt.show()
```

![Figure_8](https://github.com/seanh824/MLBProject/assets/140123586/c9e1f108-28c3-4bae-8c21-4ab8431fd79d)

In order to produce this scatter plot, the code ran through a new column that was created with an if error division formula that left any player's SO:BB ratio value blank if they failed to record a walk. This also essentially doubled as an innings-pitched requirement. The syntax of the formula used for the first row in the new column is as follows: =IFERROR(W2/U2, ""). There is a clear relationship between the two statistics, yet not as strong as expected. The correlation coefficient is only -0.54, suggesting strikeouts reduce traffic on the bases and subsequent WHIP values, but so can other methods. The ideology of pitching to contact is most definitely viable in today's game. It may not be as prevalent as in years past but pitching to contact reduces pitch counts and is particularly useful for older pitchers with diminished velocity.    

## Every Team's Best Hitter by OPS+
A major reason why the 2022 MLB season preceded the most significant rule changes the game had ever seen was because offenses across the league were historically down. In 2023, MLB banned the shift and made the bases bigger in an attempt to stimulate offenses. One way to analyze a team's offensive output is to determine who their best player was going into the year and who finished the year with their team's highest OPS+. This was accomplished with the following code, the list of every team's highest OPS+ player was then transferred to a new spreadsheet:

```python
import pandas as pd

# Read the CSV file into a DataFrame
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
df = pd.read_csv(file_path)

# Filter the DataFrame for players with 'G' >= 81 and 'Tm' not equal to 'TOT'
df_filtered = df[(df['G'] >= 81) & (df['Tm'] != 'TOT')]

# Group the filtered DataFrame by 'Tm' (team) and find the player with the max 'OPS+' in each group
result = df_filtered.groupby('Tm').apply(lambda group: group[group['OPS+'] == group['OPS+'].max()])

# Reset the index to get rid of the multi-index
result = result.reset_index(drop=True)

# Display the resulting DataFrame
print(result[['Tm', 'Name', 'OPS+', 'G']])
```

![Figure_9](https://github.com/seanh824/MLBProject/assets/140123586/b6c4fd62-df9a-4ecd-bc2f-17b45ec0e5c0)

This table is sorted by OPS+ and the games played column is conditional formatted so that the red becomes progressively darker as games played increases. In other words, the darker the red the more stock you can put into the OPS+ statistic. All players were required to appear in 81 games or half the season. Overall, most of the brand names are here with five players surprisingly making the list: C.J. Cron, Eric Haase, Hunter Renfroe, Nathaniel Lowe, and Austin Riley. I expected Kris Bryant instead of C.J. Cron, Javier Baez instead of Eric Haase, Christian Yelich instead of Hunter Renfroe, Corey Seager instead of Nathaniel Lowe, and Ronald Acuna Jr. instead of Austin Riley. Acuna Jr. was still not 100% due to his torn ACL in 2021 and Kris Bryant dealt with back and foot injuries all year, but Yelich, Baez, and Seager all have no excuses. While this analysis was fascinating and can identify which superstars delivered and which ones didn't, it by no means outlines an entire team's offensive performance. 

## Three True Outcomes Rate by Team
We know that offensive performances in the years leading up to and including 2022 prompted the rule changes seen in 2023. An underlying part of these offensive struggles can attributed to how often players fall victim to the three true outcomes. The three true outcomes refer to strikeouts, walks, or home runs. These outcomes present themselves more in the postseason, as there is better pitching and the few mistakes that are made are either balls that produce walks or pitches left over the plate producing home runs. Rarely are multiple hits strung together in the postseason, making a team's three true outcomes rate important yet tricky to interpret. Too many walks and strikeouts will not move runners over to manufacture runs, but too few home runs will also make it hard to push runs across. Thus, a middle-of-the-pack three true outcome rate is best. Each team was color-coded to indicate where they finished in the postseason and although these three true outcome rates are from the 2022 regular season, they still are a valid litmus test of how a team may subsequently perform in the postseason.

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load the CSV file into a DataFrame
file_path = "/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv"
df = pd.read_csv(file_path)

# Filter out rows with 'TOT' in the 'Tm' column
df = df[df['Tm'] != 'TOT']

# Group the data by team and calculate the sum of HR, SO, BB, and PA for each team
team_stats = df.groupby('Tm').agg({'HR': 'sum', 'SO': 'sum', 'BB': 'sum', 'PA': 'sum'}).reset_index()

# Calculate the True Outcome percentages for each team
team_stats['True_Outcome_Rate'] = (team_stats['HR'] + team_stats['SO'] + team_stats['BB']) / team_stats['PA'] * 100

# Define color mapping for each team
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

# Sort the DataFrame by the order of the color mapping
team_stats['Color_Order'] = [list(team_colors.keys()).index(team) for team in team_stats['Tm']]
team_stats = team_stats.sort_values(by='Color_Order').drop(columns=['Color_Order'])

# Create a bar graph of True Outcome rates for each team
plt.figure(figsize=(12, 6))
bars = plt.bar(team_stats['Tm'], team_stats['True_Outcome_Rate'], color=[team_colors.get(team, 'gray') for team in team_stats['Tm']])
plt.xlabel('Team')
plt.ylabel('Three True Outcomes Rate (%)')
plt.title('Three True Outcomes Rate by Team (2022)')
plt.xticks(rotation=90)

# Add actual values above the bars
for bar, value in zip(bars, team_stats['True_Outcome_Rate']):
    plt.text(bar.get_x() + bar.get_width() / 2, value + 1, str(round(value, 2)), ha='center', va='bottom')

# Set the y-axis maximum value to 40
plt.ylim(0, 40)

# Add the legend to the side of the bar graph on the right
legend_handles = [plt.Rectangle((0, 0), 1, 1, color=color) for color in legend_labels.keys()]
legend_labels_list = [label for label in legend_labels.values()]
plt.legend(legend_handles, legend_labels_list, title="Legend", loc="center left", bbox_to_anchor=(1, 0.5))

plt.tight_layout()

# Show the bar graph
plt.show()
```

![Figure_10](https://github.com/seanh824/MLBProject/assets/140123586/a7c77f9b-3b76-45d1-bace-cf5564496c1f)

The Guardians' three true outcomes rate is the lowest among not only postseason teams but the league as a whole. This supports the theme of the 2022 Cleveland Guardians - a scrappy, put-the-ball-in-play team. What they failed to do and what makes this statistic a balancing act is hit home runs. The Guardians hit the fewest home runs in 2022. Meanwhile, the Yankees hit the most home runs in 2022 and finished with the highest three true outcome rate among all teams. This rate is only a fraction higher than a few teams and is skewed by Aaron Judge's historic 62 home run season. Ultimately these points still stand, the Guardians failed to advance further in the postseason because they did not hit enough home runs and lacked general slugging power, home run or not. The Yankees were too reliant on the home run and when they didn't homer, they didn't make as many productive outs nor put the ball in play. Obviously, there are several factors that predict postseason performance, but the three true outcomes rate is definitely one of them. 

## Regular Season Team ERAs with Postseason Outcome
It is often said that in the playoffs good pitching will always triumph over good hitting. So in this graph of every team's regular season ERA, I again color code each team by their placement in the 2022 MLB postseason.

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Load the CSV file into a DataFrame
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Pitching.csv'
data = pd.read_csv(file_path)

# Mapping of team codes to colors
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
    'WSN': 'gray',
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

# Group the data by 'Tm' (team) and calculate the average ERA
team_eras = data.groupby('Tm')['ERA'].mean()

# Order the teams according to the specified mapping
team_eras = team_eras.reindex(team_colors.keys())

# Create a bar graph of the average ERAs with custom colors and labels
plt.figure(figsize=(12, 6))
ax = team_eras.plot(kind='bar', color=[team_colors[team] for team in team_eras.index], edgecolor='black')
plt.title('Regular Season Team ERAs with Postseason Outcome (2022)')
plt.xlabel('Team')
plt.ylabel('Average ERA')

# Set the y-axis ticks and limits
plt.yticks(np.arange(0, 8.50, 0.50))  # Increments of 0.50
plt.ylim(0, 8)  # Limit of 8

plt.xticks(rotation=45)

# Add actual values above each bar
for i, v in enumerate(team_eras):
    ax.text(i, v + 0.1, f'{v:.2f}', ha='center', va='bottom', fontsize=8)

# Create a custom legend and move it to the right
legend_handles = []
for color, label in legend_labels.items():
    legend_handles.append(plt.bar(0, 0, color=color, label=label))

plt.legend(handles=legend_handles, loc='upper left', title='Postseason Result', bbox_to_anchor=(1.05, 1))

plt.tight_layout()

# Show the plot
plt.show()
```

![Figure_11](https://github.com/seanh824/MLBProject/assets/140123586/685b04a5-b55e-49fe-b2ce-15605da8585b)

The first thing that jumps out at you is the Philadelphia Phillies. Their ERA is so high for a team that won a pennant, further solidifying the narrative that the Phillies overachieved. There is also a clear ERA drop between division series teams and wild card series teams. The average team ERA for division series losers was 4.0075, and 5.60 for wild card series losers - the Phillies team ERA was 6.45. Also, the average ERA of playoff teams is 4.8425, 5.625 for nonplayoff teams. A team that helps drive down the average ERA of playoff teams is the Dodgers. They finished the 2022 regular season with the best team ERA, yet failed to make it out of the NLDS. Of course, this data does not take into account the actual team ERAs in the playoffs, but, other than the Phillies and Dodgers, for better or worse, there is reason to believe a team's regular season performance was a sign of things to come. 

## Average K% by Team with Postseason Outcome
A team's strikeout percentage for hitters in baseball is important in the regular season because it reflects their ability to make contact with the ball. There is a possibly for an error to occur or the ball finds a hole, which the same cannot be said about a strikeout. It is a guaranteed out. Lower strikeout percentages often correlate with better offensive performance, as they result in more balls in play and potential scoring opportunities. In the playoffs, where runs are crucial, a team with hitters who can limit strikeouts may have an advantage in generating offense and winning crucial games. 

```python
import pandas as pd
import matplotlib.pyplot as plt

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

# Read the CSV file
file_path = '/Users/seanhughes/Downloads/MLBProjectData/2022_MLB_Player_Stats_Batting.csv'
data = pd.read_csv(file_path)

# Filter data for players with 100 or more Plate Appearances (PA)
qualified_players = data[data['PA'] >= 100]

# Calculate strikeout percentage
qualified_players['Strikeout_Percentage'] = (qualified_players['SO'] / qualified_players['PA']) * 100

# Group data by 'Tm' and calculate the average strikeout percentage for each team
team_strikeout_avg = qualified_players[qualified_players['Tm'] != 'TOT'].groupby('Tm')['Strikeout_Percentage'].mean()

# Sort teams based on 'Tm'
sorted_teams = team_colors.keys()
team_strikeout_avg = team_strikeout_avg.reindex(sorted_teams)

# Create a bar graph
plt.figure(figsize=(12, 6))
bars = team_strikeout_avg.plot(kind='bar', color=[team_colors[team] for team in team_strikeout_avg.index])

# Add data values above each bar
for bar in bars.patches:
    plt.text(bar.get_x() + bar.get_width() / 2 - 0.1, bar.get_height() + 0.5, f'{bar.get_height():.2f}', fontsize=10)

# Customize the plot
plt.title('Average Strikeout Percentage by Team in 2022 MLB Season with Postseason Outcome (min. 100 PA)')
plt.xlabel('Team')
plt.ylabel('Average Strikeout Percentage')
plt.xticks(rotation=45)
plt.yticks(range(0, 27, 2))
plt.tight_layout()

# Show the plot
plt.show()
```

![Figure_12](https://github.com/seanh824/MLBProject/assets/140123586/7b08e11c-efe0-4863-b768-0f21d481fa0b)

The visualization is an extension of the three true outcomes rates by team, as strikeouts are a third of the equation. There appears to be a drop in K% among playoff teams, an effect also shown in the three true outcomes graph. Oddly enough, the strikeout rates of wild card series teams, other than the Tampa Bay Rays, were mostly on par with the Astros. The Astros strikeout rate is a good index to judge other teams because it is one of the lowest in the league. The fact that the Astros continue to lead the league in lowest strikeout rate year in and year out after the sign-stealing scandal is impressive. 


# Summary
In conclusion, this data analysis project delved into the 2022 Major League Baseball season by examining batting metrics like BA, OPS, and OBP, as well as pitching statistics such as ERA, WHIP, and K%. This analysis also takes into account each team's postseason outcome, offering a holistic view of the dynamic trends within the ever-evolving world of MLB. While the database did incorporate some sabermetrics, including OPS and OPS+, it's important to acknowledge that more advanced statistical measures, such as wRC+, WAR, BABIP, and DRS, are available within the [FanGraphs database.](https://www.fangraphs.com/) These advanced metrics offer a deeper and more comprehensive insight into player performance, complementing the analysis and providing a broader perspective on player contributions to their teams.
