# COVID Deaths and Infection Rate in Countries (Analytics Using BigQuery)

## QUESTIONS
1. What country has the highest infection rate?
2. Which country has the highest death count? 
3. Which continent has the highest death count?
4. What is the global death rate? 
5. What is the global vaccination rate?
6. What country has the highest vaccination rate?

## COMPUTED QUERIES AND ANALYSIS


### 1. What country has the highest infection rate?
#### I used MAX to find country that has highest infection rate (relative to the population). The country with highest infection rate is Peru with 64.1%

SELECT Location, population, MAX(total_cases) AS highest_infection_count,Max((total_deaths/population))*100 AS percent_population_infected

FROM PortfolioProject.covid_deaths

GROUP BY location, population

ORDER BY percent_population_infected DESC

### 2. Which country has the highest death count? 
#### By grouping countries to calculate their total death counts, I found out the United States has the highest death counts among other countries. 

SELECT Location, MAX(cast(total_deaths as int)) AS highest_death_count

FROM PortfolioProject.covid_deaths

WHERE continent is not null

GROUP BY location

ORDER BY highest_death_count DESC

### 3. Which continent has the highest death count? 
#### By grouping continents to calculate their total death counts, I found out North America has the highest death counts among other countries with 1,095,235 deaths. 

SELECT continent, MAX(cast(total_deaths as int)) AS highest_death_count

FROM PortfolioProject.covid_deaths

WHERE continent is not null

GROUP BY continent

ORDER BY highest_death_count DESC

### 4. What is the global death rate?
#### By dividing total new deaths by total new cases, the death rate is approximately 1%

SELECT SUM(new_cases) AS total_global_cases, SUM(new_deaths) AS total_global_deaths, SUM(new_deaths)/SUM(new_cases)*100 AS global_death_rate

FROM PortfolioProject.covid_deaths

WHERE continent is not null

### 5. What is the global vaccination rate?
#### By dividing total new deaths by total new cases, the death rate is approximately 1%-- Look at Global Vaccinations Rate
SELECT SUM(deaths.population), SUM(new_vaccinations), SUM(new_vaccinations)/SUM(deaths.population)*100  

FROM PortfolioProject.covid_deaths AS deaths

JOIN PortfolioProject.covid_vaccinations AS vacc

  ON deaths.location = vacc.location
  
  AND deaths.date = vacc.date

### 6. What country has the highest vaccination rate?
#### Since the data is provided by date, which means we need the data of rolling people vaccinated to avoid double counting. Therefore, I use Partition By location and date to calculate the number of people vaccinated overtime. China has the highest number of vaccinations. 

SELECT deaths.continent, deaths.location, deaths.date, deaths.population, vacc.new_vaccinations, 

SUM(vacc.new_vaccinations) OVER (PARTITION BY deaths.location ORDER BY deaths.location, deaths.date) AS RollingPeopleVaccinated,

FROM PortfolioProject.covid_deaths AS deaths

JOIN PortfolioProject.covid_vaccinations AS vacc

  ON deaths.location = vacc.location
  
  AND deaths.date = vacc.date
  
WHERE deaths.continent IS NOT NULL

ORDER BY 2,3


