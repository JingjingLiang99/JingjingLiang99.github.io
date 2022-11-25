---
layout: project
title: Advertising Budgets Optimization 
image: assets/images/ads_opt.png
description: Part 1 Exploratory Data Analysis
---
<h2 id="Motivation" style="color:black">Motivation</h2>
Companies across industries are using analytics to understand their customers better. By having a clear image of who their customers are, companies can implement personalized strategies and increase conversion rates.

One way to do this is by clustering the customers and customize the marketing activities based on their demographic features and app behaviors. Take one aspect of marketing, advertising as an example, companies would want to increase the probability of their ads being clicked by choosing the groups that are pertaining to their campaign objective. For instance, if Phillips are launching a Valentine’s campaign for their razor products, from all the customer groups, they might be interested in delivering advertisement to groups such as girlfriends who are looking for a valentine gift, or males with matching spending power. Obviously marketing teams can simply brainstorm the targeting groups, but using machine learning methods can reveal underlying patterns before and after the campaign, answering questions such as who their potential buying groups are and what the features are from the converted customers.

During the campaign, companies would want to make every penny count. That means they prefer to deliver ads to user that has higher chance of converting. We could, intuitively, display ads n-times to all groups, and get n-average Click-Through-Rate. However, this method is continuously exploiting an information set without updating it, and are potentially wasting budget on allocating it to sub-optimizing groups. Therefore, this paper suggested an algorithm that makes advertising decision based on the previous CTR distributions, chooses the optimized group, and keeps updating the CTR information set for each group.

After the campaign, it is also important to investigate what features are driving success so that we can tune our marketing campaign for enhanced performance in the future. Advertisement campaign usually consists of waves, and we can iterate the ads characteristics for every wave. Using the first-wave information, we can adjust our subsequent features aiming for higher CTR.

<h2 id="Problem framing" style="color:black">Problem framing</h2>

Four questions need to be analyzed for optimizing companies' marketing budget:

\* What are the potential customer segmentation?

\* How to optimize the advertising budget to achieve higher CTR?

\* What are the features contributing to CTR?

\* How do we adjust advertisement strategy based on these results?

To answer these questions, this paper will first use k-medoids clustering to detect underlying patterns. Then it will build a exploring algorithm that builds CTR distribution for each group, and allocate ads to group with highest drawn CTR. XGboost will be used to investigate the features that are contributing to the probability of clicking an ad. The model can then predict the CTR of another batch of customers and choose those who has \>.2 CTR as our targeted customers for next ads wave. Finally, I will use SHApley Additive exPlanation to further adjust ads characteristics to enhance predicted CTR for each customer groups. There are some variables that are not disclosed by the website, and might have significant importance to the model's ability to learn. For instance, as it is an e-commerce website, the browsing time, number of items in cart, number of items saved, etc could be important features for customer clustering. Another problem is that customer information and ads information are not linked together, so customer information is not included in the predictive model. This fact also restricted the improvement strategy because suggestions about ads characteristics could not be specific to customer groups. Additionally, as each marketing campaign has different objectives, the clusters chosen and driving factors for click will be different, which means the model would suffer from potential bias. 

<h2 id="Data Overview" style="color:black">Data Overview</h2>

The datasets comes from an E-commerce website. There are two main datasets for this project. The first one `clustering_data` contains demographic information about the whole customer group. The second one `ads_data` , which are later splitted into `Ads_train` and `Ads_test` for prediction, are information about the ads characteristics each targeted customer is shown to.

<h3 id="Variable Description" style="color:black">1. Variable Description</h3>

**clustering_data**

`ID`:Customer unique identifier

`Year_Birth`: Customer's birth year

`Education`: Education Qualification of customer

`Marital_Status`: Marital Status of customer

`Income`:Customer's yearly household income

`Kidhome`: Number of children in customer's household

`Teenhome`: Number of children in customer's household

`Dt_Customer`: Date of customer's enrollment with the company

`Recency`: Number of days since customer's last purchase

`Mntxxx`: Amount spent on wine/fruits/meat/fish/sweet/gold products

`NumCatalogPurchases`: Number of category purchased

`NumDealsPurchases`: Purchases made in history

`NumWebPurchases`: Purchases made on website

`NumStorePurchases`:Purchases made in store

`NumWebVisitsMonth`: Times a customer has visited the website per month


**ads_data**

`Email_Type`: 1 indicates Newsletter and 2 indicates promotion information

`Subject_Hotness_Score`: The score of how on trend the ads topic is

`Email_Source_Type`: 1 indicates subscribed users, 2 indicates unsubcribed users.

`Customer_Location`: Location names of the customer

`Email_Campaign_Type`: Emails promoting different discounts.

`Total_Past_Communications`: Number of emails a customer received last month

`Time_Email_sent_Category`: 1 indicates the email was sent in the morning; 2 indicates in the afternoon and 3 indicates in the evening.

`Word_Count`: Word count of the email.

`Total_Links`: Total links contained in the email.

<h3 id="User Demographics" style="color:black">2. User Demographics</h3>
From the distribution plot, most customers are middle-aged and above. In terms of money spent on different categories of food, Gold Products has higher density in each consumption bin. Spend on different categories might be different for different ages, family size, education level, etc., and serve as a good clustering variable. Number of purchases by store and web also show different patterns, and might suggest different consuming habits.

According to the correlation plot, individuals with higher income visits the e-commerce website fewer times. And family with bigger size spend less on meat products, but they visit the websites more times. Spend on fish is positively correlated with that on sweet products.
{::options parse_block_html="true" /}
<details>

```r
# Distribution of all continuous variables
clustering_data %>%
  select(-ID, -Education,-Marital_Status,-Kidhome,-Teenhome) %>%
  keep(is.numeric) %>%
  gather() %>% 
  ggplot(aes(value)) +
    facet_wrap(~ key, scales = "free") +
    geom_density(alpha = 0.3, fill = "blue", color = NA)+
    ggtitle('Distribution of continuous customer variables')+
    theme(plot.title = element_text(hjust = 0.5))+
    theme(panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())
```
</details>
<br/>
{::options parse_block_html="false" /}
