---
layout: post
title: Etiam
description: 
image: 
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, message = FALSE)
```

## Bronze

### Question 1: Production Cost Minimization

Code the following model so that you minimize production and storage costs over a 4 month period:

$$
Min: \sum\limits_{i=1}^4 (12m_i + 2s_i) \\
m1 - s1 = 100 \\
s1 + m2 - s2 = 200 \\
s2 + m3 - s3 = 150 \\
s3 + m4 - s4 = 400 \\
m1 \leq 400 \\
m2 \leq 400 \\
m3 \leq 300 \\
m4 \leq 300
$$


What is the total cost for this problem? Provide a very brief explanation of the first 4 constraints.

```{r}
c_vector1 <- rep(c(12,2),times=4)
b_vector1 <- c(100,200,150,400,400,400,300,300)
A_mat1 <- rbind(c(1,-1,0,0,0,0,0,0),
                c(0,1,1,-1,0,0,0,0),
                c(0,0,0,1,1,-1,0,0),
                c(0,0,0,0,0,1,1,-1),
                c(1,0,0,0,0,0,0,0),
                c(0,0,1,0,0,0,0,0),
                c(0,0,0,0,1,0,0,0),
                c(0,0,0,0,0,0,1,0))

con_dir1 <- c(">=",">=",">=",">=","<=","<=","<=","<=")

product_1 <- linprog::solveLP(cvec = c_vector1 , 
                 bvec= b_vector1 ,
                 Amat = A_mat1, 
                 maximum = FALSE,
                 const.dir = con_dir1)

cost1 <- sum(c_vector1 * product_1$solution)
```
Answer: The total cost of is 10,400. The first 4 constraints represents the inventory from last month adding the production of this month and substracting the intentory of this month equal to a constant, so it means sales of that month.

### Question 2: Convenience Store Diet

The average resident of Paris, Illinois needs to consume at least 2000 calories a day from the nearest convenience store; naturally, it needs to be done as cheaply as possible and a healthy diet typically consists of at least 10 ounces of sugar. A slice of pizza costs 3 dollars, coffee is 1 dollar, Red Bull is usually on sale for 1.50 per can, and candy bars cost 1 dollar.

A slice of pizza contains 285 calories and practically 0 sugar.

A cup of sweet coffee contains 1/2 oz of sugar and 100 calories.

A can of Red Bull contains 168 calories and 2 oz of sugar.

A candy bar contains about 200 calories and 3 oz of sugar.

What is the total cost to consume the proper amount of food and how much of each food should be bought?
$$
Min: 3Pizza+1Coffee+1.5Red+1Candy\\
Calories: 285Pizza + 100Coffee + 168Red + 200Candy \geq 2000\\
Sugar: .5Coffee+2Red+3Candy \geq 10 \\

$$

```{r}
c_vector2 <- c(3,1,1.5,1)
b_vector2 <- c(2000,10)
A_mat2 <- rbind(c(285, 100, 168, 200),
                c(0,0.5,2,3))
con_dir2 <- c(">=", ">=")
product_2 <- linprog::solveLP(cvec = c_vector2 , 
                 bvec= b_vector2 ,
                 Amat = A_mat2, 
                 maximum = FALSE,
                 const.dir = con_dir2)
product_2$solution
cost2 <- sum(c_vector2*product_2$solution)
cost2
#Sounds like an awful diet...
```
Answer: The lowest total cost is 10 dollars for buying 10 candy bars.

### Question 3: Steel Mill Emissions

A steel mill is trying to reduce emissions of three particular kinds of air pollutants.

The following are clean air standards for the facility (in millions of pounds):

```{r}
data.frame(pollutant = c("particulates", "sulfer oxides", "hydrocarbons"), 
           requiredReduction = c(60, 150, 125))
```

The steel mill has two major sources of these pollutants: blast furnaces and open-hearth furnances. To reduce the pollutants from these two furnace types, engineers are exploring using taller smokestacks, filters, and cleaner fuels.

The following are maximum estimated reduction rates (in millions of pounds) for various abatement methods:

```{r}
data.frame(pollutant = c("particulates", "sulfer oxides", "hydrocarbons"), 
           ts_blastFurnace = c(12, 35, 37), 
           ts_openHearth = c(9, 42, 53), 
           filter_blastFurnace = c(25, 18, 28), 
           filter_openHearth = c(20, 31, 24), 
           fuels_blastFurnace = c(17, 56, 29), 
           fuels_openHearth = c(13, 49, 20))
```

Each of these abatement methods can be used to any proportion.

The following table specifies the cost for fully-adopting each method (if the proportion is 1):

```{r}
data.frame(method = c("taller smokestacks", "filters", "fuels"), 
           blastFurnaces = c(8, 7, 11), 
           openHearth = c(10, 6, 9))
```


The ultimate goal of the steel mill is to minimize the money spent on each abatement method, while hitting the require reduction thresholds.

This creates the following linear program:

$$Minimize Z = 8_{x1} + 10_{x2} +7_{x3} +6_{x4} + 11_{x5} + 9_{x6}$$

**Subject to the following emission reduction constrains:**

$$12_{x1} + 9_{x2} + 25_{x3} + 20_{x4} + 17_{x5} + 13_{x6} \geq 60 \\
35_{x1} + 42_{x2} + 18_{x3} + 31_{x4} + 56_{x5} + 49_{x6} \geq 150 \\
37_{x1} + 53_{x2} + 28_{x3} + 24_{x4} + 29_{x5} + 20_{x6} \geq 125
$$

**Subject to the following technology limitation constrains:**

$$x_j \leq 1$$

What is each abatement methods optimal proportion?
```{r}
c_vector3 <- c(8, 10, 7, 6, 11, 9)
b_vector3 <- c(60,150,125,rep(1,times=6))
A_mat3 <- rbind(c(12,9,25,20,17,13),
                c(35,42,18,31,56,49),
                c(37,53,28,24,29,20),
                diag(6))
con_dir3 <- c(">=", ">=", ">=", rep("<=",6))
product_3 <- linprog::solveLP(cvec = c_vector3 , 
                 bvec= b_vector3 ,
                 Amat = A_mat3, 
                 maximum = FALSE,
                 const.dir = con_dir3)
product_3

```
On blast furnaces, the results suggest applying 1 taller smoke, 0.34 filters and 0.04 cleaner fuels


On open hearth, the results suggest applying 0.62 taller smoke, 1 filters and 1 cleaner fuels.

the proportion is then (1+0.62):(0.34+1):(0.04+1), 
which is 0.405:0.335:0.26


## Silver

Let's extend that question 1 just a little bit more.

The cost of labor over the next four months will go up by 3 dollars a month, starting at 12 dollars. Every unit that gets made needs to go into storage and it requires 2 dollars to store each unit. Every unit made requires 30 minutes of labor. Fortunately, exact demand and available labor are known months in advance and are as follows: demand = 100, 200, 150, 400; max labor hours = 200, 200, 150, 150. Anything made during a month can be stored and reused the following month. Add the increased labor costs and resources to your model, and then report the production plan.

$$
Min: \sum\limits_{i=1}^4 (7.5m_i + 2s_i) \\
m1 - s1 \geq 100 \\
s1 + m2 - s2 \geq 200 \\
s2 + m3 - s3 \geq 150 \\
s3 + m4 - s4 \geq 400 \\
m1 \leq 400 \\
m2 \leq 400 \\
m3 \leq 300 \\
m4 \leq 300
$$
```{r}
c_vector4 <- rep(c(7.5,2),times=4)
b_vector4 <- c(100,200,150,400,400,400,300,300)
A_mat4 <- rbind(c(1,-1,0,0,0,0,0,0),
                c(0,1,1,-1,0,0,0,0),
                c(0,0,0,1,1,-1,0,0),
                c(0,0,0,0,0,1,1,-1),
                c(1,0,0,0,0,0,0,0),
                c(0,0,1,0,0,0,0,0),
                c(0,0,0,0,1,0,0,0),
                c(0,0,0,0,0,0,1,0))

con_dir4 <- c(">=",">=",">=",">=","<=","<=","<=","<=")

product_4 <- linprog::solveLP(cvec = c_vector4 , 
                 bvec= b_vector4 ,
                 Amat = A_mat4, 
                 maximum = FALSE,
                 const.dir = con_dir4)
product_4
```
Answer: The production plan would be: produce 100,200,250,300 for the first four months, respectively.
## Gold

The hardest of all questions -- put down your Google and create your own linear optimization problem! It can be a minimization or maximization problem. You need at least 3 variables and at least 3 constraints. Explain the set-up, write the notation, code it up, and then tell me what you found! Seriously, no need to involve Google in this -- come up with something on your own.

//I have to minimize my groceries cost while finish the groceries I bought last week, otherwise they will rot in my fridge (especially fresh vegetables). I'm wondering how I can optimize the proportion of money I put into each category

Meat x1
Fresh vegetables x2
Storage vegetables x3

10% of the meat, 80% of the fresh vegetables and 30% of the storage vegetables perishes at the end of the week

average price of meat, fresh vegetables and storage vegetables are $17, $10 and $3

I want to minimize the cost of perished groceries:

Min: 0.1*17X1 + 0.8*10x2 + 0.3*3x3

Meat provides 117gram of protein, 9000IU vitamin; Fresh vegetables provides 5gram of protein, and 19400IU vitamin; Storage provides 3gram of protein and 15000IU vitamin. 

To meet my protein demand: 
117x1 + 5x2 + 3x3 >= 350
To meet the vitamin demand: 
9000x1 + 19400x2 + 16000x3 >= 50000

My refrigerator has limited capacity:
x1 + x2 + x3 <= 30

I want at least 3 pound of meat: 
x1 >= 3

$$
Min: 1.7x1 + 8x2 + 0.9x3 \\
St:117x1 + 5x2 + 3x3 \geq 350 \\
9000x1 + 19400x2+16000x3 \geq 50000 \\
x1+x2+x3 \leq 30 \\
x1 \geq 3
$$

```{r}
c_vector5 <- c(1.7,8,0.9)
b_vector5 <- c(400,500000,30,3)
A_mat5 <- rbind(c(117,5,3),
                c(9000,19400,16000),
                c(1,1,1),
                c(1,0,0))
con_dir5 <- c(">=",">=","<=",">=")

product_5 <- linprog::solveLP(cvec = c_vector5 , 
                 bvec= b_vector5 ,
                 Amat = A_mat5, 
                 maximum = FALSE,
                 const.dir = con_dir5)
product_5
```
I should buy 3 pound of meat, 12 pound of fresh vegetables and 15 pound of storage vegetables each week. 
