---
layout: project
title: Marketing Budgets Optimization-P1
image: assets/images/Marketing_Optimization/mkt_opt.jpg
description: Part 1 Exploratory Data Analysis
---
<h2 id="Motivation" style="color:black">Motivation</h2>
Companies across various industries are increasingly utilizing analytics to gain deeper insights into their customers. By developing a clear, data-driven understanding of their customer base, these companies can implement personalized strategies that significantly boost conversion rates.

One effective approach is customer clustering, where marketing activities are tailored based on demographic attributes and app behaviors. For instance, in the realm of advertising, businesses aim to maximize ad engagement by targeting specific groups that align with their campaign objectives. Consider a scenario where Philips is launching a Valentine’s Day campaign for their razor products. Instead of broadly targeting all customers, they might focus their efforts on groups like girlfriends seeking Valentine’s gifts or males with a matching spending profile. While traditional methods might involve marketing teams brainstorming potential target groups, machine learning offers a more sophisticated alternative. These methods can uncover underlying patterns both before and after the campaign, providing answers to critical questions such as identifying potential buyer segments and understanding the characteristics of customers who convert.

During a campaign, maximizing the return on investment is crucial, meaning that ads should be shown to users with the highest likelihood of conversion. A simplistic approach might involve displaying ads uniformly across all groups and calculating an average Click-Through Rate (CTR). However, this method fails to adapt and optimize, potentially leading to budget waste by over-investing in suboptimal groups. This report proposes an algorithm that bases advertising decisions on previous CTR distributions, enabling the selection of optimized target groups and the continuous updating of CTR data for each group.

Post-campaign analysis is equally important, as it helps identify the features that drive success, enabling marketers to refine their strategies for future campaigns. Given that ad campaigns often unfold in waves, each wave provides an opportunity to iterate and enhance ad characteristics. By leveraging insights from the first wave, subsequent campaign elements can be adjusted to achieve higher CTRs and overall better performance.

Even though Google Ads and most programmatic platforms have already automated the process with strategy such as PerformanceMax and Customer Lookalike, it was still an interesting learning for me to combine what I learned in Machine Learning classes with the underlying ads bidding mechanism :)


<h2 id="Problem framing" style="color:black">Problem framing</h2>

Four questions need to be analyzed for optimizing companies' marketing budget:

\* What are the potential customer segmentation?

\* How to optimize the advertising budget to achieve higher CTR?

\* What are the features contributing to CTR?

\* How do we adjust advertisement strategy based on these results?

To address these questions, this analysis will begin by employing k-medoids clustering to identify underlying customer patterns. Following this, an exploration algorithm will be developed to construct CTR distributions for each group, enabling the allocation of ads to the group with the highest predicted CTR. XGBoost will then be utilized to analyze the features that contribute to the likelihood of a user clicking an ad. The model will subsequently predict the CTR for a new batch of customers, and those with a predicted CTR greater than 0.2 will be selected as the target audience for the next wave of ads. Finally, SHapley Additive exPlanation (SHAP) will be used to fine-tune ad characteristics, further enhancing the predicted CTR for each customer group.

However, there are certain limitations. Some variables, which might significantly impact the model's performance, are not disclosed by the website. For example, as this is an e-commerce platform, features such as browsing time, the number of items in the cart, and the number of saved items could be crucial for effective customer clustering. Another challenge is the lack of linkage between customer information and ad data, which means customer-specific information is not included in the predictive model. This limitation also restricts the improvement strategy, as suggestions regarding ad characteristics cannot be tailored to specific customer groups. Additionally, since each marketing campaign has unique objectives, the clusters chosen and the key drivers for clicks will vary, introducing potential bias into the model.

<h2 id="Data Overview" style="color:black">Data Overview</h2>

The datasets comes from an E-commerce website. There are two main datasets for this project. The first one `clustering_data` contains demographic information about the whole customer group. The second one `ads_data` , which are later splitted into `Ads_train` and `Ads_test` for prediction, are information about the ads characteristics each targeted customer is shown to.

<h3 id="Variable Description" style="color:black">1. Variable Description</h3>

<h4 id="Clustering data" style="color:black">Clustering data</h4>

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


<h4 id="Ads data" style="color:black">Ads data</h4>

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
The distribution plot reveals that most customers fall into the middle-aged and older demographics. When analyzing spending across different food categories, Gold Products show a higher density within each spending range. Spending behavior across various categories likely varies based on factors such as age, family size, education level, and could serve as effective clustering variables. Additionally, the number of purchases made in-store versus online displays distinct patterns, suggesting varying consumption habits.

The correlation plot provides further insights. It shows that individuals with higher incomes tend to visit the e-commerce website less frequently. In contrast, larger families spend less on meat products but visit the website more often. Interestingly, spending on fish is positively correlated with spending on sweet products.
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

# Distribution of categorical variables
g1 <-ggplot(clustering_data, aes(x=reorder(Education, Education, function(x)-length(x)))) +
    geom_bar(fill='grey', width = 0.5) +  labs(x='Education')+
    theme(panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())

g2 <- ggplot(clustering_data, aes(x=reorder(Marital_Status, Marital_Status, function(x)-length(x)))) +
geom_bar(fill='grey', width = 0.5) +  labs(x='Marital_Status')+
    theme(panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())

g3 <- ggplot(clustering_data, aes(x=reorder(Teenhome, Teenhome, function(x)-length(x)))) +
geom_bar(fill='grey', width = 0.5) +  labs(x='Teenhome')+
    theme(panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())

g4 <- ggplot(clustering_data, aes(x=reorder(Kidhome, Kidhome, function(x)-length(x)))) +
geom_bar(fill='grey', width = 0.5) +  labs(x='Kidhome')+
    theme(panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())


g_bar <- ggarrange(g1, g2, g3, g4,
          ncol = 2, nrow = 2)

annotate_figure(g_bar, top = text_grob("Bar graph of categorical customer data", 
                size = 14,hjust = 0.5))
         
# Correlation plot
##Data cleaning and feature engineering
corr_data <- clustering_data %>%
  mutate(family_size = Teenhome + Kidhome) %>%
  mutate(Education = case_when(
    Education == 'PhD'~5,
    Education == 'Master'~4,
    Education == '2n Cycle'~4,
    Education == 'Basic' ~3,
    Education == 'Graduation'~2
  )) %>%
  mutate(Dt_Customer = dmy(Dt_Customer))%>%
  mutate(tenure = as.numeric(Sys.Date() - Dt_Customer))%>%
  mutate(age = year(Sys.Date()) - Year_Birth)%>%
  select(age, Education, Income, Recency, starts_with("Mnt"),starts_with("Num"), tenure, family_size)
  
corr1 <- corrplot(cor(corr_data),       
         method = "color", 
         type = "full",   
         diag = FALSE, 
         tl.col = "black",
         tl.cex = 0.7,
         title = "Correlation plot of customer variables",
         bg = "white",    
         mar=c(0,0,1,0),
         col = NULL)  
```
</details>
<br/>
{::options parse_block_html="false" /}

<img src="/assets/images/Marketing_Optimization/customer_demo.png" alt = "Distribution of continuous customer variables" width="600"/>
<img src="/assets/images/Marketing_Optimization/customer_demo_bar.png" alt = "customer_demo_bar" width="600"/>
<img src="/assets/images/Marketing_Optimization/customer_demo_corr.png" alt = "customer_demo_corr" width="600"/>

<h3 id="Email characteristics" style="color:black">3. Email characteristics</h3>
Advertisements that customers previously received and word count of the advertisement are mostly normally distributed. A majority of campaign type is Campaign type 2, while email source is more evenly distributed. Total links contained in the ad concentrate around 10. When distribution of these variables are separated by click or not click, The number of past communications and campaign type seem to be good classifiers.

{::options parse_block_html="true" /}
<details>

```r
# Distribution
Ads_data %>%
  keep(is.numeric) %>%
  gather() %>% 
  ggplot(aes(value)) +
    facet_wrap(~ key, scales = "free") +
    ggtitle('Distribution of Ads variables')+
    theme(plot.title = element_text(hjust = 0.5))+
    theme(panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank())+  
    geom_histogram(alpha = 0.3, fill="blue")

# Potential classifiers
## Word count
g_1 <- ggplot(Ads_data, aes(x = Word_Count, fill = Email_Status)) + # Set x and y aesthetics
    geom_density(alpha = 0.3,show.legend = FALSE, color=NA) + # Set geom density for density plot
    theme_bw() + # Set theme bw
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    labs(x = "Word_Count",  # Set plot labels
    fill = "Email_Status")+
    #title = "Word_Count v Email_Status") +
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

## Total past communications
g_2 <- ggplot(Ads_data, aes(x = Total_Past_Communications, fill = Email_Status)) + # Set x and y aesthetics
    geom_density(alpha = 0.3,show.legend = FALSE, color=NA) + # Set geom density for density plot
    theme_bw() + # Set theme bw
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    labs(x = "Total_Past_Communications",  # Set plot labels
    fill = "Email_Status")+
    #title = "Total_Past_Communications v Email_Status") +
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

## Total Links
g_3 <- ggplot(Ads_data, aes(x = Total_Links, fill = Email_Status)) + # Set x and y aesthetics
    geom_density(alpha = 0.3,show.legend = FALSE, color=NA) + # Set geom density for density plot
    theme_bw() + # Set theme bw
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    labs(x = "Total_Links",  # Set plot labels
    fill = "Email_Status")+
    #title = "Total_Links v Email_Status") +
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

## Total images
g_4 <- ggplot(Ads_data, aes(x = Total_Images, fill = Email_Status)) + # Set x and y aesthetics
    geom_density(alpha = 0.3,show.legend = FALSE, color=NA) + # Set geom density for density plot
    theme_bw() + # Set theme bw
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    labs(x = "Total_Images",  # Set plot labels
    fill = "Email_Status")+
    #title = "Total_Images v Email_Status") +
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

## Topic hotness
g_5 <- ggplot(Ads_data, aes(x = Subject_Hotness_Score, fill = Email_Status)) + # Set x and y aesthetics
    geom_density(alpha = 0.3,show.legend = FALSE, color=NA) + # Set geom density for density plot
    theme_bw() + # Set theme bw
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    labs(x = "Subject_Hotness_Score",  # Set plot labels
    fill = "Email_Status")+
    #title = "Subject_Hotness_Score v Email_Status") +
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

# 3.2.2 Bar for categorical variables
g_6 <- Ads_data %>%
  group_by(Email_Type, Email_Status) %>%
  count()%>%
  ggplot(., aes(fill=Email_Status, y=n, x=Email_Type)) + 
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    geom_bar(position="dodge", stat="identity", alpha = 0.3,show.legend = FALSE)+
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

g_7 <- Ads_data %>%
  group_by(Email_Campaign_Type, Email_Status) %>%
  count()%>%
  ggplot(., aes(fill=Email_Status, y=n, x=Email_Campaign_Type)) +
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    geom_bar(position="dodge", stat="identity", alpha = 0.3,show.legend = FALSE)+
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

g_8 <- Ads_data %>%
  group_by(Customer_Location, Email_Status) %>%
  count()%>%
  ggplot(., aes(fill=Email_Status, y=n, x=Customer_Location)) + 
    theme(text = element_text(size = 9))+
    theme(panel.grid.major = element_blank(), # Turn of the background grid
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank()) +
    geom_bar(position="dodge", stat="identity", alpha = 0.3)+
    scale_fill_manual(values = c("0" = "red", "1" = "blue"), # Manually set fill values
    labels = c("0" = "Didn't click", "1" = "Clicked"))

g_pred <- ggarrange(g_1, g_2, g_3, g_4, g_5, g_6, g_7, g_8,
          ncol = 3, nrow = 3)

annotate_figure(g_pred, top = text_grob("Potential classifiers", 
                size = 14,hjust = 0.5))
```
</details>
<br/>
{::options parse_block_html="false" /}
  
<img src="/assets/images/Marketing_Optimization/mkt_distribution.png" alt = "mkt_distribution" width="600"/>
<img src="/assets/images/Marketing_Optimization/potential_classifier.png" alt = "potential_classifier" width="600"/>
