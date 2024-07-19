/*
Covid 19 Data Exploration 

Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types

*/

SELECT * 
FROM Covid_Deaths
WHERE continent is not NULL
order by 3,4 

 -- Data columns chosen to start with---

SELECT location,date, total_cases, new_cases, total_deaths, population
FROM Covid_Deaths 
WHERE continent is not NULL
order by 1,2

--Total cases vs Total Deaths
--This query shows the likelihood of an individual dying from Covid in specified country; chosen country Ireland---

SELECT location, date, total_cases,total_deaths,(total_deaths/total_cases) *100 AS DeathPercentage
FROM Covid_Deaths
WHERE location like '%Ireland%'
and continent is not NULL
order by 1,2

-- -Looking at Total cases vs Population--
--Shows what percentange of population got Covid

SELECT location, date, population, total_cases,(total_cases/population) *100 AS PercentagePopulationInfected
FROM Covid_Deaths
WHERE location like '%Ireland%'
and continent is not NULL
order by 1,2

--Countries with highest infection rate compared to population----

SELECT location,population, max(total_cases)AS HighestInfectionCount, max((total_cases/population)) *100 AS PercentagePopulationInfected
FROM Covid_Deaths
WHERE continent is not NULL
--WHERE location like '%Ireland%'
Group by location, population
order by PercentagePopulationInfected DESC

--Countries with the Highest Death Count per population

SELECT location, max(cast(total_deaths as INT))as TotalDeathCount
FROM Covid_Deaths
WHERE continent is not NULL
--WHERE location like '%Ireland%'
Group by location
order by TotalDeathCount DESC

-- BREAKING THINGS DOWN BY CONTINENT

-- Showing contintents with the highest death count per population

Select continent, MAX(cast(Total_deaths as int)) as TotalDeathCount
FROM Covid_Deaths
--Where location like '%Ireland%'
Where continent is not null 
Group by continent
order by TotalDeathCount desc

-- GLOBAL NUMBERS

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
FROM Covid_Deaths
--Where location like '%states%'
where continent is not null 
--Group By date
order by 1,2


--To view all the data fields in Covid vaccination data--
SELECT *
FROM Covid_Vaccinations


-- Total Population vs Vaccinations
-- Shows Percentage of Population that has recieved at least one Covid Vaccine


SELECT dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations

FROM Covid_Deaths dea

JOIN Covid_Vaccinations vac

     ON dea.location = vac.location

	 and dea.date = vac.date

where dea.continent is not null 

	 order by 2,3

---Calculation on Partition By in previous query---

SELECT dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations

, sum(Cast(vac.new_vaccinations as bigint))OVER(PARTITION by dea.location ORDER BY dea.location,

dea.date) as RollingPeopleVaccindated

, (

FROM Covid_Deaths dea

JOIN Covid_Vaccinations vac

     ON dea.location = vac.location

	 and dea.date = vac.date

where dea.continent is not null 

	 order by 2,3	 

	 
--- Creating a CTE ---

	 

WITH PopvsVac(continent, location, date, population, new_vaccinations, RollingPeopleVaccinated) AS

(	

    SELECT 

        dea.continent,

        dea.location, 

        dea.date, 

        dea.population, 

        vac.new_vaccinations,

        SUM(CAST(vac.new_vaccinations AS BIGINT)) OVER (

            PARTITION BY dea.location 

            ORDER BY dea.location, dea.date

        ) AS RollingPeopleVaccinated

    FROM 

        Covid_Deaths dea

    JOIN 

        Covid_Vaccinations vac

    ON 

        dea.location = vac.location AND dea.date = vac.date

    WHERE 

        dea.continent IS NOT NULL 

)

--  query that calls the CTE---

SELECT * , (RollingPeopleVaccinated/Population) * 100

FROM PopvsVac



-- Using Temp Table to perform Calculation on Partition By in previous query


-- Drop the temporary table if it exists--

DROP TABLE IF EXISTS PercentPopulationVaccinated;


-- Create the temporary table to perform calculation on Partition By in previous query--

CREATE TEMP TABLE PercentPopulationVaccinated

(

    Continent NVARCHAR(255),

    Location NVARCHAR(255),

    Date DATETIME,

    Population NUMERIC,

    New_vaccinations NUMERIC,

    RollingPeopleVaccinated NUMERIC

);


-- Insert data into the temporary table--

INSERT INTO PercentPopulationVaccinated

SELECT 

    dea.Continent, 

    dea.Location, 

    dea.Date, 

    dea.Population, 

    vac.New_vaccinations,

    SUM(CAST(vac.New_vaccinations AS INT)) OVER (

        PARTITION BY dea.Location 

        ORDER BY dea.Location, dea.Date

    ) AS RollingPeopleVaccinated

FROM 

    Covid_Deaths dea

JOIN 

    Covid_Vaccinations vac

    ON dea.Location = vac.Location AND dea.Date = vac.Date;



-- Select data and calculate the percentage of the population vaccinated

SELECT 

    *,

    (RollingPeopleVaccinated / Population) * 100 AS PercentPopulationVaccinated

FROM 

    PercentPopulationVaccinated;


-- Creating View to store data for Tableau visualizations---


Create View PercentPopulationVaccinated as

Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations

, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated

--, (RollingPeopleVaccinated/population)*100

From Covid_Deaths dea

Join Covid_Vaccinations vac

	On dea.location = vac.location

	and dea.date = vac.date

where dea.continent is not null 

SELECT *

FROM PercentPopulationVaccinated
