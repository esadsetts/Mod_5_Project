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

I scraped my data from the website fbref.com and used transfer data scraped from transfermarkt.com by github user ewenme. I performed some some preliminary EDA on the transfer market data. I made the decision to only include data from the previous three years because transfer prices are steadily increasing and I was concerned that data from too long ago would not tell us much about the way clubs currently value players. I combined these two sourced with an inner join, so that I lose any players who have been transfered in the last three years, but don't have statistics on fbref. I also lose players who have statistics on fbref but have not been transfered in the previous three years. Once I scraped all this data I created a csv called database.csv so that I have the data with having to run the scrape notebook repeatedly which takes a long time.

Next, I removed all goalies from my data. I did this because the statistics for goalies are completely different. As a future step, I could design a model for goalies. While outfield players have statistics like goals and assists, Goalies have statistics like saves and goals allowed. Next I explored the features that I have. My features were:

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

After performing a train test split and scaling my data with a standard scaler, I began modeling. I chose to use root mean squared error (RMSE) as my evaluation metric. I chose this becuase RMSE is the best metric to assess how good a model is at predicting values, which is what I intended my model to be used for. For every model that I could I tuned hyperparameters using grid search cross validation.

The first model I ran was a dummy model that always predicted the mean value. I used this model as a baseline to assess my more complex models against. The dummy model had a test RMSE of 12.25

The next model I ran was a linear regression model. My linear regression model ended up performing quite well, with an RMSE of 11.46.

I then ran a ridge model which performed slightly worse than my linear regression model. The test RMSE on my ridge model was 11.62.

Next I ran a lasso model. The lasso model performed relatively poorly. It had an RMSE of 11.68.

My next model was a support vector machine. This model also did not perform very well. The RMSE was 11.92.

Among my worst models was a decision tree. My decision tree had an RMSE of 12.38, worse even than my dummy model.

My best performing model was a random forest. The random forest had an RMSE of 11.24.

The most complex and final model I ran was an XG boost model. The XG boost model had an rmse of 11.42, just a little worse than the random forest model.

## Analysis

In the end, none of my models performed as well as I had hoped. When my mean is 8.22, an RMSE of 11.37 is not that great. When viewing the residual plot below, you see that my model actually performs quite well when predicting high transfer values. However, my model does significantly underestimate the value of many players. I think this is caused by the statistics I used in constructing model. While goals and assists are good statistics for evaluating attacking players, they are not good for defenders, who I think my model routinely undervalues. As a next step, I will find defensive statistics and incorporate them into my model.

![Residual Plot](https://lh6.googleusercontent.com/396Bbt68ZTjl5k6kKZiDh6Z7ocVvd4RoA0q8PMHPQYM8Sm4HiSgc8U3i7a3UrVFI_qqSyV9zGS8Pe42TRQNpVguL_WCVJR2TgYHgK1-XZj7tt-pFgdjzaxML8gyzRQSV1STqLjIt5QE)

Despite the problems my model still has, you can draw interesting conclusions from the feature importances. Unsurprisingly, assists and goals were two of the most important features, the other being non penalty expected goals. Expected statistics did have some importance in my model. The third most important statistic in my model was non penalty expected goals. Expected goals and expected assists were both used, but both were less important than goals and assists. Most positional data did not turn out to be important in the model.

## Navigation

I have 4 Notebooks in this repo that are all pretty self explanatory. In the notebook scrape, I perform my webscraping and data collection. In the notebook data cleaning, I cleaned the data as described above. In the eda notebook, I explore my data, and in the modeling notebook I model. All the steps involved in that process are described above.

## Sources

* fbref.com
* transfermarkt.com
* github.com/ewenme/transfers
* soccerstats.com

In addition, [this](https://docs.google.com/presentation/d/1U972OgnZSR4_1-3fxJr4ywPuWQkqS02mTgUVUxVd-vw/edit#slide=id.p) is a link to my presentation of this project.