# Netflix Movies and TV Shows analysis using SQL 

![Netflix Logo](https://github.com/DiegoZenas/Netflix-Project/blob/main/logo.png)

## Objective

-- Netflix project
 drop table if exists netflix;
Create table netflix
(
	show_id	VARCHAR(6),
	type	VARCHAR(10),
	title	VARCHAR(150),
	director VARCHAR(208),
	casts VARCHAR( 1000),
	country	VARCHAR(150),
	date_added	VARCHAR (50),
	release_year	Int,
	rating VARCHAR(10),
	duration	VARCHAR(15),
	listed_in	VARCHAR(100),
	description VARCHAR(250)
);

select * from netflix;


select 
	count(*) as total_content
from netflix;

select 
	distinct type
from netflix;


## count tv vs movies
select* from netflix
select
	type,
	count(*) as total_content
	from netflix
	group by type

## common rating for movies and tv shows

SELECT type, rating
FROM
(
    SELECT
        type,
        rating,
        COUNT(*),
        RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking
    FROM netflix
    GROUP BY 1, 2
) AS t1
WHERE ranking = 1;


## list all the movies released in 2020

select * from netflix
WHERE 
	type = 'Movie'
	AND
	release_year = 2020


## top 5 countries with most content on netflix

select 
		unnest(String_to_array (country, ',')) as new_country,
	COUNT(show_id) as total_content
from netflix
group by 1
order by 2 desc
limit 5

select 
	unnest(String_to_array (country, ',')) as new_country
From netflix

## longest movie

select * from netflix
where
	type = 'Movie'
	and 
	duration = (select max(duration)from netflix)

## content add in last 10 years

select 
	*
from netflix
where
	to_date(date_added, 'month DD, YYYY') >= current_date - interval '10 years'

## find conten by director rajiv chilaka
select * from netflix
where director Ilike '%Rajiv Chilaka%'

## list tv shows with more than 5 seasons
select 
	*
from netflix 
where 
	type = 'TV Show'
	and
 SPLIT_PART (duration, ' ',1)::numeric > 5 

 ## count # of content in each genre

select 
	unnest(string_to_array(listed_in, ',')) as genre,
	count(show_id) as total_content
from netflix
group by 1

## find each year and the average numbers of content released by united states on netflix.  return top 5 years with highest avg content release


select 
	extract(year from to_date( date_added, 'Month DD, YYYY')) as year,
	count (*) as yearly_content,
	round(
	count (*)::numeric/(select count(*) from netflix where country = 'United States'):: numeric * 100
	,2) as avg_content_per_yeat
from netflix
where country ='United States'
group by 1

## list movies that are documentaries
select * from netflix
where
listed_in Ilike '%documentaries%'

## find all content without a director
select * from netflix
where
director is null

## how many movies did salam kham appeared in the last ten years
select * from netflix
where
	casts ilike '%Salman Khan%'
	and
	release_year > extract(year from current_date) - 10

## find top 10 actors from the US
select
unnest(String_To_Array(casts, ',')) as actors,
count(*) as total_content
from netflix
where country ILIKE '%United States%'
group by 1

order by 2 DESC
limit 10

## categorize the content based on the presence of the keywords 'kill' and violence in the description field. Label content containing these keywords as 'bad' and all other content as 'good'. Count how many items fall into each catergory
  with new_table
  as
  (
  select
  	*,
  	case
	when description Ilike '%kill%' or description ilike '%violence%' then 'Bad_content'
	else 'good content'
	end category
  from netflix
  )
select 
	category,
	count(*) as total_content
from new_table
group by 1
