# Sentiment in Reporting: A data-driven analysis of bias in 15 major publications

Trust in the media is at an all-time low. There is a common argument that news should "Tell us what happened, not how to feel about it." That is the impetus for this research. Are news sources telling us how to feel? If so, which publications are doing it? Finally, which political party receives more emotional support from the media?

But trust has fallen so low that any research that disagrees with a person's predispositions is also treated as untrustworthy; chalk it up to researcher bias. How can we remove researcher bias? The methods used in this research were chosen to limit researcher interaction with data. How can the research be biased when the researcher barely touches it? 

The primary consideration at every step was the amount of researcher bias that could *arguably* come from the action taken. Thus, the articles tested were not read, they were sorted only by counting mentions of Democrats and Republicans. The sentiment was coded only by the Valence Aware Dictionary and sEntiment Reasoner (VADER), a rules-based sentiment analysis algorithm commonly used in marketing research. Once sentiment polarity scores had been calculated, publications were compared using linear models and post-hoc testing. To examine similarities between news sources, publications were grouped using agglomerative hierarchical clustering, K-means clustering, and DBSCAN.

## Summary

By using completely data-driven analysis, mostly free from analyst input, this exploration draws bias-free inference on political slant in 15 different news publications. A few broad and import insights from the research are:

* Articles about Republicans are (on average) more neutral than those about Democrats
* Articles about Democrats are (on average) more negative than those about Republicans
* The New York Times writes the most positive coverage about Republicans of all tested publications
* The New York Times is the most neutral and most consistent of all tested publications
* Fox News falls within the mainstream
* Fox News is the most negative of all tested publications

There are far more conclusions and insights to be found, but the five above are perhaps most contrary to the general belief.

## VADER

VADER was originally developed by C.J. Hutto and Eric Gilbert from the Georgia Institute of Technology. It is a lexicon and rule based algorithm, and includes a dictionary of slang, acronyms, and emoticons. Study of the algorithm has shown it to be as accurate as human interpreters, and in F1-score VADER has outperformed humans in sentiment analysis. It captures information from punction, capitalization, degree modifiers, polarity shift (due to conjunctions) and polarity negation. It is a very powerful method for sentiment analysis.

VADER can return judgments on both sentiment polarity and intensity; it determines not only whether a statement is positive or negative but also *how* positive or *how* negative. In Python, VADER can be implemented from the vaderSentiment or NLTK packages.

## Data

Data was downloaded from https://www.kaggle.com/snapcrack/all-the-news; there were three .csv files contained a total of 142,570 news articles published between 2011 and 2017, with most published between January 2016 and June 2017. Publications represented in the dataset are:

| Breitbart | Guardian | Talking Points Memo |
| Atlantic | National Review | Vox |
| Business Insider | New York Post | Washington Post |
| Buzzfeed News | New York Times | CNN |
| NPR | Fox News | Reuters |

The data contains article text, titles, publication, author, publication date, and a unique ID. The text appears as written, and VADER uses information such as capitalization and punctuation, so there was minimal text preprocessing. The SentimentIntensityAnalyzer from vaderSentiment returns four measurements:

* 'neg' - Negative Sentiment intensity, scaled to [0, 1]
* 'pos' - Positive Sentiment intensity, scaled to [0, 1]
* 'neu' - A measure of neutrality
* 'compound' - A combined measure of total sentiment, scaled to [-1, 1]

In the compound sentiment, negative numbers indicate overall negative sentiment and positive numbers indicate overall positive sentiment. Numbers farther from zero indicate more intense sentiment. 

After getting sentiment intensities, the articles were separated into two categories: Those that mention Democrats more, and those that mention Republicans more. This was done with simple lexical search: Occurences of the words `['Democratic', 'Democrats', 'Democrat', 'Obama', 'Clinton']` were counted, as were `['Republican', 'Republicans', 'Trump']`. If the words in the first list occured more often, the article was considered to be about Democrats, and if the second list had more occurences the article was considered to be about Republicans. If no words from either list appeared in an article, the article was discarder.

## Sentiment Exploration

Overall, positive sentiment was slightly more prevalent than negative sentiment, and neutrality scores were surprisingly high. The goal of this research was to examine neutrality in reporting, so the neutrality and compound scores were most thoroughly examined.

Neutrality scores were quite similar across publications with three exceptions: Talking Points Memo and Business Insider had unually low neutrality peaks, and the New York Times has an unusually high peak.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/intensity_kdes.png)

See the [full report](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/reports/Sentiment%20in%20Reporting.pdf) for more details, comparisons, and exploration.

## Linear Regression

Whether the news source had a significant effect on neutrality or the compound sentiment score was tested using OLS regression and ANOVA hypothesis testing. Separate tests were performed for each dependent variable and for each group of articles: 4 tests total. 

For Republicans, 14 of the 15 publications showed significant effects on the neutrality score; the lone non-signficiant news source was NPR. Breitbart, Fox News, Buzzfeed News, the Washington Post, and the New York Times had positive coefficients, while CNN, the New York Post, National Review, Vox and others had negative coefficients (indicating less neutrality in reporting). The statsmodels linear regression output appears below.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/rep_linreg_neutral.png)

In testing compound scores for articles about Republicans, all coefficients were positive, indicating that all tested publications had positive average sentiment toward Republicans. Interestingly, the highest compound coefficient was from the New York Times (0.008), and lowest was from Reuters (0.001).

Post-Hoc testing consisted of Tukey's Test for Honest Significant Differences. After taking Bonferroni's correction into account, the lowest significance level that could be accurately considered was $\alpha=0.21$, which is quite high. Still, the HSD test yielded interesting results:

* CNN is not significantly different from Fox News, Breitbart, or the Washington Post
* Breitbart has a significantly lower compound sentiment score than the New York Times, Washington Post, or NPR
* Fox News has a significantly lower compound sentiment score than the New York Times or NPR
* The New York Times has a higher compound score than Reuters, the Washington Post, or Talking Points Memo

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/rep_barplot_tukey.png)

Similarly, the linear regression on articles about Democrats using neutrality as the dependent variable found that 12 of the 15 publications had a significant effect. The news sources that did not have a signficant effect on neutrality were NPR, the Guardian, and Talking Points Memo. CNN, National Review, the New York Post, Reuters and Vox all had negative coefficients (less neutral than average), while Breitbart, Fox News, the Washington Post, New York Times, and others had positive coefficients.

When it comes to compound score, 11 of the 15 publications returned significantly non-zero coefficients. However, only one news source (Reuters) had a p-value greater than 0.1, and all coefficients were positive. See the regression table below.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/dem_linreg_compound.png)

The Tukey HSD test found the following unexpected results:

* The New York Times has significantly higher neutrality in articles about Democrats than any publication except for Fox News and Buzzfeed News
* Vox has a significantly higher compound score than nearly any other publication
* National Review is only different from Vox
* Most publications are only significantly from the three highest: Vox, Atlantic, and NPR; the others seem to form a group

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/dem_barplot_tukey.png)

## Clustering

#### Agglomerative Hierarchical Clustering

In articles about Republicans, the two most similar publications are the New York Post and Reuters, Breitbart's nearest neighbor is the Washington Post, and Fox News is closest to Buzzfeed News. National Review, Vox and Atlantic are a separate cluster from the other publications.

When plotted on an axis of compound score by neutrality, the New York Times is far off by itself, in the high-neutrality-high-compound-score corner.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/rep_aggclust.png)

In articles about Democrats, the two most similar publications are again the New York Post and Reuters. However, this time thw Washington Post is closest to the New York Times and Fox News is closest to Breitbart. Atlantic and Vox are in their own separate cluster. 

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/dem_aggclust.png)

When all articles are considered together, the publications form four distinct groups.

| Group 1 | Group 2 | Group 3 | Group 4|
|:-------:|:-------:|:-------:|:------:|
| NYT | Business Insider | Guardian | National Review |
| NPR | Talking Points Memo | New York Post | Atlantic |
| CNN | Buzzfeed News | Reuters | Vox |
| Washington Post | Breitbart | | |
| | Fox News | | |

In the plot with axes of neutrality and compound sentiment, publications are much more spread out than when the articles are split. However, the New York Times is still off by itself in the high-neutrality-high-compound-score corner.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/all_aggclust.png)

#### K-Means Clustering

Articles were clustered separately. Through trial and error, it was determined that three seemed to be the best number of clusters. These turned out to be:

* Articles with negative compound scores and low neutrality
* Articles with positive compound scores and low neutrality
* Articles with high neutrality

Once articles had been clustered, the proportion of articles from each publication in each cluster was compared to the proportion of articles from that publication in the dataset as a whole. Thus, it was possible to determine which news sources were over-represented or under-represented in each cluster. See the [full report](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/reports/Sentiment%20in%20Reporting.pdf) for more details.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/all_kmeans.png)

#### DBSCAN

The DBSCAN algorithm automatically tunes the number of clusters and puts all observations that do not fall into one of the clusters into an "other" category. In this case, there was one cluster, which was deemed "mainstream", and all other articles were outliers. As in the K-Means section, the proportion of articles from each publication in each cluster was compared to the total proportion of articles from that publication to compare which news sources were over-represented or under-represented in each cluster.

![](https://github.com/JohnTheQuantYoutube/SentimentInReporting/blob/main/plots/all_dbscan.png)

## Conclusion

Sentiment in political reporting was compared between 15 different news sources and tens of thousands of articles. The average neutrality and compound sentiment was compared by linear regression, direct comparisons were made between publications using Tukey HSD tests, and then three different clustering techniques were used to explore the relationships between news sources more closely. Some interesting conclusions:

* Fox News seems to be fully within the mainstream, despite being the only publication with a negative mean compound sentiment score
* NPR is under-represent in the mainstream cluster
* Articles about Democrats tend to have stronger negative sentiment than articles about Republicans
* The New York Times is incredibly neutral and consistent in its reporting
* National Review is more similar to Atlantic and Vox than to Breitbart or Fox News

In further research, it would be interesting to examine these relationships over time, especially through the Trump administration. It would also be interesting to compare web-based media outlets to tradition television/paper media and examine quality of reporting on different platforms. 

There is an ever-growing wealth of articles available to be analyzed, and with the current political unrest in the United States, the quality of news being ingested by the population is more important than ever. Recent improvements in data-based text analysis allow for drawing bias-free inference about political slant; this analysis should be trustworthy even to the most entrenched consumer.
