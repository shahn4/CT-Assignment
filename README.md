# CT-Assignment 1
Cloud Technologies Assignment 1 - Stack Exchange Data
* Name - Nirali Vikramkumar Shah  
* Student Number - 21260310

**Tasks**
---
1. Get data from Stack Exchange (Data Acquisition/Collection)] 


2. Load data into chosen cloud technology (MapReduce/Pig/Hive)]


3. Query data using MapReduce/Pig/Hive Task 4: Calculate TF-IDF with MapReduce/Pig/Hive]

**Task Description**

1. Task 1 - Acquire the top 200,000 posts by ViewCount (see Section 3 - Data Acquisition for more details)


2. Task 2 & 3 - Use Pig/Hive/MapReduce - Extract, Transform and Load the data as applicable to get:


- The top 10 posts by score
- The top 10 users by post score
- The number of distinct users, who used the word “cloud” in one of their posts


3. Task 4- Use Mapreduce/Pig/Hive to calculate the per-user TF-IDF of the top 10 terms for each of the top 10 users

***
**Data Acquisition/Collection** 
> Description - We are acquiring top 2 lakh posts from the Stack Exchange website (https://data.stackexchange.com/stackoverflow/query/new) . I ran the query four times to get the top 50k posts each time. I did a couple queries to find the range of top 2L posts. Queries are follows –


```sql
select count(*) from posts where posts.ViewCount > 127350


select count(*) from posts where posts.ViewCount > 74500 and posts.ViewCount < 127350


select count(*) from posts where posts.ViewCount > 53000 and posts.ViewCount < 74500


select count(*) from posts where posts.ViewCount > 41200 and posts.ViewCount < 53000
```


Then, I used the following queries to acquire top 2L posts


```sql
select top 50000 * from posts where posts.ViewCount > 127350 ORDER BY posts.ViewCount


select top 50000 * from posts where posts.ViewCount > 74500 and posts.ViewCount < 127350 ORDER BY posts.ViewCount


select top 50000 * from posts where posts.ViewCount > 53000 and posts.ViewCount < 74500 ORDER BY posts.ViewCount


select top 50000 * from posts where posts.ViewCount > 41200 and posts.ViewCount < 53000 ORDER BY posts.ViewCount
```
