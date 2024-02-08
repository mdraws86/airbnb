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

*Listings* was the only table that already contained oinformation about the city. However it did not just contain the values 'Boston' and 'Seattle'. So I mapped other values to these two.

#### Reviews
Taking a look at the 'reviews' table it seems we don't need to clean anything.

### 1. Can you describe the vibe of each neighborhood using listing descriptions?
