# Predicting NBA All-Stars Using Linear Regression Models

## Executive Summary:
Each season, 24 of the NBA’s elite are selected to the All-Star Team: a commemoration of the most popular and talented players in the league. We analyzed the performance of players during the 2020-2021 season in order to find out which attributes had the greatest impact on a player’s selection to the All-Star squad. After devising a suitable model, we used players’ performance during the second half of the prior season in order to predict All-Stars in the current season, and conducted a corresponding analysis of our results.

## Introduction:
Every season, the NBA hosts the All-Star Weekend, a three-day event where a collection of the league’s stars and rising talents showcase their skills throughout a series of exhibition games and events. The centerpiece of the All-Star Weekend is the All-Star Game: a full-length exhibition match played between the best players in the Eastern and Western Conferences. 

Selection to this event is a testament to both a player’s dominance and longevity within the league, as evidenced by Kareem Abdul-Jabbar, Kobe Bryant, and Lebron James topping the record books with 19,18, and 17 selections respectively. However, the award holds importance beyond merely a player’s legacy, as oftentimes salary incentives are structured around performance criteria such as All-Star selection. Because of the nontrivial implications of being an All-Star, the NBA revised the selection process in 2017 to reduce fan impact on voting - from 100% fan-based to now include player and media opinion.

The current weighted ranked vote consists of 50% popular fan votes, 25% current players, and 25% media representatives. This weighted average is computed by ranking each player within their conference and position (backcourt vs frontcourt) across each of the voting channels (fan, player, media). For example, budding superstar Trae Young ranked 6th among fans, 11th among players, and 6th among media within Eastern Conference guards and did not make the All-Star team. The top two guards and top three frontcourt players among each conference are selected as starters, with the remaining seven players on each team being chosen by head coaches within the conference. If a player selected as an All-Star is injured prior to the event, a replacement is selected by the All-Star coach or the NBA Commissioner. For the purposes of this project, we ignored players selected to the team in this fashion.

## Problem Statement:
Considering the importance and year-to-year variability of being selected as an All-Star, either as a starter or reserve, predicting a player’s likelihood of selection has far-ranging implications. These include future salary and potential legacy, their team’s success and popularity, as well the ability to attract free agents to the franchise. As such, forecasting the chance of a player’s selection to the All-Star roster is a compelling proposition. 

To do so, we conducted linear regression using box score data on all NBA players from the first half of the 2020-2021 NBA season, with the key variable of interest being a player’s weighted All-Star Voting rank (the average of the fan, player, and media vote). After assessing the relative value of a range of attributes such as Win/Loss, Points per Game, Age, and so forth, we devised a model to predict whether or not a player will make the All-Star Game by comparing their forecasted All-Star Voting Rank to the necessary score to make the game in their position and conference. By computing a new All-Star Rank using the latter half of the season’s data, we predict whether or not each player would similarly make the All-Star Game during the 2021-2022 season. Of course, we would not yet be able to verify this assumption as the game occurs in February of 2022 and is based upon player performance during the current season rather than the former.

## Data Description:
Our core dataset on player performance comes from NBA.com - the official source of NBA-related statistics. We concatenated the primary data with All-Star Voting dating sourced from Basketball-reference.com, the premier source for basketball stats across leagues. The initial data set displays seasonal performance on a per-player basis up until the 2021 All-Star game. We then take the second half of this same dataset (up until the end of the regular season) to predict All-Star Rank. Examples of the variables included in this dataset are age, wins, minutes per game, points per game, and so forth. 

One issue that arose while joining the player performance data with All-Star Voting data related to the players with exceptionally forgettable seasons - those most likely to not receive any votes. Such players who did not receive All-Star votes did not appear in the All-Star voting dataset, and as such could not be immediately mapped to seasonal performance. While we had the option of simply assigning them the lowest All-Star Voting rank in the dataset (157.5), we elected not to do so as the presence of such outliers could have skewed our regression models. In any case, the number of players not included in the All-Star data was negligible: only 20 of the bottom feeders in the league were excluded relative to the 500 players considered in the initial dataset.

Following is a description of the variables included in our analysis on the 2020-2021 NBA season; not all of them are incorporated into the final model. Certain variables become redundant as they are a composite of other attributes. e.g. Rebounds = Offensive Rebounds + Defensive Rebounds. 

ID (player ID), PLAYER (player name), TEAM (team),AGE (age), GP (games played),	W (wins), L (losses), MIN (minutes), PPG (points per game),FGM (field goals made),	FGA (field goals attempted), FG% (field goal %), 3PM (three pointers made), 3PA (three pointers attempted), 3P% (three point %), FTM (free throws made),	FTA (free throws attempted), FT% (free throw %), OREB (offensive rebounds), DREB (defensive rebounds), REB (rebounds), AST (assists), TOV (turnovers), STL (steals), BLK (blocks), PF (personal fouls), FP (fantasy points), DD2 (double-doubles), TD3 (triple-doubles), Plus/Minus (box plus/minus), AllStarRank (weighted average All Star rank), AllStar (binary All-Star selection), Conference (East/West), Position (Front/Backcourt).

All variables are quantitative (integer or numeric) except for PLAYER, TEAM, Conference, and Position (qualitative).

## Regression Analysis:
### i) Plots of Variables:
To start off our analysis, we decided to plot the major components of the box score (Points Per Game, Rebounds, Assists) as well as Wins. The primary stats within the box score typically constitute the “eye test” for a player’s skill level, while wins serve to differentiate “skilled” players from “valuable” players. We hypothesize that these variables will be important and influential for the model and as such we want to look at their distribution. 



​​
From the above plots, we can see that there is a strong negative correlation between the various stats and All-Star Rank.

### ii) Correlation:
We then plotted the correlation between the variables used in the model to check for multicollinearity. 



As you can see from the above figure, there is a high degree of positive correlation between some of the variables such as field goals made and field goals attempted. This makes sense as many of these variables have an inherently causational relationship. For example, more field goals attempted should lead to more field goals made. Because of this, we will likely not need two such variables which tell us similar information for the final model.

## Model Selection Process:


We choose model 8(1). This has the highest adjusted R2 value and lowest  Cp. The 8 variables are Age, Games Played, Wins, Points per Game, Field Goals Attempted, Field Goal Percentage, Rebounds, Assists. 

## Multicollinearity:
After selecting these variables, we wanted to check multicollinearity as not to have multiple variables giving us the same information.



As is shown by the VIF scores above, PPG and FGA both have a high degree of multicollinearity (VIF>10). Keeping this in mind, we run backward stepwise variable selection using AIC as the metric. The final step of this process is shown below:


Given that PPG was in this list of variables and FGA was not, we decided to remove FGA to try to alleviate the issue of multicollinearity. We also added DD2(double-doubles) and Plus.Minus(Plus-Minus) as the backward stepwise regression showed these variables could be important. After this change in variables, we again run the VIF scores.

There is no apparent multicollinearity within this new model as all of the VIF scores are less than the threshold of 10. This new model (model8var2) also retains a high adjusted R2value of 0.7335. 

### iii)Residual Analysis:
One of the assumptions in the use of a linear model is that the residuals show constant variance and do not grow or decrease in a noticeable pattern. We plot the residuals below measured against the player index(which is sorted by PPG). 

We can see that the constant variance assumption is violated as there is a growth in the spread of the residuals as the index increases.

Another assumption that we make in using the linear model is that the residuals are normally distributed. We also graph the normal probability plot which in theory can tell us if there are places in the data in which the residuals significantly veer off of the Normal Distribution. The variable y represents AllStar Rank.

As can be seen from the above plot, the residuals have a significant difference from that of the normal distribution along with both extremes. 
	To fix these issues, we perform several transformations on the AllStar Rank. These transformations are square root, quartic root, and natural log. We show the residual plots under the transformations below.








While the natural log and quartic root transformation seem to make the problem of differing variance worse, the square root transformation leads to a higher degree of constant variance. We then plot the normal probability plots under the transformations and show those results below.

Again, the quartic root and natural log transformation seem to maintain or exaggerate the problem of non-normally distributed residuals. The square root transformation however seems to generally alleviate the issue.

## V) Analysis of Outliers:
Using the leverage values and their respective cutoff, we find the below players to be outliers with respect to the independent variables.

Utilizing studentized residuals and their respective cutoff, we can see the below players are outliers with respect to the square root of the AllStarRank.



## VI) Influential Points:
We computed the quantile over the F-distribution to determine the possible influential points based on the Cook’s Distance. Our results found no influential points (Cooks D>F0.5) or points with the potential to be influenced (CooksD<F0.5 and CooksD>F0.8)

## VII) Performance Measures:
Our model had an MSE (Mean Squared Error) of 1.276448, MAPE (Mean Absolute Percentage Error) of 16.03014, and a MAD(Mean Absolute Deviation) of 0.8710102. Given the high degree of volatility in predicting all-stars, we believe that these metrics show that our model is fairly robust and has utility. 

## Conclusion/Summary/Recommendations:
After passing in the second half of the 2020-2021 season's player data to the optimized regression model, we observed the new AllStarRank for each player. Using the second half of the season as a proxy to determine All-Star selections for the 2021-2022 season, we identified the five starters on each team. We sorted the new AllStarRank in ascending order, accounting for position and conference to capture the following 15 highest ranked players (shown in appendix).

As seen in the table, the top two backcourt and top three frontcourt players in each conference make the starting roster. Considering the changes in the starting roster vs. the actual All-Stars we see that among Eastern frontcourt players Jayson Tatum (4th in the original standings) took the spot from Kevin Durant. Among Western frontcourt players, Zion Williamson (8th) and Karl Anthony-Towns (19th) took spots from LeBron James and Kawhi Leonard. Among Eastern backcourt players, James Harden (originally 3rd) received a starting spot over Kyrie Irving. Furthermore, Donovan Mitchell (4th) and Shai Gilgeous-Alexander (8th) received spots over Stephen Curry and Damian Lillard. 

Noting that all “replacement players” were typically within the original top 10 in their respective conference and position type, we conclude that the model is not obviously misrepresentative. However, we recognize that this mechanism is likely underestimating the effect of Wins and superstardom. For example, Zion, KAT, SGA, and Fox were all players on heavily losing teams, which heavily decreases their chances of being selected as starters. Furthermore, we think the effect of raw popularity is being undervalued; when LeBron and Steph are playing at an elite level, there is a negligible chance that Zion or Donovan Mitchell would displace them as starters. SGA also figures as an outlier as he was injured post-All-Star Break and therefore deactivated as a player - meaning his averages came from limited sample size and should preclude him from selection. 

Of course, the latter half of the prior season's performance might not be a perfect indicator for a player's current season, so we would recommend rerunning the numbers with updated data. One option would be to merge the second half of last season's data with data from the 2021-2022 season thus far, but that creates uncertainty as all players' performance has not been consistent across seasons: regression and breakouts as misleading trends and retirements and rookies posing outliers (as their data would be unmergeable). 

We also elected not to predict reserves because of the wider variability in their selection, considering that coaches can arbitrarily choose them. In order to conduct further analysis, we could identify a grouping of players that fall into a range which makes them likely candidates for selection as a reserve. By evaluating the All Star Rank of historically selected reserves as well as potentially assigning greater weight to certain variables (e.g. age, as all-time players playing into the twilight of their careers might get a selection, as well as veterans previously snubbed, etc.) we could develop a separate model to similarly predict All-Star reserves. 

Considering the reasonability of our model, we recommend that players aspiring to receive All-Star recognition consider the variables we highlighted in order to cement their status as legends and maximize earnings potential. 






## Appendix:
















Optimal Linear Regression Model & ANOVA Table 



ID
AllStarRank
Name
Conference
Position
All-Star Starter?
2
1.180892
Donovan Mitchell
West
Backcourt
Yes
10
1.41396
Shai Gilgeous-Alexander
West
Backcourt
Yes
16
1.530637
Nikola Jokic
West
Frontcourt
Yes
5
1.60616
De'Aaron Fox
West
Backcourt
No
14
1.916234
Joel Embiid
East
Frontcourt
Yes
3
2.262009
Bradley Beal
East
Backcourt
Yes
8
2.271601
Giannis Antetokounmpo
East
Frontcourt
Yes
4
2.621146
Zion Williamson
West
Frontcourt
Yes
6
2.862073
Jayson Tatum
East
Frontcourt
Yes
1
3.046502
Stephen Curry
West
Backcourt
No
26
3.223893
James Harden
East
Backcourt
Yes
12
3.439643
Devin Booker
West
Backcourt
No
13
3.44279
Karl-Anthony Towns
West
Frontcourt
Yes
9
3.502565
Luka Doncic
West
Backcourt
No
21
3.510287
Trae Young
East
Backcourt
No

Predicted All-Stars













Name
ID
PLAYER
TEAM
AGE
GP
W
Description
Player ID ordered by PPG
Player name
Player team
Player age
Games played
Wins
Type
Integer
Character
Character
Integer
Integer
Integer


Name
L
MIN
PPG
FGM
FGA
FG%
Description
Losses
Minutes per game
Points per game
Field goals made
Field goals attempted
Field goal percentage
Type
Integer
Numeric
Numeric
Numeric
Numeric
Numeric


Name
3PM
3PA
3P%
FTM
FTA
FT%
Description
Three pointers made
Three pointers attempted
Three point percentage
Free throws made
Free throws attempted
Free throw percentage
Type
Numeric
Numeric
Numeric
Numeric
Numeric
Numeric


Name
OREB
DREB
REB
AST
TOV
STL
Description
Offensive rebounds
Defensive rebounds
Rebounds
Assists
Turnovers
Steals
Type
Numeric
Numeric
Numeric
Numeric
Numeric
Numeric


Name
BLK
PF
FP
DD2
TD3


Description
Blocks
Personal fouls
Fantasy points
Double doubles
Triple doubles


Type
Numeric
Numeric
Numeric
Integer
Integer




Name
AllStarRank
AllStar
Conference
Position
Plus/Minus
Description
Weighted average All Star Ranking
All-star selection (binary)
Player conference
Player position
Box plus/minus (point differential when player in on court)
Type
Numeric
Integer
Character
Character
Numeric

Variable descriptions



## References:

2021 NBA All-Star Game Voting Scores. Basketball. (n.d.). Retrieved November 20, 2021, from https://www.basketball-reference.com/allstar/NBA_2021_voting.html
Players advanced. NBA Stats. (n.d.). Retrieved November 20, 2021, from https://www.nba.com/stats/players/advanced/?sort=GP&dir=-1. 


