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



## Clustering

## Conclusion
