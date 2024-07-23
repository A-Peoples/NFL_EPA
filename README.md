Recently, I have been bored waiting for the NFL Season to start playing games again. I was wondering which team in the NFL is the most efficiency. I loaded up Python and attempted to code the solution to my dilemma. I am going to show you my step-by-step for how I’ve created it with Python Pandas.

Explanation of EPA

In order to understand the methodology of my code, you need to learn about the Expected Points Added (EPA). The EPA Formula is the expected amount of points a play is added to a team’s score. The EPA’s value can range from -3 to 7. It multiplies a touchdown with the chance that the team score. The probability is different based on the situation. For example, if a team is on the opponents 1-yard line with a 85% chance of scoring, the formula is calculated as 7(touchdown) * 0.85(chance of scoring) = 5.95(Final EPA).

Coding

Now that you understand EPA, I’ll begin to walkthrough my code. If you want to, you can follow along with this medium. After importing the packages I listed the team names and find out different play type to cycle through them later in the project.

!pip install nfl_data_py

import nfl_data_py as nfl
import pandas as pd

df = nfl.import_pbp_data([2023])

team_names = ('CAR', 'CIN', 'LV', 'IND', 'NYJ', 'DET', 'DAL', 'KC', 'PHI', 'TB',
       'SF', 'LA', 'NE', 'MIN', 'JAX', 'HOU', 'ARI', 'MIA', 'PIT', 'NYG',
       'WAS', 'NO', 'SEA', 'TEN', 'ATL', 'CLE', 'GB', 'LAC', 'BUF', 'CHI',
       'DEN', 'BAL')


df["play_type"].unique()
After doing this, I created a for loop to cycle through each team. For this basic example, I filtered the dataset to only feature the team the dataset was currently on , the formation, the type of play, and the epa. I would then calulate the mean of the combined epa for each formation with .mean(). I also had to concat to combine the newly combined epa and the one generated inside the loop. I copy pasted the code in this section so the pass and the run.

Creating Passing and Rushing Dataframes

#THIS ONE IS THE PASSING ONE
pass_epa = pd.DataFrame()
for team_name in team_names:
  df_2 = df.loc[(df["posteam"]==team_name) & (df["play_type"]=="pass")]
  total_epa = df_2.groupby('offense_formation')["epa"].mean().reset_index().sort_values(by='epa', ascending=False)
  total_epa["name"]=team_name
  pass_epa = pd.concat([pass_epa, total_epa]).sort_values(by='epa', ascending=False)
pass_epa

#THIS ONE IS THE RUSHING ONE
rush_epa = pd.DataFrame()
for team_name in team_names:
  df_2 = df.loc[(df["posteam"]==team_name) & (df["play_type"]=="run")]
  total_epa = df_2.groupby('offense_formation')["epa"].mean().reset_index().sort_values(by='epa', ascending=False)
  total_epa["name"]=team_name
  rush_epa = pd.concat([rush_epa, total_epa]).sort_values(by='epa', ascending=False)
rush_epa

You should get one for pass and rush if you did this correctly
Combining Datasets

In order to correctly combine the datasets and tell the difference between passing and rushing, I had to create new columns indicating if it was a rush or a pass, afterwards merging the datasets with another concat().

pass_epa["stat"] = "pass"
rush_epa["stat"] = "rush"
combined_epa = pd.concat([pass_epa, rush_epa])
combined_epa

The final combined dataset
Final Dataset: Average EPA for NFL Teams
