### Overview

The goal of the project is to develop an algorithm that allows to predict if particular reddit post belongs to the subreddit r/DunderMifflin based on post's title and body. To evaluate how good is the prediction, I will be using accuracy score along with several other supplemental metrics like recall, precision, F1 score. 

The project is structured the following way:
1. Data Collection
2. Data cleaning and preprocessing
3. Natural Language Processing Exploratory Data Analysis
4. Classification
5. Choosing the top-performing classification model to address the problem statement

---

### Data Collection
I used [PRAW API](https://praw.readthedocs.io/en/stable/) to download posts from [r/DunderMifflin](https://www.reddit.com/r/DunderMifflin/) and [r/office](https://www.reddit.com/r/office/) subreddits. 
* r/office: 793 posts, 67216 words, 85 words per average post
* r/dunderMifflin: 993 posts, 29274 words, 29 words per average post

### Data Cleaning and Preprocessing
First, I dropped all excessive columns. Then merged title and body text into one column.
After first iteration of EDA, I've noticed that hyperlinks had generated too much irrelevant data, so I got rid of them. I also removed special characters, that are used for text formatting on reddit. 

### NLP EDA

During EDA, I've explored how posts' length differes between subreddits, what words, bigrams and trigrams are most recurrent for every subreddit, and what do they have in common. That information giuded me when choosing models hyperparameters in the next section

![Common words](/images/most_frequent_words_nltk.png)

Based on the words that were equally present in both subreddits, I've created a custom stop word list, which was used during the classification.

### Classification
Based on the web search, Multinomial Naive Bayes and K Nearest Neighbors Classifier are the most common and best performing models for document classification in the wild from what we've covered in the course so far.

I tried both classification algorythms with Count Vectorizer and TF-IDF Vectorizer. Vectorizer type didn't affect models' performance significantly.

![ROCs](/images/ROCs.png)

| model | train | test | recall | precision | f1_score |
| --- | --- | --- | --- | --- | --- |
| **MNB_cvec** | 0.948 | 0.933 | 0.928 | 0.951 | 0.939 |
| **MNB_tvec** | 0.953 | 0.935 | 0.932 | 0.951 | 0.941 |
| **kNN_tvec** | 0.931 | 0.796 | 0.876 | 0.784 | 0.827 |
| **kNN_cvec** | 0.875 | 0.803 | 0.900 | 0.780 | 0.836 |

Best-performing model is Multinomial Naive Bayes using TF-IDF Vectorizer with following hyper-parameters:

* max_df: 0.8
* max_features: 2000
* min_df: 2
* ngram_range: (1, 1)
* stop_words: custom stop words
* alpha: 0.2

---

### Conclusions

During the project, I created a model allowing to predict whether given post belongs to subreddit r/DunderMifflin. Accuracy of predictions is 94% which outperforms baseline accuracy by 28%.