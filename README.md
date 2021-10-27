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
**Technologies Used** – SQL Queries to acquire top 2L posts.

>Uploading files in GCP
 
**Description**-I have uploaded all the data directly on GCP.


**Technologies Used**- Basic LINUX commands to upload and list the files.


**Cleaning the data**


**Description** - Removing HTML tags using sed command in LINUX and storing it in a new file named Data1,Data2 and so on.


**Technologies Used** – Sed command in LINUX for Data cleaning.


**Reference**- https://stackoverflow.com/questions/19878056/sed-remove-tags-from-html-file/19878198


**Commands** 
```bash
nirali_shah4@ct-assignment1-m:~$ sed -e 's/<[^>]*>//g' 'QueryResults (1).csv'>Data1
nirali_shah4@ct-assignment1-m:~$ sed -e 's/<[^>]*>//g' 'QueryResults (2).csv'>Data2
nirali_shah4@ct-assignment1-m:~$ sed -e 's/<[^>]*>//g' 'QueryResults (3).csv'>Data3
nirali_shah4@ct-assignment1-m:~$ sed -e 's/<[^>]*>//g' 'QueryResults (4).csv'>Data4
```
 
**Description** – Removing Punctuation using sed command for all the files.


**Technologies Used** – Sed command in LINUX for Data cleaning.


**Reference** - https://stackoverflow.com/questions/42108365/removing-punctuation-and-tabs-with-sed


**Commands**
```bash
nirali_shah4@ct-assignment1-m:~$ sed -e $'s/\t//g' -e "s/[[:punct:]]\+//g" -e 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/' Data1 > NoPunctuation1
nirali_shah4@ct-assignment1-m:~$ sed -e $'s/\t//g' -e "s/[[:punct:]]\+//g" -e 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/' Data2 > NoPunctuation2
nirali_shah4@ct-assignment1-m:~$ sed -e $'s/\t//g' -e "s/[[:punct:]]\+//g" -e 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/' Data3 > NoPunctuation3
nirali_shah4@ct-assignment1-m:~$ sed -e $'s/\t//g' -e "s/[[:punct:]]\+//g" -e 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/' Data4 > NoPunctuation4
```

**Concatenating all 4 files**

**Command**
```bash
Cat NoPunctuation1 NoPunctuation1 NoPunctuation1 NoPunctuation1 >  Final-Data
```

**Uploading the final file to HDFS**

**Command**

```hadoop
hadoop fs –put Final-Data.csv
```

**Creating table**

**Description** - Creating Database and Table in Hive.

**Technologies Used** – HQL

**Queries**-

```hql
>>CREATE DATABASE ct_assignment;
>>USE ct_assignment;
```
```hql
CREATE TABLE ViewCount(Id INT ,PostTypeId INT,AcceptedAnswerId INT,ParentId INT,CreationDate STRING,DeletionDate STRING,Score INT,ViewCount INT,Body VARCHAR(10000),OwnerUserId INT,OwnerDisplayName STRING,LastEditorUserId INT,LastEditorDisplayName STRING,LastEditDate STRING,LastActivityDate STRING,Title STRING,Tags STRING,AnswerCount INT,CommentCount INT,FavoriteCount INT,ClosedDate STRING,CommunityOwnedDate STRING,ContentLicense STRING) ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```

**The top 10 posts by score**
**Query** –  
```sql
SELECT id,score,viewcount,title FROM viewcount ORDER BY score DESC LIMIT 10;
```
**Technologies Used** – HQL
 

**The top 10 users by post score**
**Query** - 
```hql
SELECT owneruserid AS USERID, SUM(score) AS SCORE FROM viewcount GROUP BY owneruserid having owneruserid is not null SORT BY score DESC LIMIT 10;
```

**The number of distinct users, who used the word “cloud” in one of their posts**
**Query** - 
```hql
SELECT COUNT(DISTINCT owneruserid) FROM viewcount WHERE UPPER(body) LIKE '%CLOUD%' OR UPPER(title) LIKE '%CLOUD%' OR LOWER(body) LIKE '%cloud%' OR UPPER(tags) LIKE '%CLOUD%' OR LOWER(title) LIKE'%cloud%' OR LOWER(tags) LIKE '%CLOUD%';
```
 
**Calculating the per-user TF-IDF of the top 10 terms for each of the top 10 users** 
**Query**– 
```sql
add jar hivemall-core-0.4.2-rc.2-with-dependencies.jar;
source define-all.hive;
create temporary macro max2(x INT, y INT) if(x>y,x,y);
create temporary macro tfidf(tf FLOAT, df_t INT, n_docs INT) tf * (log(10, CAST(n_docs as FLOAT)/max2(1,df_t)) + 1.0);
create table topUsers as select owneruserid, sum(score) as OverallScore from viewcount group by OwnerUserId order by OverallScore desc limit 10;
create table topUsers1 as select d.OwnerUserID,title from viewcount d join topUsers t on  d.OwnerUserID = t.OwnerUserID;
create or replace view topUsersExplode as select ownerUserId, eachword from topUsers1 LATERAL VIEW explode(tokenize(Title, True)) t as eachword where not is_stopword(eachword);
create or replace view tf_temp as select ownerUserid, eachword, freq from (select ownerUserId, tf(eachword) as word2freq from topUsersExplode group by ownerUserId) t LATERAL VIEW explode(word2freq) t2 as eachword, freq;
create or replace view tf as select * from (select ownerUserId, eachword, freq, rank() over (partition by ownerUserId order by freq desc) as rank from tf_temp ) t where rank < 10;
select owneruserid,eachword,freq from tf;
```
References –
1.	https://github.com/rajesh-codes/Stack-Exchange-Data-Analysis
2.	https://github.com/swathikiran86/pig-Hive-programmming-on-StackExchange-data
3.	https://stackoverflow.com/questions/42108365/removing-punctuation-and-tabs-with-sed



