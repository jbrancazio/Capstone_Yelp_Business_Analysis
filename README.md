# Flatiron Capstone Project

## Abstract

The purpose of my capstone project is to showcase the skills I have learned in NLP, feature engineering and modeling through the exploration of yelp review data. I used the [yelp challenge dataset](https://www.yelp.com/dataset/challenge) for this project and focused on the user and review data. I will also be using the OSEMN process to organize my project.  

The main question I am trying to answer **there is an underlying relationship between the reviews members write on yelp and the resulting score given?** In order to answer this question, I plan on creating a model that not only uses the review information but also uses information about the user, and numerical information about the review itself. I will finish this project by obtaining new data and testing my pipleine and models on that data. 

## [Obtain](/Obtain.ipynb)

This dataset consists of 5 json files:

1. **tip.json**: Tips written by a user on a business. Tips are shorter than reviews and tend to convey quick suggestions.
2. **photo.json**: Contains photo data including the caption and classification (one of "food", "drink", "menu", "inside" or "outside").
3. **checkin.json**: Checkins on a business.
4. **user.json**: User data including the user's friend mapping and all the metadata associated with the user.
5. **review.json**: Contains full review text data including the user_id that wrote the review and the business_id the review is written for.
6. **business.json**: Contains business data including location data, attributes, and categories.

These data sets contains millions of rows of data so I have decided only to use 400,000 reviews for my analysis. This should give me a good subset of the data and allow me to answer my questions accurately and in a timely manner. 

## [Scrub](/Scrub.ipynb)

This step is where I completed most of my feature engineering. While looking at the actual review and using finding patterns in the words are exciting, there is more information that can be gleaned from the review other than just words. The goal with creating new features is to turn my review data into features that might capture more subtle nuances within the text and hopefully aid in model creation.  The main features I created are the following:

- **Word count**: Number of words in a review
- **Avgerage word length**: Average length of the words in the review
- **Number of Stop Words**: Number of stop words which are commonly used words such as “the”, “a”, “an”, “in”.
- **Percentage of non stopwords**: Percentage of the reviews that does not contain stopwords
- **Polarity**: A value [-1,1] that expresses the sentiment of the review where -1 is a negative statement and 1 is a positive statement.
- **Subjectivity**: A value [0,1] thast expresses the sentiment of the revied where 0 is a factual statement and 1 is popular opinion.

I also took this opportunity to clean the text data of all stop words and punctuation so that the words left hold greater meaning.


## [EDA](/EDA.ipynb)

This is the most open ended part of the project and one of the most exciting aspects. I approached this section by trying to answer a few key questions about my data. Part of the strength of the OSMIN process is that it is iterative and the Exploratory Data Analysis (EDA) process is where this is most visible in my opinion. While trying to answer questions, you often find that you need more data or need it in a different format which means you have to return to previous steps. Some of the questions I have been trying to answer are:

- What is the breakdown of reviews given by customers?
- Is there a difference in language used based on the number of stars given? 
- Are there any important word associations and how do we capture those?
- Are there any themes that emerge from the data and do those themes vary by number of stars?

The code can be found in the EDA jupyter notebook but the resulting graphs and findings are below. 

#### What is the breakdown of reviews given by customers?
![Countplot of Reviews by Stars given](/images/number_of_reviews.png)
As you can see, there is a much larger amount of five star reviews than any other category. This will naturally skew the data a little towards the 5 star reviews due to the large number of samples, as well as splitting by stars given, we can over come this. 

#### Is there a difference in language used based on the number of stars given? 
![Polarity by number of stars](/images/Yelp/FE_violinplot_polarity.png)
![subjectivity by number of stars](/images/Yelp/FE_violinplot_subjectivity.png)
![Non-stopwords Percentage by number of stars](/images/Yelp/FE_violinplot_non_stop_percent.png)

As you can see, there is some variance in the Polarity, Subjectitity and Percentage of Non-stopwords between the different stars. It seems that as the number of stars given increases, the polarity and subjectivity increase but the percentage of Non-stop words decreases.  This would indicate that when giving 5-star reviews, customers tend to be more positive, more subjective and use more descriptive i.e. less common words. 

I was curious about this phenomenon so I decided to explore it a little more. One feature that was provided is the number of average stars given by a user and so I decided to use that to see if the same phenomenon continued to hold true for users based on their tendencies of giving stars. 
<br>
![Polarity by average number of stars](/images/Yelp/Average_stars_scatterplot_polarity.png)![Subjectitiy by average number of stars](/images/Yelp/Average_stars_scatterplot_subjectivity.png)
![Non-stopwords Percentage by average numver of stars](/images/Yelp/Average_stars_scatterplot_non_stop_percent.png)

While the trends are not strong, there seems to be some pattern that users who on average give more stars, also tend to be more positive and use less common words. 

#### Are there any important word associations and how do we capture those?

This question came out of exploration of the Gensim word2 vec model I created. I found rhart rthere were certain words that appeared next to each other fairly often and so I wanted to make sure I understood if they are associated with each other or not. A great example of this is Dr. J Phillips. These words appeared next to each other often but are not actually tied together. I decided use a bigram finder and see if I could combine hese words. I ran the bigram finder twice, once on the original data and onc new bigrams in order to successfuly create the trigram "dr j phillips". I then used this scrubbed data, with stop words removed for the rest of my project. 

Below is an example of words that appear close to sandwhich. As you can see, the trigram model has successfully created some associations.
![Word2Vec most similar to sandwhich](/images/Yelp/sandwhich.png)


#### Are there any themes that emerge from the data and do those themes vary by number of stars?

I used gensim's LDA model to determine whether there were any themes in the reviews. Below is a visual of the themes for 1 star and 5 star reviews. 

![Graph of themes for 1 star reviews using frequency count and importance](/images/yelp_keyword_graph.png)
![Graph of themes for 5 star reviews using frequency count and importance](/images/yelp_keyword_graph_5star.png)

Based on the key words, it is possible to start extrapulating possible themes. For example:

***One Star Reviews***
As you can see from the graphs below, this attempt to create topics has been partially successful. Some topics, such as topic 0 seem to be too broad to really be useful. It seems to follow a restaurant theme however the word count is much larger than the associated weight which would indicate that those words do not hold as much importance as it would seem. Another great example of a topic that is too broad is topic 8.

There are some topics though that seem to have been successfully learned. Topic 1 seems to refer to a pharmecy/doctors office, maybe with a dr. cox. Topic 2 refers to airlines and airports, likely allegiant. 
[Graph of word count and importance](/images/Yelp/yelp_keyword_graph.png)

***Five Star Reviews***

When looking at the topics within 5 star reviews, there seem to be less clarity. Topic 6 seems to refer to a donut, breakfast place and topic 9 is describing a barbershop. Topic 0 refers to good service and there are a few restaurant topics as well. 

[Graph of word count and importance](/images/Yelp/yelp_keyword_graph_5star.png)

The next step for this model is t return to my text cleaner and remove words that have a significantly higher word count than their associated weights.


## [Model](/model.ipynb)

I started by running a few very simple models on both the text itself as well as the numerical features I created from the text. The models I ran and their performance can be seen below. 

Supervised Learning Models:
1. Text Models
    1. Naive Bayes Model: *64.62% (+/- 0.2514%)*
    2. Logistical Regression Model: *68.65% (+/- 0.2543%)*
    3. Random Forest Model: *44.19% (+/- 0.04548%)*
    4. Linear SVC Model: *66.64% (+/- 0.1989%)*
    
![Model performance for text models](/images/Yelp/MP_Text.png)

2. Numerical Models
    1. Naive Bayes Model: *50.08% (+/- 0.3167%)*
    2. Logistical Regression Model: *53.46% (+/- 0.1023%)*
    3. Random Forest Model: *53.74% (+/- 0.1313%)*
    4. Linear SVC Model: *53.45% (+/- 0.08733%)*
![Model performance for numerical models](/images/Yelp/MP_num.png)
    
After completing these supervised models I attempted to see if I could improve my model performance by combining multiple models together. I started with a combination of the two Logistical Regression Models because they were quick and strong performers. I found that the best result on my test data came from a model that was based 100% on my text model and used no numerical data. 
 
I decided to combine both my SVC Text model as well as my Logistical Regression Model and found that a balance of 66.6% Logistical Regression and 33.3% SVC performed well and was receptive to new data. This is the model I saved and used in my final pipeline. 

## [Final Pipeline](/final_pipeline.ipynb)

In order to test my final pipeline I decided to build a [simple scraper](https://github.com/Jbrancazio/Flatiron_Capstone/blob/master/yelp_scraper.ipynb) that would allow me to take a business url and scrape for their reviews. I used this on a company I am currently working for as an example of how it works. The model achieved a 75% accuracy when predicting number of stars given.
