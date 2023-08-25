covid19_sql_data_exploration project:

1. To ensure that the continent does not come up as null:
```sql
SELECT *
FROM profoliodatabase.coviddeaths
WHERE continent IS NOT NULL
ORDER BY 3, 4;
```

2. Extracting COVID-19 data for analysis:
```sql
SELECT location, date, total_cases_per_million, new_cases, total_deaths, population
FROM profolioprojectdatabase.profoliodatabase.coviddeaths
ORDER BY 1, 2;
```

3. Calculating the likelihood of dying if a person contracted COVID-19 in their area:
```sql
SELECT location, date, total_cases_per_million, total_deaths_per_million,
(total_cases_per_million / total_deaths_per_million) * 100 AS DeathPercentage
FROM profoliodatabase.coviddeaths
ORDER BY 1, 2;
```

4. Analyzing total cases versus population percentage:
```sql
SELECT location, date, total_cases_per_million, population,
(total_cases_per_million / population) * 100 AS DeathPercentage
FROM profoliodatabase.coviddeaths
ORDER BY 1, 2;
```

5. Identifying the country with the highest infection rate:
```sql
SELECT location, population, MAX(total_cases_per_million) AS highestinfectedcount,
MAX(total_cases_per_million / population) * 100
FROM profoliodatabase.coviddeaths
GROUP BY location, population
ORDER BY 1, 2;
```

6. Countries with the highest death count:
```sql
SELECT location, population, MAX(total_cases) AS HighestInfectionCount,
MAX((total_cases / population)) * 100 AS PercentPopulationInfected
FROM profoliodatabase.coviddeaths
GROUP BY location, population
ORDER BY PercentPopulationInfected DESC;
```

7. Countries with the highest death count per population:
```sql
SELECT location, MAX(total_deaths) AS totaldeathcount
FROM profoliodatabase.coviddeaths
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY TotalDeathCount DESC;
```

8. Death count breakdown by continent:
```sql
SELECT continent, MAX(total_deaths) AS totaldeathcount
FROM profoliodatabase.coviddeaths
WHERE continent IS NOT NULL
GROUP BY continent
ORDER BY TotalDeathCount DESC;
```

9. Joining COVID-19 deaths and vaccination data for analysis:
```sql
SELECT *
FROM profoliodatabase.coviddeaths dea
JOIN profoliodatabase.covidvaccination vac ON dea.location = vac.location AND dea.date = vac.date;
```

10. Analyzing total population versus new vaccinations:
```sql
SELECT dea.continent, dea.location, dea.date, population, vac.new_vaccinations
FROM profoliodatabase.coviddeaths dea
JOIN profoliodatabase.covidvaccination vac ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
ORDER BY 1, 2, 3;
```

11. Showing the percentage of the population that received at least one COVID-19 vaccine:
```sql
SELECT dea.continent, dea.location, dea.date, population, vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS PeopleVaccinated
FROM profoliodatabase.coviddeaths dea
JOIN profoliodatabase.covidvaccination vac ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
ORDER BY 2, 3;
```

12. Using a Temporary Table for calculations:
```sql
-- Assuming #PercentPopulationVaccinated Temporary Table exists
INSERT INTO #PercentPopulationVaccinated
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(CONVERT(int, vac.new_vaccinations)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS PeopleVaccinated
-- (PeopleVaccinated/population) * 100
FROM profoliodatabase.coviddeaths dea
JOIN profoliodatabase.covidvaccination vac ON dea.location = vac.location AND dea.date = vac.date
-- WHERE dea.continent IS NOT NULL
-- ORDER BY 2, 3;
```

13. Creating a view for storing calculated data:
```sql
CREATE VIEW PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(CONVERT(int, vac.new_vaccinations)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS RollingPeopleVaccinated
-- (RollingPeopleVaccinated/population) * 100
FROM profoliodatabase.coviddeaths dea
JOIN profoliodatabase.covidvaccination vac ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL;
```

These queries effectively illustrate the step-by-step data exploration and analysis performed in the "covid19_sql_data_exploration" project.