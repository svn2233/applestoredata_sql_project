# Apple Store Dataset SQL Project

By: Steven Nguyen

## Introduction
In this project I will use SQL Lite to explore and analyze data and provide insights to the stakeholder. The dataset is found on Kaggle a website that offers free public datasets for all.

## Objective
An aspiring developer aims to create apps for the Apple App Store and seeks data-driven insights to determine which app to build. To provide valuable insights to my stakeholder, I will address questions such as:
	
 	1. What App Categories are the most popular?
	2. What price should the stakeholder set for his app?
	3. How Can he maximize users rating?
	4. Is there any trends that is seen in the database?

## Stakeholders

    [] Jerry Thompson- Aspiring Developer

## Steps

### 1. I will combine the applestore_description tables into 1 by using UNION ALL as it will make for easier analysis.
```
CREATE TABLE AS 

SELECT * FROM appleStore_description1
UNION ALL
SELECT * FROM appleStore_description2
UNION ALL 
SELECT * FROM appleStore_description3
UNION ALL
SELECT * FROM appleStore_description4
```

### 2. Now I will check to see if the table is actually combined
```
SELECT * FROM applestore_description_combined
```
![](images/1a.png)<!-- -->

### 3. I will check the # of unique apps in both tables 
```
SELECT COUNT(DISTINCT id) AS Unique_app_ids
FROM AppleStore

SELECT COUNT(DISTINCT id) AS Unique_app_ids
FROM applestore_description_combined
```
![](images/2b2.png)<!-- -->
### 4. Check for any missing values in key fields
```
SELECT COUNT(*) AS missing_values
FROM AppleStore
WHERE track_name is NULL OR user_rating is NULL OR prime_genre

SELECT COUNT(*) AS missing_values
FROM applestore_description_combined
WHERE app_desc is NULL
```
![](images/3b.png)<!-- -->

### 5. Find out # of apps per genre ordered by most popular genre
```
SELECT prime_genre, COUNT(*) as num_apps
FROM AppleStore
GROUP BY 1 
ORDER BY 2 DESC
```
![](images/4a.png)<!-- -->
### 6. Get overview of apps ratings 
```
SELECT min(user_rating) AS min_rating,
       max(user_rating) AS max_rating,
       avg(user_rating) AS avg_rating
FROM AppleStore
```
![](images/5a.png)<!-- -->

### 7. I will add a round function to avg. rating to two decimals to make it more readable.
```
SELECT min(user_rating) AS min_rating,
       max(user_rating) AS max_rating,
       round(avg(user_rating),2) AS avg_rating
FROM AppleStore
```
![](images/6a.png)<!-- -->

### 8. Determine whether paid apps have higher ratings than free apps
```
SELECT CASE
      WHEN price > 0 THEN 'Paid'
      ELSE 'Free'
      END AS app_type,
      round(avg(user_rating),2) as avg_rating
FROM AppleStore
GROUP BY 1
```
![](images/7a.png)<!-- -->

### 9. Check if apps with more supported languages have higher ratings
```
SELECT CASE
       WHEN lang_num < 10 THEN 'less than 10'
       WHEN lang_num BETWEEN 10 AND 30 THEN 'between 10 30'
       ELSE 'greater than 30'
       END AS num_of_languages,
       round(avg(user_rating),2) AS avg_rating
FROM AppleStore
GROUP BY 1
ORDER BY 2 DESC
```
![](images/8a.png)<!-- -->`

### 10. Top 10 genres with highest rating
```
SELECT prime_genre,
       round(avg(user_rating),2) as avg_rating
FROM AppleStore 
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```
![](images/9a.png)<!-- -->

### 11.Top 10 genres with lowest rating
```
SELECT prime_genre,
       round(avg(user_rating),2) as avg_rating
FROM AppleStore 
GROUP BY 1
ORDER BY 2 ASC
LIMIT 10
```
![](images/10a.png)<!-- -->

### 12. Check if there is a correlation between length of app desc. and user rating.
```
SELECT CASE
     WHEN length(b.app_desc) <500 THEN 'Short'
     WHEN length(b.app_desc) BETWEEN 500 AND 1000 THEN 'Medium'
     ELSE 'Long'
     END AS description_length,
     round(avg(a.user_rating),2) as average_rating
FROM AppleStore AS a
JOIN applestore_description_combined AS b 
ON a.id=b.id
GROUP BY 1
ORDER BY 2 DESC
```
![](images/11a.png)<!-- -->

### 13. Check the top-rated apps for each genre
```
SELECT prime_genre,
      track_name,
      user_rating
FROM (
     SELECT
     prime_genre,
     track_name,
     user_rating,
     RANK() OVER(PARTITION BY prime_genre order BY user_rating DESC, rating_count_tot DESC) AS Rank
     FROM
     AppleStore
     ) AS a
 WHERE
 a.rank=1
```
![](images/12a.png)<!-- -->

### 14. Checking to see the average price of each app
```
SELECT (ROUND(avg(price),2)||' '||currency) as avg_price,
       ROUND(avg(user_rating),2) AS avg_user_rating
FROM AppleStore
```
![](images/13a.png)<!-- -->

## Final Recommendations
* The Games category is the most popular, with nearly 6 times more popularity than the second most popular category, Entertainment. This suggests that the category may be over-saturated, but it also indicates a high user demand. Developing an app in this category could be a safe option to achieve high user engagement due to the substantial demand.
* When Jerry develops an app, he should aim to support 10 to 30 languages, as apps with this level of language support tend to receive better ratings.
* There are opportunities in the Finance and Catalogs categories, as they currently have the lowest user ratings. This indicates that apps in these categories are not meeting users' needs. If Jerry can address those needs effectively, there is potential for high user ratings and market penetration.
* Jerry should not make his app free; instead, he should set a price, as apps that are not free tend to have better ratings.
* To improve ratings, Jerry should create apps with longer descriptions, as there is a positive correlation between longer descriptions and better ratings. This suggests that users prefer to read more about the app before deciding to download it.
* The average rating of all apps is 3.5, so Jerry should aim for a user rating above 3.5 to stand out from the crowd.
* The average price of an app is $1.73 USD. Jerry should consider setting his app's price within this range.
