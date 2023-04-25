# covid19_sql_data_exploration
( Personal Project practice)

Skills used in this project: Joins, CTE's, Temp Tables, Aggregate Functions, Creating Views, Converting Data Types


# I wanted to make sure that the continent does not come up as null

select*
from `profoliodatabase.coviddeaths`
where continent is not null 
order by 3,4


selectlocation,date,total_cases_per_million,new_cases,total_deaths,population
from `profolioprojectdatabase.profoliodatabase.coviddeaths`
order by 1,2

#l Then I calculated the at total cases VS total deaths| Which showed the likelihood of dying if a person contracted Covid in their Area

SELECT location,date,total_cases_per_million,total_deaths_per_million,(total_cases_per_million/total_deaths_per_million)*100 as DeathPercentage
from `profoliodatabase.coviddeaths`
order by 1,2

#Now we are looking at the total cases VS population | which showed us what % of population had covid

SELECT location,date,total_cases_per_million,population,(total_cases_per_million/population)*100 as DeathPercentage
from `profoliodatabase.coviddeaths`
order by 1,2

#what country has the highest infection rate|

SELECT location,population, Max (total_cases_per_million) as highestinfectedcount MAX (total_cases_per_million/population)*100
from `profoliodatabase.coviddeaths`
group by population,location
order by 1.2

#countries with highest death count

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From `profoliodatabase.coviddeaths`
Group by Location, Population
order by PercentPopulationInfected desc

#countries with the highest deathcount per population

Select location, MAX(total_deaths)as totaldeathcount
From `profoliodatabase.coviddeaths`
where continent is not null
Group by location
order by TotalDeathCount desc


#broken down by continent 

Select continent, MAX(total_deaths)as totaldeathcount
From `profoliodatabase.coviddeaths`
where continent is not null
Group by continent
order by TotalDeathCount desc

Total Population Vs Vaccination
# I first wanted to make sure the tables were joined correctly
select *
from `profoliodatabase.coviddeaths` dea
JOIN `profoliodatabase.covidvaccination` vac
ON  dea.location = vac.location
and dea.date = vac.date

# Now im looking at the total population Vs vaccination ( Total amount of people in the world that has been vaccinated) { dea=death/vac= vaccination}

select dea.continent ,dea.location ,dea.date, population, vac.new_vaccinations
from `profoliodatabase.coviddeaths` dea
JOIN `profoliodatabase.covidvaccination` vac
ON  dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 1,2,3

# Shows Percentage of Population that has recieved at least one Covid Vaccine

select dea.continent ,dea.location ,dea.date, population, vac.new_vaccinations
, SUM(vac.new_vaccinations) OVER (Partition by dea.Location Order by dea.location, dea.Date) as PeopleVaccinated
from `profoliodatabase.coviddeaths` dea
JOIN `profoliodatabase.covidvaccination` vac
ON  dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3

-- Using Temp Table to perform Calculation on Partition By in previous query
)

Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as PeopleVaccinated
--, (PeopleVaccinated/population)*100
From `profoliodatabase.coviddeaths`  dea
Join `profoliodatabase.covidvaccination` vac
	On dea.location = vac.location
	and dea.date = vac.date
--where dea.continent is not null 
--order by 2,3


# Creatng view to store data later, for Viz
will be looking at the 

Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From `profoliodatabase.coviddeaths` dea
Join `profoliodatabase.covidvaccination` vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
