---
layout: project
title: Marketing Budgets Optimization-P2
image: assets/images/Marketing_Optimization/mkt_opt_pt2.jpg
description: Part 2 Customer segmentation and Thompson sampling
---
<h2 id="Customer Segmentation" style="color:black">Customer Segmentation</h2>

Using k-medoids clustering with 6 clusters, we identified customer groups that are similar in nature but exhibit slight differences in their attributes. Group 1, for example, is characterized by high tenure and large family size. They make frequent purchases and visit the website regularly, indicating that they are likely loyal customers.

Two other groups, Group 3 and Group 6, also stand out. Group 3 consists of customers with low tenure who visit the website frequently but have low purchase rates and spending levels. Group 6, on the other hand, includes new users who have not engaged with the website recently. Given these characteristics, Groups 3 and 6 are excluded from the target audience for the advertisement campaign, as their engagement and spending behaviors do not align with our campaign objectives.

{::options parse_block_html="true" /}
<details>
  
```r
pam1 <- pam(scaled_clust, 6, metric = "euclidean", stand = FALSE) #Using dataset with categorical variables
pam.res <- pam(scaled_clust, 6)
clust_vec <- as.data.frame(pam.res[3])

# Generating heatmap 
heat_df <- as.data.frame(pam1$medoids)
clust_num <- seq(1,6,1)
heat_df <- cbind.data.frame(clust_num,heat_df)
heat_df <- heat_df[,-2]

# Scaled numeric numbers
center_reshape1 <- gather(heat_df, features, values, age:NumWebVisitsMonth)

g_heat_2 <- ggplot(data = center_reshape1, # Set dataset
                   aes(x = features, y = clust_num, fill = values)) + # Set aesthetics
  scale_y_continuous(breaks = seq(1, 6, by = 1)) + # Set y axis breaks
  geom_tile() + # Geom tile for heatmap
  coord_equal() +  # Make scale the same for both axis
  theme_set(theme_bw(base_size = 22) ) + # Set theme
  theme(text = element_text(size = 10)) +
  scale_fill_gradient2(low = "blue", # Choose low color
                       mid = "white", # Choose mid color
                       high = "red", # Choose high color
                       midpoint =0, # Choose mid point
                       space = "Lab", 
                       na.value ="grey", # Choose NA value
                       guide = "colourbar", # Set color bar
                       aesthetics = "fill") + # Select aesthetics to apply
  coord_flip() # Rotate plot to view names clearly

center_reshape2 <- gather(heat_df, features, values, Education:Marital_Status)
g_heat_3 <- ggplot(data = center_reshape2, # Set dataset
                   aes(x = features, y = clust_num, fill = values)) + # Set aesthetics
  scale_y_continuous(breaks = seq(1, 6, by = 1)) + # Set y axis breaks
  geom_tile() + # Geom tile for heatmap
  coord_equal() +  # Make scale the same for both axis
  theme_set(theme_bw(base_size = 22) ) + # Set theme
  theme(text = element_text(size = 10)) +
  scale_fill_gradient2(low = "blue", # Choose low color
                       mid = "white", # Choose mid color
                       high = "grey", # Choose high color
                       midpoint =0, # Choose mid point
                       space = "Lab", 
                       na.value ="grey", # Choose NA value
                       guide = "colourbar", # Set color bar
                       aesthetics = "fill") + # Select aesthetics to apply
  coord_flip() # Rotate plot to view names clearly
```
</details>
<br/>
{::options parse_block_html="false" /}
  
<img src="/assets/images/Marketing_Optimization/cluster.png" alt = "cluster" width="600"/>
<img src="/assets/images/Marketing_Optimization/cluster_1.png" alt = "cluster_1" width="600"/>
  
<h2 id="Optimization strategy" style="color:black">Optimization strategy</h2>
During the advertisement delivery process, the Thompson sampling algorithm is used to optimize the selection of customer groups. Initially, the algorithm selects 10 customers from each group and records their click status. It then constructs a CTR information set for each customer group, modeled as a beta distribution with parameters based on the number of customers who clicked and those who did not.

When deciding which customer group to target next, the algorithm draws a sample CTR from each group's distribution and selects the group with the highest sampled CTR. After delivering the ads, the CTR information set is updated accordingly. This approach ensures that customer groups with higher CTRs are selected more frequently, leading to narrower confidence intervals for those groups.

From the distribution chart, we observe that Group 1, with a CTR of 0.16, was chosen 742 times out of 1,000 iterations. Although Group 3, with a CTR of 0.03, was also selected, it occurred much less frequently compared to the other groups.
  
{::options parse_block_html="true" /}

<details>
```r
set.seed(2022)
dataset <- read.csv("/Users/liangjingjing/Desktop/Machine learning/Project/Ads_CTR_Optimisation.csv")

n_groups = 4 #Number of customer groups
grp_selected = integer(0) #Document which group is selected for each delivery

#Deliver 10 ads to each customer group to begin with  
numbers_of_rewards_1 = integer(n_groups) 
numbers_of_rewards_0 = integer(n_groups)

for (i in 1:n_groups){
  for (j in 1:4){
    ctr = sample_n(dataset[i],1)
    if (ctr == 1){
      numbers_of_rewards_1[i] = numbers_of_rewards_1[i]+1
    }
    else {numbers_of_rewards_0[i] = numbers_of_rewards_0[i]+1}
  }
}

#Deliver n ads based on previous CTR distribution 
for (n in 1:1000) {
  cus_grp = 0
  max_random = 0
  for (i in 1:n_groups) {
    random_beta = rbeta(n = 1,
                        shape1 = numbers_of_rewards_1[i] + 1,
                        shape2 = numbers_of_rewards_0[i] + 1)
    if (random_beta > max_random) {
      max_random = random_beta
      cus_grp = i
    }
  }
  grp_selected = append(grp_selected, cus_grp)
  reward = dataset[n, cus_grp]
  if (reward == 1) {
    numbers_of_rewards_1[cus_grp] = numbers_of_rewards_1[cus_grp] + 1
  } else {
    numbers_of_rewards_0[cus_grp] = numbers_of_rewards_0[cus_grp] + 1
  }
  df_100 <- data.frame(a=numbers_of_rewards_1, b=numbers_of_rewards_0)
}
  
#Plot CTR distribution for each customer groups
p = seq(0,1, length=100)

plot(p, dbeta(p, df_100[1,1], df_100[1,2]), ylab='density', ylim=range(0:35), type ='l', col='purple')
lines(p, dbeta(p, df_100[2,1], df_100[2,2]), col='red', alpha = 0.3) 
lines(p, dbeta(p, df_100[3,1], df_100[3,2]), col='blue')
lines(p, dbeta(p, df_100[4,1], df_100[4,2]), col='green')

legend(.3, 35, c(
               paste0('Group1: ','beta(',df_100[1,1],',', df_100[1,2],') CTR: ', round(df_100[1,1]/(df_100[1,1]+df_100[1,2]),2))
              ,paste0('Group2: ','beta(',df_100[2,1],',', df_100[2,2],') CTR: ', round(df_100[2,1]/(df_100[2,1]+df_100[2,2]),2))
              ,paste0('Group3: ','beta(',df_100[3,1],',', df_100[3,2],') CTR: ', round(df_100[3,1]/(df_100[3,1]+df_100[3,2]),2))
              ,paste0('Group4: ','beta(',df_100[4,1],',', df_100[4,2],') CTR: ', round(df_100[4,1]/(df_100[4,1]+df_100[4,2]),2))
              )
       ,lty=c(1,1,1,1)
       ,col=c('purple', 'red', 'blue','green'))
```
</details>
<br/>
{::options parse_block_html="false" /}
  
<img src="/assets/images/Marketing_Optimization/CTR_distribution.png" alt = "CTR_distribution" width="600"/>
<img src="/assets/images/Marketing_Optimization/group_selected.png" alt = "group_selected" width="600"/>  

