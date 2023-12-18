/* This is a project that integrates SQL data extraction and Tableau visualization to provide an overview of the global landscape during and after COVID-19 period. I used SQL to extract tables, capturing important metrics such as total COVID-19 cases, deaths, death percentages, and the infection percentages showed the impact on total population health. 

The extracted data were then transformed into dashboards using Tableau, which enabled exploration of infection and mortality trends across continents, alongside a detailed analysis of daily progression and stringency indices. This project not only showcases technical proficiency in SQL and Tableau but also demonstrates an analytical approach to understanding and visualizing complex global health data.
*/

Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types

Below are the SQL codes I used to extract the tables:
---------------------------------------------------------------------------------------------------------------------------------------
1. **World Count - Total Cases, Total Deaths, Death Percentage - Shows the total number of Infections all over the world, total deaths, and percentage of world population that died from the infection**

          select 
          	sum(cast (new_cases as float)) as [Total Cases],
          	sum(cast (new_deaths as float)) as [Total Deaths],
          	cast (sum(cast (new_deaths as float))/(sum(cast (new_cases as float)))*100 as decimal (10,2)) as [Death Percentage]
          from [Covid Deaths 2023]
          where continent is not null
   
2. **Count of Death Per Continent**

          Select
          	Location, 
          	sum(new_deaths) as [Total Deaths]
          from [Covid Deaths 2023]
          where continent is null 
          and location not in ('World', 'European union', 'High Income', 'Upper Middle Income', 'Lower Middle Income', 'Low Income')
          group by location
          order by 2 desc
   
3. **Country Population vs Total Infected - This shows the percentage of each country's population that was infected with the virus**

          select
          	Location, Population,
          	max(total_cases) as [Total Infected], 
          	cast ((max(cast (total_cases as decimal))/population)*100 as decimal (10,2)) as [%Pop. Infected] from [Covid Deaths 2023]
          where continent is not null and
          location not in ('World', 'European Union', 'International', 'High Income')
          group by location, population
          order by 4 desc
   
4. **Percentage of Population Infected + Daily Progression. - This shows the percentage of population infected and the daily rate of pregression over the period between when the data started being collected till date**

          select
          	Location, Population,date,
          	max(total_cases) as [Total Infected], 
          	cast ((max(cast (total_cases as decimal))/population)*100 as decimal (10,2)) as [%Pop. Infected] from [Covid Deaths 2023]
          where continent is not null and
          location not in ('World', 'European Union', 'International', 'High Income')
          group by location, population, date
          order by 4 desc
   
6. **Percentage of Population Vaccinated**

              select dea.location,
              		dea.population,
              		sum (cast (vac.new_vaccinations as float)) as [Vaccinations Count],
              		cast ((max(cast (vac.total_vaccinations as decimal))/dea.population)*100 as decimal (10,2))  as [% Pop. Vaccinated]
              from [Covid Deaths 2023] dea
              join [Covid Vaccination 2023] vac
              	on dea.location = vac.location
              where dea.continent is not null and vac.continent is not null
              group by dea.location, dea.population
              order by 1,2 asc
   
7. **Vaccination Daily Progression + Percentage of Population Vaccinated**

              select dea.location,
              		dea.population,
              		dea.date,
              		sum (cast (vac.new_vaccinations as float)) as [Vaccinations Count],
              		cast ((max(cast (vac.total_vaccinations as decimal))/dea.population)*100 as decimal (10,2))  as [% Pop. Vaccinated]
              from [Covid Deaths 2023] dea
              join [Covid Vaccination 2023] vac
              	on dea.location = vac.location
              	and dea.date = vac.date
              where dea.continent is not null and vac.continent is not null
              group by dea.location, dea.population, dea.date
              order by 1,2 asc
8. **Count of Infection with daily progression + Stringency Index**

/* In this query, we compare daily infection numbers with stringent index, the comparison helps us to know how effective lockdown measures for reducing the spread of the virus were. i.e Did infection count reduce over the period stringent index(lockdown measures) were high or increase when stringent index was low or lockdown measures were softened???

              select
              	dea.Location, dea.date,
              	max (dea.total_cases) as [Daily Infection Count],
              	--max(dea.total_deaths) as [Total Deaths],
              	--cast (max(cast (dea.total_deaths as float))/max( cast (dea.total_cases as float))*100 as float) as [% Death Cases],
              	max (stringency_index) as [Stringency Index]
              from [Covid Deaths 2023] dea
              join [Covid Vaccination 2023] vac
              	on dea.location = vac.location
              	and dea.date = vac.date
              where dea.continent is not null and vac.continent is not null
              group by dea.location, dea.date
              order by 1,2 desc

