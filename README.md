# Mod_5_Project
## The Project
Using soccer statistics to predict transfer value.
## The Transfer Market
Every year billions of pounds are spent in the transfer market. Unlike American sports, where players are traded for other players, in soccer players are sold for money. This is called the transfer market by fans. All summer many fans are absorbed by the latest rumors involving their favorite team. The teams hope that their newest player will improve the team.

For all the excitement their is a dangerous side to the transfer market. In soccer there are rules against spending more money than the club takes in, so if a team spends a great deal of money on a player who doesn't perform it can hurt them for years.

I know from experience how disasterous this can be. In a desperate attempt to achieve promotion, my favorite team, Sheffield Wednesday signed a striker named Jordan Rhodes, who has since proved disappointing. As a result of the Rhodes signing, and similar expensive players who didn't perform, Sheffield Wednesday recieved a 12 point deduction for breaking rules about making a profit and currently have negative points.

![Championship Table](https://lh3.googleusercontent.com/sGm3cI3F1qxgVnHWgiV0L_XtxEhmGWsXNm89yANVPlsz7eOsU1Nsn7fThlVCJn8V0rbX3sgdXBG6g8elT3_1dZ8H06LuuuCXFx1ummA_)

With good evaluation of player value a club can avoid the kind of disaster that Sheffield Wednesday finds itself in. In addition to the use for teams, fans can use a model that predicts transfer value to assess the transfer market in a more educated way.

## The Data

I scraped my data from the website fbref.com and used transfer data scraped from transfermarkt.com by github user ewenme. I performed some some preliminary EDA on the transfer market data. I made the decision to only include data from the previous three years because transfer prices are steadily increasing and I was concerned that data from too long ago would not tell us much about the way clubs currently value players. I combined these two sourced with an inner join, so that I lose any players who have been transfered in the last three years, but don't have statistics on fbref. I also lose players who have statistics on fbref but have not been transfered in the previous three years. Once I scraped all this data I created a csv called database.csv so that I have the data with having to run the scrape notebook repeatedly which takes a long time. All this occurs in Scrape and EDA.ipynb.

In the notebook, Modeling.ipynb fistly I removed all goalies from my data. I did this because the statistics for goalies are completely different. While outfield players have statistics like goals and assists, Goalies have statistics like saves and goals allowed. Next I explored the features that I have. My features were:
* Age
* Position
* Matches Played
* Starts
* Minutes
* Goals
* Assists
* Penalty Goals
* Penalty Attempts
* Yellow Cards
* Red Cards
* Goals per 90 minutes (the length of a soccer match)
* Assists per 90
* Goals plus assits per 90
* Goals minus penalties per 90 (penalties are scored the majority of the time so taking them can artificially increase goal tallies)
* Goals plus assists minus penalties per 90
* Expected goals (expected goals is an advanced statistic that attempts to account for how lucky a player was)
* Non penalty expected goals
* Expected assists
* Expected goals per 90
* Expected assists per 90
* Expected goals plus expected assists per 90
* Non penalty expected goals per 90
* Non penalty expected goals plus assists per 90
I found that there was is a strong positive skew to my data as illustrated in the histogram below.

![Histogram](https://lh3.googleusercontent.com/KxxiL8DtDAS-ldjsMhi4lSx4NzSqPB-YbfoWcK6mHoqvHIK6md2wR1gmRf9viKFCPvygGrNQbOBvB84K5trfsRVTVceNrw7ITQVRJ40)
## Data Preperation

During EDA I found massive outliers that need to be dealt with. In the box plot of assists below you can see that one player has almost 14 assists per 90 minutes. This is an extraordinary rate that isn't remotely sustainable. It turned out that the player only played a few minutes and got one assist so they average 14 assists every 90 minutes. To avoid this and similar situations I capped many of my data features at more reasonable values.

![Assists per 90 box plot](https://lh5.googleusercontent.com/uCBvsSoHsu3vfld4xxweGVa1BAZ4kTmRDCdSaeZNA65VGt00AOi-dh6DyNG4iUt4-jqvZS1hMVkZRYWEiLUhWj0BvPdLfA2kK55vxbcO-379NrgpizeeoVR12J8NymvLiiOrRRmrM1k)

I also know from my experience as a soccer fan that in general attacking players are more expensive. This assumption was backed up in the data. To account for this I added three features, is_attack, is_midfield, and is_defense. I also used get dummies on position data so that I can put it in my model.

## Modeling