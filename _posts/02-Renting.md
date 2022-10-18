---
layout: project
title: Renting Rate Analysis
image: assets/images/02-Renting-banner.jpg
description: Text data cleaning and machine learning using Python
---
<h2 id="Overview" style="color:black">Overview</h2>

Can text mining apartment descriptions give insight to what renters are caring about their renting choice?

In the real-estate industry, listing page of the apartment is what customers first see. How good it looks digitally and how attractive it reads has been a prominent role in a customer's decision. Real-estate businesses are looking to listing page elements to understand their customers' desires and experiences to identify their areas of opportunities.

The dataset contains 50k inquiry of a real-estate agency. Each listing has information on the number of bathrooms and bedrooms, apartment description, amenities and photo URL. It has the inquirer's information such as gender, number of times they have followed up, and their expected price. And finally we can see if the deal has been made.

<h2 id="Outline" style="color:black">Outline</h2>

-   Data cleaning
-   NLP on apartment description: Can positive description boost the chance of a deal?
-   Feature engineering on amenity tags: Is any specific amenity a deal-breaker?
-   Quantify number of photos: Can more number of photos boost the chance of a deal?
-   Machine learning: How do the above variables affect renting behavior?

<h2 id="Tools" style="color:black">Tools</h2>

-   Python
-   NLTK
-   sklearn
-   Pandas
-   numpy
-   Matplotlib

<h2 id="Summary" style="color:black">Summary</h2>
How many photos the listing has and how positive the description sounds has high importance to making a deal. The marketing team should check the listings without photos or have low positivity score, and using these criteria to promote them more efficiently. 
The number of bedrooms and bathrooms, and hardwood floors are top amenities that affect customer decision. They should be placed at the front of the tags. 
 
<h2 id="Technical Process" style="color:black">Technical Process</h2>
Raw dataset:
<div>
<table class="project">
<thead>
<tr class="header">
<th><p></p></th>
<th><p>bathrooms</p></th>
<th><p>bedrooms</p></th>
<th><p>created</p></th>
<th><p>description</p></th>
<th><p>display_address</p></th>
<th><p>features</p></th>
<th><p>latitude</p></th>
<th><p>listing_id</p></th>
<th><p>longitude</p></th>
<th><p>photos</p></th>
<th><p>price</p></th>
<th><p>street_address</p></th>
<th><p>is_deal</p></th>
<th><p>gender</p></th>
<th><p>expected_price</p></th>
<th><p>followup</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>0</p></td>
<td><p>1.5</p></td>
<td><p>3</p></td>
<td><p>24/06/2016 7:54</p></td>
<td><p>A Brand New 3 Bedroom 1.5 bath ApartmentEnjoy ...</p></td>
<td><p>Metropolitan Avenue</p></td>
<td><p>[]</p></td>
<td><p>40.7145</p></td>
<td><p>7211212</p></td>
<td><p>-73.9425</p></td>
<td><p>[https://photos.renthop.com/2/7211212_1ed4542...</p></td>
<td><p>3000.0</p></td>
<td><p>792 Metropolitan Avenue</p></td>
<td><p>1</p></td>
<td><p>female</p></td>
<td><p>2700.0</p></td>
<td><p>13.0</p></td>
</tr>
<tr class="even">
<td><p>1</p></td>
<td><p>1</p></td>
<td><p>2</p></td>
<td><p>12/06/2016 12:19</p></td>
<td></td>
<td><p>Columbus Avenue</p></td>
<td><p>[Doorman, Elevator, Fitness Center, Cat...</p></td>
<td><p>40.7947</p></td>
<td><p>7150865</p></td>
<td><p>-73.9667</p></td>
<td><p>[https://photos.renthop.com/2/7150865_be3306c...</p></td>
<td><p>5465.0</p></td>
<td><p>808 Columbus Avenue</p></td>
<td><p>0</p></td>
<td><p>male</p></td>
<td><p>5200.0</p></td>
<td><p>NaN</p></td>
</tr>
<tr class="odd">
<td><p>2</p></td>
<td><p>1</p></td>
<td><p>1</p></td>
<td><p>17/04/2016 3:26</p></td>
<td><p>Top Top West Village location, beautiful Pre-w...</p></td>
<td><p>W 13 Street</p></td>
<td><p>[Laundry In Building, Dishwasher, Hardwoo...</p></td>
<td><p>40.7388</p></td>
<td><p>6887163</p></td>
<td><p>-74.0018</p></td>
<td><p>[https://photos.renthop.com/2/6887163_de85c42...</p></td>
<td><p>2850.0</p></td>
<td><p>241 W 13 Street</p></td>
<td><p>1</p></td>
<td><p>male</p></td>
<td><p>2900.0</p></td>
<td><p>NaN</p></td>
</tr>
<tr class="even">
<td><p>3</p></td>
<td><p>1</p></td>
<td><p>1</p></td>
<td><p>18/04/2016 2:22</p></td>
<td><p>Building Amenities - Garage - Garden - fitness...</p></td>
<td><p>East 49th Street</p></td>
<td><p>[Hardwood Floors, No Fee]</p></td>
<td><p>40.7539</p></td>
<td><p>6888711</p></td>
<td><p>-73.9677</p></td>
<td><p>[https://photos.renthop.com/2/6888711_6e660ce...</p></td>
<td><p>3275.0</p></td>
<td><p>333 East 49th Street</p></td>
<td><p>0</p></td>
<td><p>female</p></td>
<td><p>3500.0</p></td>
<td><p>NaN</p></td>
</tr>
<tr class="odd">
<td><p>4</p></td>
<td><p>1</p></td>
<td><p>4</p></td>
<td><p>28/04/2016 1:32</p></td>
<td><p>Beautifully renovated 3 bedroom flex 4 bedroom...</p></td>
<td><p>West 143rd Street</p></td>
<td><p>[Pre-War]</p></td>
<td><p>40.8241</p></td>
<td><p>6934781</p></td>
<td><p>-73.9493</p></td>
<td><p>[https://photos.renthop.com/2/6934781_1fa4b41...</p></td>
<td><p>3350.0</p></td>
<td><p>500 West 143rd Street</p></td>
<td><p>0</p></td>
<td><p>female</p></td>
<td><p>3200.0</p></td>
<td><p>0.0</p></td>
</tr>
</tbody>
</table>

</div>
<h3 id="Data cleaning" style="color:black">Data cleaning</h3>
Because machine learning models don't work well with NaN, they are dropped from the dataset. Outliers (5% percentile) are trimmed as well.

<h3 id="Feature engineering" style="color:black">Feature engineering</h3>
The purpose of feature engineering is to extract the amenities such as "Elevator", "Doorman", and "Fitness center" in the features column, and turn them into binary variables to investigate their relationships with settling a deal.I first unravelled the list and used textblob to seperate them into phrases.The top ten frequent features are selected and turned into binary variable.

<h3 id="NLP analysis" style="color:black">NLP analysis</h3>
The apartment description are seperated into sentences, phrases and the words. NLTK is used to calculate the polarity and subjectivity of each description with the average of each word. 

<h3 id="Number of photos" style="color:black">Number of photos</h3>
Because each photo URL is stored in a nested list in the dataset, I calculated the lenth of each listing's photo URL and replaced null with 0.

<h3 id="Exploratory data analysis" style="color:black">Exploratory data analysis</h3>
With the above variables, I investigated the distribution of each variable and their correlation to each other.
The variables are not obviously skewed, which leaves me with enough sample data. Some amenities are high correlated with each other, such as Doorman to Wood floor and Doorman to Fitness center. I furthur look into the factors effecting deal using machine learning.
![Variable distribution](/assets/images/Renting-rate-analysis/output_80_0.png)
![Correlation matrix](/assets/images/Renting-rate-analysis/output_82_0.png)

<h3 id="Machine learning" style="color:black">Machine learning</h3>
Four models are implemented in this session: Logistic regression, Decision tree, Random forest and xgboost. All results suggest the number of photos, polarity of apartment description and number of bedrooms have high importance to settling a deal. By comparing the accuracy, precision and recall scores, the result from xgboost is the most ideal one. 

![Xgboost result](/assets/images/Renting-rate-analysis/output_109_0.png)

<h3 id="See full codes in depository" style="color:black">See full codes in depository</h3>
[Renting rate analysis](https://github.com/JingjingLiang99/Renting-rate-analysis)
