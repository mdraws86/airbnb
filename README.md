# Airbnb Analysis

## Introduction
This analysis is carried out as part of the Data Science training by Udacity. The choice of data and the questions to be answered as part of the analysis were left to the course participants themselves.
Using Airbnb data was a suggestion that I followed because I have used this platform myself on vacation and it is real data. Thus I expect that I also will face real world challenges with the data which leads to a maximum learning experience.

The foundation for the present analysis are the airbnb datasets for [Seattle](https://www.kaggle.com/datasets/airbnb/seattle) and [Boston](https://www.kaggle.com/datasets/airbnb/boston) on Kaggle.

The data for both cities includes:
- calendar.csv:
   - listing id and the price and availability for that day
- listings.csv:
  - full descriptions and average review score
- reviews.csv:
  - unique id for each reviewer and detailed comments
 
Looking at the data there are three questions we can dedicate ourselves during this analysis:
1. **Can you describe the vibe of each neighborhood using listing descriptions?**
2. **Is there a significant difference between ratings in Seattle and Boston?**
3. **What are the busiest times of the year to visit both cities? By how much do prices spike?**

To answer these questions a Jupyter Notebook was created using Python with the following libraries:
- pandas
- matplotlib
- spacy
- scipy

I would like to thank [Udacity](https://www.udacity.com) for the great opportunity to learn and improve my skills and [Airbnb](https://www.airbnb.com) for providing the data. Additionally I want to thank [Kaggle](https://www.kaggle.com) for being such a great platform for data science and hosting all data used for this analysis.

## Analysis
This chapter includes a summary of the analysis for each question raised in the introduction.  
First I had to load all data. I choosed to use the module **pandas** to import the data and combine the separated data sets for Boston and Seattle to a single data frame. For the *calendar* and *reviews* data I had to add a column for the city to be able to assign a row to the correct one.  
The data for *listings* already had columns for the city.

In the functions used for importing the data I already set appropriate data types for the columns. However I faced issues loading the data because the price column in the *calendar* could not be loaded as float for example. That is why there were some steps needed to clean the data before the analysis.

### Data Cleaning
#### Calendar
Two columns stand out looking at the data types and the first rows in *calendar*.  
The first one is column *available*. It is a string and only contains the values 'f' and 't'. Thus we can convert this column to boolian by stting the value False for 'f' and True for 't'.

The second column we can transform is *price*. It contains a Dollar sign at the beginning of each amount. Stripping the $ enables us to cast the amount to float.

#### Listings
The *listings* table is the most complex one. To get an overview of the data I printed the head of the data.  
By taking a look it seems that there might be issues with the string columns like before with *calendar*.

So I printed the column names of all string columns. I ended up casting the following columns to another data type:  
'host_since', 'host_response_rate','host_acceptance_rate','host_is_superhost', host_has_profile_pic','host_identity_verified','is_location_exact','price','weekly_price','monthly_price','security_deposit','cleaning_fee','extra_people','has_availability', 'calendar_last_scraped','first_review',last_review','requires_license','instant_bookable','require_guest_profile_picture','require_guest_phone_verification'.

Some of the above columns where transformed to dates, others to boolian or floats after removing string characters if necessary.

After further analysis it turned out that the column 'license' is always missing and the columns 'country_code', 'country', 'has_availability', 'jurisdiction_names', 'experiences_offered' and 'requires_license' only contain one distinct value.  
So those fields were dropped from the data set because they add no value for the ongoing analysis.

*Listings* was the only table that already contained information about the city. However it did not just contain the values 'Boston' and 'Seattle'. So I mapped other values to these two.

#### Reviews
Taking a look at the *reviews* table, it seems we don't need to clean anything.

### 1. Can you describe the vibe of each neighborhood using listing descriptions?
In the *listings* table there are two columns named 'neighborhood' and 'neighborhood_overview'. 'Neighborhood' contains the district of a city while 'neighborhood_overview' offers a short description of the district phrased by the host.  
To make a description you have to use adjectives. For example something is *beautiful* or an environment is *quiet*.

The module **spacy** in Python provides the possibility to extract adjectives from a text. I did that for every 'neighborhood_overview', counted the adjectives for each neighborhood and kept the five most frequent ones. Then I combined them again in a comma separated string.  
The result looks somewhat like follows:

|City|Neighborhood|Neighborhood_Vibe|
|------------|-------------------|------------------------------|
|Boston|Allston-Brighton|safe,quiet,young,easy,convenient|
|Boston|Back Bay|historic,beautiful,public,fashionable,famous|
|Boston|Beacon Hill|historic,antique,beautiful,desirable,public|
|Seattle|Alki|quiet,beautiful,old,open,urban|
|Seattle|Arbor Heights|quick,awesome,beautiful,convenient,free|
|Seattle|Atlantic|quiet,easy,good,urban,asian|

### 2. Is there a significant difference between ratings in Seattle and Boston?
Guests have the possibility to rate a location after they stayed there. The rating can be found in the *listings* table in column 'review_scores_value'. Guests can give a rating from 1 to 10 with 1 being the worst and 10 being the best.

In average ratings are at about 9.17 for Boston and 9.45 for Seattle. So obviously ratings are slightly better for locations in Seattle. The question we try to answer is whether the difference is statistically significant.
Normally a one-sided t-test would be performed. But drawing histograms for the distribution of ratings for both cities indicates very clearly that the ratings are not normally distributed.

Since normal distribution is a prerequisite for a t-test we have to use a nonparametrical test in this case. So I performed a Wilcoxon rank sum test using the module **scipy** with the hypotheses

```math
H_{0}: \mu_{0}\geq\mu_{1} \qquad vs. \qquad H_{1}: \mu_{0} < \mu_{1}
```

The p-value is close to 0. That means we can reject the null hypothesis by significance level 5%. So the ratings of listings in Boston are significally lower than the ratings in Seattle.

### 3. What are the busiest times of the year to visit both cities? By how much do prices spike?
To answer the question for the busiest time of the year in Boston and Seattle we look at the following plot.

![Unavailability](https://github.com/mdraws86/airbnb/blob/main/images/Unavailability.png)

As I am not capable of explaining the strong decrease at the beginning of both curves (probably it was a change in law in the states of Massachusetts and Washington) I will ignore these parts and concentrate on time interval of about March 2016 to January 2017 for Seattle and December 2016 to September 2017 for Boston.  
For both cities we can see that there is an increase in average unavailability rate around March when spring begins which usually means the weather gets better.

For Seattle we observe another increase at about July presumably due to the summer and afterwards a steady decrease in fall and winter as it gets colder.

In Boston the average unavailability rate is pretty constant after the increase during spring until September with an exception around April 15th. Usually Boston Marathon takes place around that date which explains the short peak of unavailabilty.

Generally Boston has higher unavailability rates than Seattle.

Looking at the next plot we can make some statements about the price developement over the year in both cities.

![Prices](https://github.com/mdraws86/airbnb/blob/main/images/Prices.png)

The price level is generally lower in Seattle over the whole year.  
Seattle has a clear seasonal component for the seasons with lower prices in winter and higher prices during summer. There is another seasonal component for the weeks with higher prices during the weekend. Additionally we can see an overall positive trend for prices in Seattle.

For Boston the curve looks quite strange at the beginning just like the one for unavailability rates. But from January 2017 on it looks similar to Seattle just on a higher price level. That means prices are lower on average during winter and higher during summer. Also we can see the same seasonal component for the weeks, with higher prices for the weekends.

Like in the plot for unavailability one can see a clear peak at around April 15th. So not only more guests are looking for an accommodation during that time, they also have to pay significantly higher prices during Boston Marathon.

The difference between highest and lowest price for Boston is almost 110 Dollars while it is only about 40 Dollars for Seattle.