<h1>NBA Draft </h1>


<h2>Description</h2>
The NBA began recording measurements of NBA prospects before the 2000-2001 NBA season. Since then, many athletes choose to have their physical attributes recorded to impress teams and advance their draft position. I wanted to compare the results of the positions of players entering the draft and how their position effects their physical attributes. I uesd MySQL for my analysis.
<br />
<br />
I had 5 main questions I wanted answered from the data:
<br />
1. How many players were drafted in each position that played in NBA games?
<br />
2. What i the average vertial jump and bench press of each position?
<br />
3. What is the average draft selection of each position?
<br />
4. What is the average height for each position? Who is the tallest and shortest?
<br />
5. What is the average minutes played per game for each position?
<br />
<br />
<h2>Languages and Programs Used</h2>

- MySQL

<h2>Program walk-through:</h2>

First I had to import the 2 .CSV files i needed into MySQL workbench. One file imported completely through the import wizard but I had to import another one manualy using command lines. 
<br />
```

SET GLOBAL local_infile = 1


LOAD DATA LOCAL INFILE 'C:/Users/wicro/Desktop/Portfolio projects/NBA_SQL/nbacareerdata'
INTO TABLE nba_players_career_data
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

```
I now had 2 tables imported. One had the combine results of players and the other had draft selection and career results. I then used the JOIN function to merge the tables into one.
<br />
```
# Join the tables by player names
CREATE TABLE nba.full_stats
SELECT *
FROM nba.draft_combine_stats
Join nba.nba_players_career_data 
ON draft_combine_stats.player_name = nba_players_career_data.player;
```
<br />
<br />
My next step was to clean the new table I created. I did this by finding differences in the year values of the draft.

```
# Finds players who have same name by finding difference in year
SELECT *
FROM nba.full_stats
WHERE season != year
```

<br />
<br />
After finding values that did not match, I deleted the rows.

```
# Delete players who have same name with wrong stats
DELETE FROM nba.full_stats
WHERE player_id = 203318

DELETE FROM nba.full_stats
WHERE player_id = 1629018

DELETE FROM nba.full_stats
WHERE player_id = 12203

DELETE FROM nba.full_stats
WHERE player_id = 1628972

DELETE FROM nba.full_stats
WHERE player_id = 201147
```
Now that the data is clean and transformed, I answer Question 1 by finding the number of players in each position drafted who played in NBA games

```
# Used LIKE because some players play multiple positions
# Number of Point Guards in draft (138)
SELECT COUNT(*)
FROM nba.full_stats
WHERE position LIKE '%PG%'

# Number of Shooting Guards in draft (202)
SELECT COUNT(*)
FROM nba.full_stats
WHERE position LIKE '%SG%'

# Number of small forwards in draft (176)
SELECT COUNT(*)
FROM nba.full_stats
WHERE position LIKE '%SF%'

# Number of Power Forwards in draft (181)
SELECT COUNT(*)
FROM nba.full_stats
WHERE position LIKE '%PF%'

# Number of Centers in draft (88)
SELECT COUNT(*)
FROM nba.full_stats
WHERE position LIKE '%C%'
```


<br />
<br />

<br />
<br />
<br />
<br />

<br />
<br />
<a href="https://imgur.com/GzIzJRq"><img src="https://i.imgur.com/GzIzJRq.png" title="source: imgur.com" /></a>
<br />
<br />
<a href="https://imgur.com/gAtJAsv"><img src="https://i.imgur.com/gAtJAsv.png" title="source: imgur.com" /></a>
<br />
<br />
<br />
<br />
After thoroughly cleaning the data, I launched Power BI and connected it to the MySQL database. <br/>
<br />
<br />
<a href="https://imgur.com/6IbS6UQ"><img src="https://i.imgur.com/6IbS6UQ.png" title="source: imgur.com" /></a>
<br />
<br />
<br />
<br />
I then needed to calculate extra values to create the visuals. I used formulas in the data view to create calculations and new columns to create insightful visualizations.  <br/>
<br />
<br />
<a href="https://imgur.com/LLYmDr0"><img src="https://i.imgur.com/LLYmDr0.png" title="source: imgur.com" /></a>
<br />
<br />
<a href="https://imgur.com/Ho4vdOw"><img src="https://i.imgur.com/Ho4vdOw.png" title="source: imgur.com" /></a>
<br />
<br />
<br />
<br />
I then proceeded to create the visualizations. I kept in mind the goal of the project which was to compare Subscribers to Casual Customers when creating the charts. I compared the trip data between the two across multiple metrics such as trip duration, day of the week, gender, and time of day<br />
<br />
<br />
<a href="https://imgur.com/n8jxnz2"><img src="https://i.imgur.com/n8jxnz2.png" title="source: imgur.com" /></a>
<br />
<br />
<a href="https://imgur.com/bMLel9Q"><img src="https://i.imgur.com/bMLel9Q.png" title="source: imgur.com" /></a>
<br />
<br />
<a href="https://imgur.com/gm2jrq7"><img src="https://i.imgur.com/gm2jrq7.png" title="source: imgur.com" /></a>
<br />
<br />
<br />
<br />
After I selected the most relevant charts, I created a PowerPoint presentation to present to investors. This included my conclusion to my analysis and my recommendations for converting Casual Riders to Subscribers.
<br />
<br />
<a href="https://imgur.com/l1hmOY9"><img src="https://i.imgur.com/l1hmOY9.png" title="source: imgur.com" /></a>
<br />
<br />
<h1>Conclusion</h1>
My prior analysis lead me to the conclusion that Casual Riders typically use the service for longer leisure trips and Subscribers use it for regular commutes. My conclusion is backed by the data and can be clearly understood by visualizations. 
<br />
<br />
I made 3 recommendations to convert Casual riders to Subscribers.
<br />
<br />
1. Visitors of Chicago are not incentivized to subscribe, unless they have access to the same service in their home city or if they travel to other cities enough with the service provided. Expanding service to other cities will increase subscribers for travelers and workers who frequently travel.
<br />
<br />
2. Adding another type of subscription service may be beneficial. Whether it be for weekend use or seasonal. However, this may discourage people to sign up annually and choose the smaller commitment.
<br />
<br />
3. Increasing the cost of weekend rides could make Casual Riders more inclined to switch to subscription service if they frequently use the bikes on weekends.

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
