1. The oldest business in the world

![400px-Eingang_zum_St _Peter_Stiftskeller](https://user-images.githubusercontent.com/84812549/134993262-039ba3e0-9c42-45cb-ad2a-d461bf1b847f.jpg)

Image: St. Peter Stiftskeller, founded 803. Credit: Pakeha.

An important part of business is planning for the future and ensuring that the company survives changing market conditions. Some businesses do this really well and last for hundreds of years.

BusinessFinancing.co.uk researched the oldest company that is still in business in (almost) every country and compiled the results into a dataset. In this project, you'll explore that dataset to see what they found.

The database contains three tables.

categories
column	type	meaning
category_code	varchar	Code for the category of the business.
category	varchar	Description of the business category.
countries
column	type	meaning
country_code	varchar	ISO 3166-1 3-letter country code.
country	varchar	Name of the country.
continent	varchar	Name of the continent that the country exists in.
businesses
column	type	meaning
business	varchar	Name of the business.
year_founded	int	Year the business was founded.
category_code	varchar	Code for the category of the business.
country_code	char	ISO 3166-1 3-letter country code.
Let's begin by looking at the range of the founding years throughout the world.

%%sql 
postgresql:///oldestbusinesses
 
-- Select the oldest and newest founding years from the businesses table
select min(year_founded), max(year_founded)
from businesses
order by 1
1 rows affected.
min	max
578	1999
2. How many businesses were founded before 1000?
Wow! That's a lot of variation between countries. In one country, the oldest business was only founded in 1999. By contrast, the oldest business in the world was founded back in 578. That's pretty incredible that a business has survived for more than a millennium.

I wonder how many other businesses there are like that.

%%sql
postgresql:///oldestbusinesses
​
-- Get the count of rows in businesses where the founding year was before 1000
select count(year_founded) from businesses
where year_founded<1000
1 rows affected.
count
6
3. Which businesses were founded before 1000?
Having a count is all very well, but I'd like more detail. Which businesses have been around for more than a millennium?

%%sql
postgresql:///oldestbusinesses
    select * from businesses 
    where year_founded < 1000
    order by year_founded 
​
6 rows affected.
business	year_founded	category_code	country_code
Kongō Gumi	578	CAT6	JPN
St. Peter Stifts Kulinarium	803	CAT4	AUT
Staffelter Hof Winery	862	CAT9	DEU
Monnaie de Paris 	864	CAT12	FRA
The Royal Mint	886	CAT12	GBR
Sean's Bar	900	CAT4	IRL
4. Exploring the categories
Now we know that the oldest, continuously operating company in the world is called Kongō Gumi. But was does that company do? The category codes in the businesses table aren't very helpful: the descriptions of the categories are stored in the categories table.

This is a common problem: for data storage, it's better to keep different types of data in different tables, but for analysis, you want all the data in one place. To solve this, you'll have to join the two tables together.

%%sql
postgresql:///oldestbusinesses
    select business, year_founded, country_code, category
    from businesses b
    inner join categories c
    on b.category_code=c.category_code
    where year_founded<1000
    order by year_founded
6 rows affected.
business	year_founded	country_code	category
Kongō Gumi	578	JPN	Construction
St. Peter Stifts Kulinarium	803	AUT	Cafés, Restaurants & Bars
Staffelter Hof Winery	862	DEU	Distillers, Vintners, & Breweries
Monnaie de Paris 	864	FRA	Manufacturing & Production
The Royal Mint	886	GBR	Manufacturing & Production
Sean's Bar	900	IRL	Cafés, Restaurants & Bars
5. Counting the categories
With that extra detail about the oldest businesses, we can see that Kongō Gumi is a construction company. In that list of six businesses, we also see a café, a winery, and a bar. The two companies recorded as "Manufacturing and Production" are both mints. That is, they produce currency.

I'm curious as to what other industries constitute the oldest companies around the world, and which industries are most common.

%%sql
postgresql:///oldestbusinesses
-- Select the category and count of category (as "n")
-- arranged by descending count, limited to 10 most common categories
select category , count(category) as n 
from categories c
join businesses b
on c.category_code=b.category_code
group by c.category_code
order by n desc
limit 10
10 rows affected.
category	n
Banking & Finance	37
Distillers, Vintners, & Breweries	22
Aviation & Transport	19
Postal Service	16
Manufacturing & Production	15
Media	7
Cafés, Restaurants & Bars	6
Food & Beverages	6
Agriculture	6
Tourism & Hotels	4

