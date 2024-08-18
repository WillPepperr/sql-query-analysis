<h1>NBA Draft Combine </h1>


<a href="https://imgur.com/s5fkL8N"><img src="https://i.imgur.com/s5fkL8N.png" title="source: imgur.com" /></a>

<h2>Description</h2>
The NBA began recording measurements of NBA prospects before the 2000-2001 NBA season. Since then, many athletes choose to have their physical attributes recorded to impress teams and advance their draft position. I wanted to compare the results of the positions of players entering the draft and how their position affects their physical attributes. My goal is to answer the following question. What physical attributes, on average, does an aspiring NBA draft selection need, for his position, to  be selected and play in the NBA? 
<br />
<br />
I thought of 5 guiding questions an analyst must know to understand the data.
<br />
<br />
1. How many players were drafted in each position that played in NBA games?
<br />
<br />
2. What is the average vertical jump and bench press of each position?
<br />
<br />
3. What is the average draft selection of each position?
<br />
<br />
4. What is the average height for each position? Who is the tallest and shortest?
<br />
<br />
5. What are the average minutes played per game for each position?
<br />
<br />


<h2>Languages and Programs Used</h2>

- MySQL

<h3>Preparing the data:</h3>

First I had to import the 2 .CSV files I needed into MySQL workbench. One file was imported completely through the import wizard but I had to import another one manually using command lines. 
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
<br />
<br />
I now had 2 tables imported. One had the Combine results of players and the other had the draft selection and career results. I then used the JOIN function to merge the tables into one.

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
<br />
<br />
I then adjusted the data so that players had uniform position names.

```
# Merge PF-C and C-PF as same position
UPDATE nba.full_stats
SET position = REPLACE(position, 'C-PF', 'PF-C')
WHERE position LIKE '%C-PF%' 

# Merge SF-PF and PF-SF
UPDATE nba.full_stats
SET position = REPLACE(position, 'PF-SF', 'SF-PF')
WHERE position LIKE '%PF-SF%' 

# Merge SG-SF and SF-SG
UPDATE nba.full_stats
SET position = REPLACE(position, 'SF-SG', 'SG-SF')
WHERE position LIKE '%SF-SG%' 

# Merge PG-SG and SG-PG
UPDATE nba.full_stats
SET position = REPLACE(position, 'SG-PG', 'PG-SG')
WHERE position LIKE '%SG-PG%' 

```
<br />
<br />

<h3>Question 1: Finding how many players were drafted in each position.</h3>

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
<h3>Question 2 Finding the average vertical jump and bench press for each position.</h3>

```
# Find the average vertical and bench press for each position

SELECT position, AVG(standing_vertical_leap) AS avg_standing_vertical, AVG(bench_press) AS avg_bench_press
FROM nba.full_stats
GROUP BY position;
```
![image](https://user-images.githubusercontent.com/127700723/226064050-dfc46049-9b2f-49ee-88ce-170e229c86c2.png)
<br />
<br />
I made an interesting observation. I noticed the bench press averages were significantly higher for players who played 2 positions. I decided to compare the averages between players listed at 1 and 2 positions.

```
# From the result of the previous query, I noticed that those who played 2 positions on average had a higher bench press, decided to compare 2 VS 1 position stats

SELECT
  CASE 
    WHEN position LIKE '%-%' THEN 'Two Positions' 
    ELSE 'One Position' 
  END AS group_position, 
  AVG(standing_vertical_leap) AS avg_standing_vertical, 
  AVG(bench_press) AS avg_bench_press 
FROM nba.full_stats
GROUP BY group_position;
 
```
<a href="https://imgur.com/afohBWA"><img src="https://i.imgur.com/afohBWA.png" title="source: imgur.com" /></a>
<br />
<br />

<h3>Question 3: Finding the average draft selection by position.</h3>

```

# Finds the player count of each position and the average selection in the draft

SELECT position, COUNT(*) AS player_count, AVG(overall_pick) AS average_selection
FROM nba.full_stats
GROUP BY position 

```
<a href="https://imgur.com/GoQ5UDn"><img src="https://i.imgur.com/GoQ5UDn.png" title="source: imgur.com" /></a>
<br />
<br />
<h3>Question 4: Finding average height and tallest/shortest players.</h3>
 
```
# Finds the tallest and shortest player in each position and lists their names
SELECT full_stats.position, 
       MAX(p1.height_wo_shoes) AS tallest_height, 
       MIN(p2.height_wo_shoes) AS shortest_height, 
       SUBSTRING_INDEX(GROUP_CONCAT(DISTINCT p1.player_name ORDER BY p1.height_wo_shoes DESC SEPARATOR ', '), ',', 1) AS tallest_player, 
       SUBSTRING_INDEX(GROUP_CONCAT(DISTINCT p2.player_name ORDER BY p2.height_wo_shoes ASC SEPARATOR ', '), ',', 1) AS shortest_player 
FROM nba.full_stats AS full_stats
JOIN nba.full_stats AS p1 ON full_stats.position = p1.position AND full_stats.height_wo_shoes = p1.height_wo_shoes
JOIN nba.full_stats AS p2 ON full_stats.position = p2.position AND full_stats.height_wo_shoes = p2.height_wo_shoes
GROUP BY full_stats.position
ORDER BY tallest_height DESC;
 
```
<a href="https://imgur.com/CCumtqE"><img src="https://i.imgur.com/CCumtqE.png" title="source: imgur.com" /></a>
<br />
<br />
<h3>Question 5: Finding average minutes played per position.</h3>

```
# Finds the average minutes of each position per game
SELECT position, COUNT(*) AS player_count, AVG(average_minutes_played) AS average_minutes
FROM nba.full_stats
GROUP BY position 
```
<a href="https://imgur.com/Az7Vgxt"><img src="https://i.imgur.com/Az7Vgxt.png" title="source: imgur.com" /></a>


<H2>Conclusion</H2>
I gained a lot of insight through my analysis. My most notable observation was that the average player who is listed at
2 positions can bench press almost 2 reps (25%) more than a player who plays a single position across all positions. 
This indicates strength is an important factor in being a more versatile player. I also spotted average minutes played among
players who played 2 positions were higher than those with 1 by about %15. However, only 30% of players who were drafted 
identified themselves as a 2 position player. 

















