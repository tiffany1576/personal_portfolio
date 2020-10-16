
---
 ## <b>USING NLP TO DETERMINE THE SUBREDDIT OF A REDDIT POST

### Table of Contents
1. [Background](#background)
2. [Problem Statement and Goal](#goal)
3. [Data Acquisition](#acquisition)
4. [Data Cleaning](#cleaning)
5. [Models](#models)
6. [Outcomes](#outcomes)
7. [Conclusions](#conclusion)


### Background <a id = 'background'></a>
Reddit is a large information aggregation site. Information is categorized by topic into subreddits.
Each subreddit is comprised of user-submitted posts and user-submitted comments on these posts.


### Problem Statement and Goal <a id = 'goal'></a>
I selected 2 text-heavy personal anecdote subreddits: [MaliciousCompliance](https://www.reddit.com/r/MaliciousCompliance/)  and [ProRevenge](https://www.reddit.com/r/ProRevenge/).  The question-- how different is malicious compliance from pro revenge? Are there language cues that indicate revenge over compliance? 

### Data Acquisition <a id = 'acquisition'></a>
Using the Pushshift API, I performed 100 pulls of 100 posts per pull, for a total of 10,000 posts
from each subreddit. After removing duplicates and posts that had been deleted or removed by subreddit moderators, the remaining strings were broken into "words" or tokens using the [TweetTokenizer](https://www.nltk.org/api/nltk.tokenize.html) in NLTK. 

### Data Cleaning and Engineering <a id = 'cleaning'></a>
Tokens were reduced to their root words using lemmatizing and filtered against the built-in English stopwords dictionary, as well as a custom stopwords dictionary. The custom dictionary is a list of tokens that appear in both the top 500 tokens in Malicious Compliance and Pro Revenge. This added 421 tokens to the stopwords filter.

Tokens were then vectorized using TFIDF. Count Vectorization gave the same results, since the data was already tokenized and processed prior to vectorization. 

### Models <a id = 'models'></a>
The first model used to classify and predict was a Bernoulli Naive Bayes model. The vectorizer used looked at whether the token is present in the document-- a binary classifier. Bernoulli is designed for binary feature variables -- which is what the vectorized tokens represent. More information on Bernoulli is available [here](http://www.inf.ed.ac.uk/teaching/courses/inf2b/learnnotes/inf2b-learn-note07-2up.pdf)

A dummy model was also established as baseline. The dummy model classified texts purely based on majority class.

A logistic regression model was then used for comparison.

Finally, a decision tree model was created with maxdepth of 3 nodes to determine which model performs the best on this set of data to classify posts by subreddit.

### Outcomes<a id = 'outcomes'></a>

Simply using a dummy classifier to categorize subreddit posts based on majority class resulted in 53.42% accuracy. Using the Bernoulli Naive Bayes classifier increased the accuracy to 73.74%. For these 2 subreddits, there is a significant improvement by using the presence (or absence) of tokens to qualify posts with the Bernoulli model.
Logistic regression returned 84.50% accuracy. I tested both SAGA and lbfgs solvers and obtained the same outcome.
The decision tree scores were 71.11% on training data and 73.5% on testing data. Not a bad showing, but not stronger than logistic regression.

In classification problems, specificity is also an important metric. Specificity is defined as the true negatives divided by all predicted negatives (the sum of true negatives and false positives). Specificity for the Bernoulli is 88.0% and specificity for the logistical regression is 88.26%


### Conclusions<a id = 'conclusion'></a>
The logistic regression performed the best in classifying the subreddit posts into the correct subreddit.

 The results from the non-optimized logistic regression are sufficiently accurate to answer the primary question-- can we predict which subreddit a post came from? In spite of the seemingly similar subject matters, the answer is yes!

 Future developments would include tuning the logistic regression model, perhaps with a ROC curve, incorporating n-grams into the tokenization process, and using token frequency instead of token presence to vectorize the tokens.









