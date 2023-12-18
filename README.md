This is a project that seamlessly integrates SQL data extraction and Tableau visualization to provide an overview of the global landscape during and after COVID-19 period. I used SQL to extract tables, capturing important metrics such as total COVID-19 cases, deaths, death percentages, and the infection percentages showed the impact on total population health. 

The extracted data were then transformed into dashboards using Tableau, which enabled exploration of infection and mortality trends across continents, alongside a detailed analysis of daily progression and stringency indices. This project not only showcases technical proficiency in SQL and Tableau but also demonstrates a keen analytical approach to understanding and visualizing complex global health data.

Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types

Below are the SQL codes I used to extract the tables:
----------------------------------------------------------------------------------------------------------------------------------------
1. Percentage of Population Vaccinated

              select dea.location,
              		dea.date,
              		dea.population,
              		sum (cast (vac.new_vaccinations as float)) as [Vaccinations Count],
              		cast ((max(cast (vac.total_vaccinations as decimal))/dea.population)*100 as decimal (10,2))  as [% Pop. Vaccinated]
              from [Covid Deaths 2023] dea
              join [Covid Vaccination 2023] vac
              	on dea.location = vac.location
              	and dea.date = vac.date
              where dea.continent is not null and vac.continent is not null
              group by dea.location, dea.population, dea.date
              order by 1,2 asc
   2. 

