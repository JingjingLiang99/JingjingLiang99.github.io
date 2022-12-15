---
layout: project
title: Marketing Budgets Optimization 
image: assets/images/Marketing_Optimization/mkt_opt_pt2.jpg
description: Part 3 CTR analysis using XGboost and Neural networks
---
<h2 id="Predictive analysis on email features" style="color:black">Predictive analysis on email features</h2>

The categorical variables are first transformed into dummy variables and stored into XGboost matrices. Total data is split into train and test datasets at the ratio of 8:2. The ads dataset with E-mail features contains 13,412 positive answers and 54,941 negative answers. To deal with the imbalanced dataset, I adjusted the cut-off threshold to 0.2.

<h3 id="1. XGboost on E-mail attributes" style="color:black">1. XGboost on E-mail attributes</h3>
I first analyzed the feature importance of email attributes to click rate by building a XGboost model with 100 rounds. Email campaign type as 1 has high importance. In reality context, it means email campaign with discount coupon can drive customers to click more. The XGboost model achieves a sensitivity score of 0.5923 and specificity score of 0.7116.

{::options parse_block_html="true" /}
<details>
  
```r
# XG boost - Email attributes
## Preparing training & testing datasets
### Checking number of responses - Imbalanced dataset
summary(as.factor(Ads_data$Email_Status))
zero_weight <- 54941/13412

### Splitting
ads_split <- stratified(Ads_data, group = 'Email_Status', size = 0.2, bothSets = TRUE)
ads_train <- ads_split[[2]]
ads_test <- ads_split[[1]]

## Normalizing data
xg_data_train <- as.data.frame(model.matrix(~., data = ads_train[,-1])[,-1])
xg_data_test <- as.data.frame(model.matrix(~., data = ads_test[,-1])[,-1])


dtrain <- xgb.DMatrix(data = as.matrix(xg_data_train[,1:11]), label = as.numeric(xg_data_train$Email_Status1))
dtest <- xgb.DMatrix(data = as.matrix(xg_data_test[,1:11]),label = as.numeric(xg_data_test$Email_Status1))

##Base model
set.seed(111111)
bst_1 <- xgboost(data = dtrain, # Set training data
               nrounds = 100, # Set number of rounds
               verbose = 1, # 1 - Prints out fit
               print_every_n = 300, # Prints out result every 20th iteration
               #scale_pos_weight = zero_weight, ## Experimenting different weights
               objective = "binary:logistic", # Set objective
               eval_metric = "auc",
               eval_metric = "error") # Set evaluation metric to use

## Generate confusion matrix for test dataset
boost_preds_1 <- predict(bst_1, dtest) # Create predictions for test dataset

### Convert predictions to ads status, using optimal cut-off
boost_pred_status <- rep(0, length(boost_preds_1))
boost_pred_status[boost_preds_1 >= 0.2] <- 1
#summary(boost_pred_status)
                                           
t <- table(boost_pred_status, xg_data_test$Email_Status) # Create table
confusionMatrix(t, positive = "1") 

### Extract feature importance
imp_mat <- xgb.importance(model = bst_1)
xgb.plot.importance(imp_mat, top_n = 10)
```
</details>
<br/>
{::options parse_block_html="false" /}

<img src="/assets/images/Marketing_Optimization/Email_feature.png" alt = "Email_feature.png" width="600"/>


<h3 id="2. XGboost on Customer demographics" style="color:black">2. XGboost on Customer demographics</h3>
Another XGboost model with customer demographic variables is built to analyze the customer features importance. The feature importance results suggest tenure, recency, and spent on wines and meat has high influence of customers willingness to click. The XGboost models achieves a sensitivity of 0.49 and an overall accuracy of 0.86.

{::options parse_block_html="true" /}
<details>
  
```r
## XGboost - Customer demographics

### Preparing data
clustering_data <- cbind.data.frame(clust_vec,clustering_data)

xg_cd <- clustering_data %>%
  mutate(family_size = Teenhome + Kidhome) %>%
  mutate(Dt_Customer = dmy(Dt_Customer))%>%
  mutate(tenure = as.numeric(Sys.Date() - Dt_Customer))%>%
  mutate(age = year(Sys.Date()) - Year_Birth)%>%
  select(ID, age, Education, Marital_Status, Income, family_size, tenure, Recency,   starts_with("Mnt"),starts_with("Num"),Response, clustering)%>%
  mutate(Education = as.factor(Education))%>%
  mutate(Marital_Status = as.factor(Marital_Status))%>%
  mutate(Response = as.factor(Response))%>%
  na.omit()  

### Splitting
ads_split_cd <- stratified(xg_cd, group = 'Response', size = 0.2, bothSets = TRUE)
cd_train <- ads_split_cd[[2]]
cd_test <- ads_split_cd[[1]]

## Normalizing data
cd_data_train <- as.data.frame(model.matrix(~., data = cd_train[,c(-1,-21)]))[,-1]
colnames(cd_data_train)[28] <- 'Response'

cd_data_test <- as.data.frame(model.matrix(~., data = cd_test[,c(-1,-21)]))[,-1]
colnames(cd_data_test)[28] <- 'Response'

cd_dtrain <- xgb.DMatrix(data = as.matrix(cd_data_train[,1:27]), label = as.numeric(cd_data_train$Response))
cd_dtest <- xgb.DMatrix(data = as.matrix(cd_data_test[,1:27]),label = as.numeric(cd_data_test$Response))

## Run XGboost model and generate confusion matrix
set.seed(111111)
bst_cd <- xgboost(data = cd_dtrain, # Set training data
               nrounds = 1000, # Set number of rounds
               verbose = 1, # 1 - Prints out fit
               print_every_n = 300, # Prints out result every 20th iteration
               objective = "binary:logistic", # Set objective
               eval_metric = "auc",
               eval_metric = "error") # Set evaluation metric to use

## Create predictions for xgboost model
cd_preds <- predict(bst_cd, cd_dtest)

pred_dat <- cbind.data.frame(cd_preds , cd_data_test$Response)

cd_data_test$Response <- as.factor(cd_data_test$Response)
cd_acc <- confusionMatrix(factor(ifelse(cd_preds>0.2, 1, 0)), cd_data_test$Response, positive="1")
print(cd_acc) 

### Extract feature importance
imp_mat1 <- xgb.importance(model = bst_cd)
xgb.plot.importance(imp_mat1, top_n = 10)
```
</details>
<br/>
{::options parse_block_html="false" /}

<img src="/assets/images/Marketing_Optimization/Customer_feature.png" alt = "Customer_feature.png" width="600"/>

<h3 id="3. Neural network approach" style="color:black">3. Neural network approach</h3>
As CTR prediction can be approached by machine learning methods or deep learning methods (Yang & Zhai, 2022), I developed another neural network model with 1 layer and 6 units. The model performance improved compare to XGboost models with a sensitivity score of 0.70. Sensitivity is the more important indicator in this case since we are dealing with an imbalanced dataset with few positive responses.

{::options parse_block_html="true" /}
<details>
  
```r
## Neural network approach

### Scale dataset
x_mean <- apply(xg_data_train[,1:16],2,mean)
x_sd <- apply(xg_data_train[,1:16],2,sd)
ads_train_nn <- xg_data_train[,1:16]
ads_train_nn <- scale(ads_train_nn, center = x_mean, scale = x_sd)
ads_train_nn <- cbind.data.frame(ads_train_nn,xg_data_train$Email_Status1)
colnames(ads_train_nn)[17] <- 'Email_Status'

ads_test_nn <- scale(xg_data_test[,1:16], center = x_mean, scale = x_sd)

nn1 <- neuralnet(Email_Status==1~., hidden = c(6), data = ads_train_nn, linear.output=F, stepmax = 1e6)
plot(nn1)

nn1_pred <- predict(nn1, newdata = ads_test_nn, type = 'response')[,1]

pred_dat <- cbind.data.frame(nn1_pred , xg_data_test$Email_Status)

names(pred_dat) <- c("predictions", "response")

#oc<- optimal.cutpoints(X = "predictions",
#                       status = "response",
#                       tag.healthy = 0,
#                       data = pred_dat,
#                       methods = "MaxEfficiency")

xg_data_test$Email_Status1 <- as.factor(xg_data_test$Email_Status1)
nn1_acc <- confusionMatrix(factor(ifelse(nn1_pred>0.2, 1, 0)), xg_data_test$Email_Status1, positive="1")
print(nn1_acc) 
```
</details>
<br/>
{::options parse_block_html="false" /}
  
<h3 id="4. Targeted adjustments" style="color:black">4. Targeted adjustments</h3>
The lift chart shows how much more likely to capture positive respondents than contacting a random sample of prospects. We can use the predictive model to generate users’ probability of accepting the ads and visualize the correlation between the percentage of budget and target customers captured. The website can rank and select high probably audience in case of shrinking marketing budget. Between XGboost and neural network models, the latter is more efficient as it correctly predicted more converted customer.
  
{::options parse_block_html="true" /}
<details>
  
```r
test_obs <- length(xg_data_test$Email_Status1)
test_obs_yes <- sum(xg_data_test$Email_Status1==1)

ordered_outcome_nn <- xg_data_test$Email_Status1[order(nn1_pred, decreasing=T)]==1
plot((1:test_obs)/test_obs, cumsum(ordered_outcome_nn)/test_obs_yes, type='l', col='red', xlab = 'budget%', ylab = 'customer captured')

ordered_outcome_xg <- xg_data_test$Email_Status1[order(boost_preds_1, decreasing=T)]==1
lines((1:test_obs)/test_obs, cumsum(ordered_outcome_xg)/test_obs_yes, type='l', col='blue')

legend('bottomright', c('neural network', 'xgboost'), lty=1, col=c('red','blue'))
```
</details>
<br/>
{::options parse_block_html="false" /}

<img src="/assets/images/Marketing_Optimization/Email_feature.png" alt = "lift_chart.png" width="600"/>

With the XGboost built on training dataset, we can build an SHAPley explainer that shows how features contributing to push the model output from the base value (the average model output over the training dataset we passed). In other words, if we adjust accordingly, we can improve individual’s chance to click on an advertisement. We are using the ads data from initial campaign wave to improve our CTR performance in subsequent waves. Take the first customer in test dataset as an example, their predicted CTR will increase as we increase the hotness score of our email topic.

<img src="/assets/images/Marketing_Optimization/sharpley.png" alt = "sharpley" width="600"/>

<h2 id="Discussion" style="color:black">Discussion</h2>

As indicated from the customer segmentation and CTR distribution chart, customer group 1, the group with highest tenure and conversion rate, are the website’s loyal customers and main force for this campaign. The website can carry our more targeted activities to prolong their user lifetime. The predictive models suggest the website can attach more discount coupons in their advertisement. As for budget allocation adjustment, it can allocate more budget to customer group 1, and if it is considering expanding the target audience, it can include customers that have high spend on meat products as it was an important factor contributing to positive CTR. The website could design bundle promotion with wine and meat products, too.

It is worth noting that the two predictive models are not linked together, so the ads features importance are not calculated keeping customers demographics constant. That is, we can only compare ads features importance to each other, not to customer demographic features. Additionally, the sensitivity score of the predictive models are rather low, compromising its ability to capture customers that will actually click. Therefore, the insights should be taken with caution.

<h2 id="Conclusion and Future work" style="color:black">Conclusion and Future work</h2>
The project first uses clustering method k-medoids to segment customer base. From analyzing the characteristics of each customer cluster, it chooses 4 clusters that have higher visits and purchase frequency as campaign’s target audience. Then it uses Thompson Sampling to create an algorithm that keep track of each cluster’s CTR performance and always chooses the best performing group to deliver advertisement. After the ads display process, it analyzes the importance of two sets of features: ads characteristics and customer demographics. The results suggest coupon type email, user tenure, recency and users’ spend on meat have high influence on CTR.

Deficiency of data and separation between customer and ads data are two major problems that can be improved for future work. Current predictive models suffer from low sensitivity as there are more customers that did not click the advertisement, and tuning process seems to overfit the model to this major class. This problem should be improved by collecting more samples so that there are enough positive response, or we can try use synthetic approach such as SMOTE to generate positive samples. Since neural network approach generates a better result, we can also explore deep learning approach such as DeepFM for predicting CTR, and uses the according explainer to adjust strategy.

