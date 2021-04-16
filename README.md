# Insights on Disney Movies Gross Analysis with SQL

For this analysis, I will be using a dataset contains all 579 movies betwem 1937 and 2016 released by Disney to determine the top 20% Movies that brought 80% of Disney's Gross Income, using Pareto principle. The data for this project was downloaded from [Kaggle]('https://www.kaggle.com/rashikrahmanpritom/disney-movies-19372016-total-gross')

--
-- What is the Total Gross after adjusted inflation
--
SELECT SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS "Total Gross" -- TotalInflationAdjustedGross
FROM disney_movies_total_gross dm
--
-- Disney made a total of USD 68,763,500,997 in income in 79 years, just wow!

--
-- What is the total number of movies released
--
SELECT COUNT(dm.movie_title) AS "Movie Title" 
FROM disney_movies_total_gross dm
--
-- Disney released a total of 579

-- 
-- What are the Top Movies by Gross Income
--
SELECT dm.movie_title AS "Movie Title"
	   ,CAST(dm.release_date AS DATE) AS "Release Date"
	   ,SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS "Movie Gross"
FROM disney_movies_total_gross dm
GROUP BY dm.movie_title, CAST(dm.release_date AS DATE)
ORDER BY SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) DESC
--
-- Snow White and the Seven Dwarfs was the best performing movie in terms of gross income as at 2016

-- 
-- What are the Bottom Movies by Gross Income
--
SELECT dm.movie_title AS "Movie Title"
	   ,CAST(dm.release_date AS DATE) AS "Release Date"
	   ,SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS "Movie Gross"
FROM disney_movies_total_gross dm
GROUP BY dm.movie_title, CAST(dm.release_date AS DATE)
ORDER BY SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) ASC
--
-- The 'The Many Adventures of Winnie the Pooh' was the worst performing movie in terms of gross income as at 2016

-- 
-- What are the Top Genres by Gross Income
--
SELECT dm.genre AS Genre
	   ,dm.movie_title AS "Movie Title"
	   ,CAST(dm.release_date AS DATE) AS "Release Date"
	   ,SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS "Movie Gross"
FROM disney_movies_total_gross dm
WHERE dm.genre IS NOT NULL
GROUP BY dm.genre, dm.movie_title, CAST(dm.release_date AS DATE)
ORDER BY SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) DESC
--
-- Insterestinly, Disney seems to make more money from Adventure Movies

-- 
-- What are the Top MPAA Film Rating system by Gross Income
--
SELECT dm.mpaa_rating AS "MPAA Rating"
	   ,dm.movie_title AS "Movie Title"
	   ,CAST(dm.release_date AS DATE) AS "Release Date"
	   ,SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS "Movie Gross"
FROM disney_movies_total_gross dm
WHERE dm.mpaa_rating IS NOT NULL
GROUP BY dm.mpaa_rating, dm.movie_title, CAST(dm.release_date AS DATE)
ORDER BY SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) DESC
--
-- So, General Audiences - G with "Snow White and the Seven Dwarfs" as the hihgest by Gross Income

--
-- Lisginf all the moving in descending order by their inflation adjusted gross
-- % change between toatl gross and inflation adjusted gross
-- Also, create a running total of all for all movies
--
WITH TotalInflationAdjustedGross AS
(
	SELECT SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS TotalInflationAdjustedGross
	FROM disney_movies_total_gross dm
),
MovieGross AS
(
	SELECT dm.movie_title AS MovieTitle
		   ,CAST(dm.release_date AS DATE) AS ReleaseDate
		   ,SUM(CAST(dm.total_gross AS NUMERIC)) AS TotalMovieGross
		   ,SUM(CAST(dm.inflation_adjusted_gross AS NUMERIC)) AS MovieGross
	FROM disney_movies_total_gross dm
	GROUP BY dm.movie_title, CAST(dm.release_date AS DATE)
)
SELECT mg.MovieTitle AS "Movie Title"
	   ,mg.ReleaseDate AS "Release Date"
	   ,mg.MovieGross AS "Movie Gross"
	   ,SUM(mg.MovieGross) OVER (ORDER BY mg.MovieGross DESC) AS "Cumulative Adjusted Gross"
	   ,SUM(mg.MovieGross) OVER (ORDER BY mg.MovieGross DESC) / (SELECT TotalInflationAdjustedGross FROM TotalInflationAdjustedGross )  AS "Cumulative % Gross"
FROM MovieGross mg
ORDER BY mg.MovieGross DESC

-- Disney earbed more from the Top 7 Movies which are 20%  out of the 100%
-- inflation adjusted gross released from 1937 - 2013

# Conclusion
From the above findings, we would notice "Snow White and the Seven Dwarfs" is the highest movie by Inflation Adjusted Gross which is 7.6% of the entire gross income earned by Dinsey and it happens to be very first movie released. It must have been a hit. Most with the high gross incomes are rated G and are mainly Adventures. Lastly, Disney earned more income from the Top 7 Movies which are 20%  out of the 100% inflation adjusted gross of movies released from 1937 - 2013.
