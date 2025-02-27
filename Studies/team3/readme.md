Comparison of the risk of hospitalization regarding the type of inhaler between long acting muscarinic antagonist (LAMA: tiotropium bromide) and inhaled corticosteroid (ICS: budesonide) among chronic obstructive lung disease (COPD)
==============================


Authors
============
IL dong, Lee <ildong.lee@iqvia.com>

Gang Jee, Ko <lovesba@korea.ac.kr>

Zepa, Yang <yangzepa@korea.ac.kr>

Study Protocols
============

**Objective**: Patients diagnosed as COPD have variable characteristics which have an impact on disease progression and severity. Specifically, airway responsiveness to steroid, which could be categorized as an asthmatic characteristic, is mixed in COPD which defined as permanent airway destruction. ICS is usually prescribed for the patients having asthmatic component. The goal of this protocol is conducting comparative effectiveness research to compare the risk of hospitalization between prescription of LAMA and ICS among patients with COPD

**Rationale**: This protocol is provided as an example of OHDSI study protocol

**Hypothesis**: Among patients with history of diagnosed COPD, people who were prescribed ICS first defined as having asthmatic component may have a different prognosis regarding the risk of hospitalization compared to patients prescribed LAMA first.

**Target cohorts**:
-	Patients who first prescribed LAMA (tiotropium bromide).
-	Those prescribed ICS within previous 30 days were excluded

**Comparative cohort**:
-	Patients who first prescribed ICS (budesonide).
-	Those prescribed LAMA within previous 30 days were excluded

**Cohort exit**
: Prescription of each comparative inhaler

**Outcome**:
: Hospitalization (inpatient visit occurrence)

**Statistical model**:
-	Cox regression analysis after 1:1 PS matching due to software limitation
Limitation
-	Budesonide and tiotropium bromide were used as representative drug for ICS and LAMA.  Other ICS and LAMA should be considered further.
-	Prescription of ICS doesn’t exactly mean those patients having asthmatic component
-	Two groups may have different disease severity.



Requirements
============

- A database in [Common Data Model version 5](https://github.com/OHDSI/CommonDataModel) in one of these platforms: SQL Server, Oracle, PostgreSQL, IBM Netezza, Apache Impala, Amazon RedShift, Google BigQuery, or Microsoft APS.
- R version 3.5.0 or newer
- On Windows: [RTools](http://cran.r-project.org/bin/windows/Rtools/)
- [Java](http://java.com)
- 25 GB of free disk space

See [these instructions](https://ohdsi.github.io/MethodsLibrary/rSetup.html) on how to set up the R environment on Windows.

How to run
==========
1. In `R`, use the following code to install the dependencies:

	```r
	install.packages("devtools")
	library(devtools)
	install_github("ohdsi/SqlRender", ref = "v1.6.0")
	install_github("ohdsi/DatabaseConnector", ref = "v2.3.0")
	install_github("ohdsi/OhdsiSharing", ref = "v0.1.3")
	install_github("ohdsi/FeatureExtraction", ref = "v2.2.3")
	install_github("ohdsi/CohortMethod", ref = "v3.0.2")
	install_github("ohdsi/EmpiricalCalibration", ref = "v2.0.0")
	install_github("ohdsi/MethodEvaluation", ref = "v1.0.2")
	```

	If you experience problems on Windows where rJava can't find Java, one solution may be to add `args = "--no-multiarch"` to each `install_github` call, for example:

	```r
	install_github("ohdsi/SqlRender", args = "--no-multiarch")
	```

	Alternatively, ensure that you have installed both 32-bit and 64-bit JDK versions, as mentioned in the [video tutorial](https://youtu.be/K9_0s2Rchbo).

2. In `R`, use the following `devtools` command to install the team3 package:

	```r
	install_github("ohdsi-korea/OhdsiDataThonKorea2019/Studies/team3")
	```

3. Once installed, you can execute the study by modifying and using the code below. For your convenience, this code is also provided under `extras/CodeToRun.R`:

	```r
	library(team3)

	# Optional: specify where the temporary files (used by the ff package) will be created:
	options(fftempdir = "c:/FFtemp")

	# Maximum number of cores to be used:
	maxCores <- parallel::detectCores()

	# Minimum cell count when exporting data:
	minCellCount <- 5

	# The folder where the study intermediate and result files will be written:
	outputFolder <- "c:/team3"

	# Details for connecting to the server:
	# See ?DatabaseConnector::createConnectionDetails for help
	connectionDetails <- DatabaseConnector::createConnectionDetails(dbms = "postgresql",
									server = "some.server.com/ohdsi",
									user = "joe",
									password = "secret")

	# The name of the database schema where the CDM data can be found:
	cdmDatabaseSchema <- "cdm_synpuf"

	# The name of the database schema and table where the study-specific cohorts will be instantiated:
	cohortDatabaseSchema <- "scratch.dbo"
	cohortTable <- "my_study_cohorts"

	# Some meta-information that will be used by the export function:
	databaseId <- "Synpuf"
	databaseName <- "Medicare Claims Synthetic Public Use Files (SynPUFs)"
	databaseDescription <- "Medicare Claims Synthetic Public Use Files (SynPUFs) were created to allow interested parties to gain familiarity using Medicare claims data while protecting beneficiary privacy. These files are intended to promote development of software and applications that utilize files in this format, train researchers on the use and complexities of Centers for Medicare and Medicaid Services (CMS) claims, and support safe data mining innovations. The SynPUFs were created by combining randomized information from multiple unique beneficiaries and changing variable values. This randomization and combining of beneficiary information ensures privacy of health information."

	# For Oracle: define a schema that can be used to emulate temp tables:
	oracleTempSchema <- NULL

	execute(connectionDetails = connectionDetails,
            cdmDatabaseSchema = cdmDatabaseSchema,
            cohortDatabaseSchema = cohortDatabaseSchema,
            cohortTable = cohortTable,
            oracleTempSchema = oracleTempSchema,
            outputFolder = outputFolder,
            databaseId = databaseId,
            databaseName = databaseName,
            databaseDescription = databaseDescription,
            createCohorts = TRUE,
            synthesizePositiveControls = TRUE,
            runAnalyses = TRUE,
            runDiagnostics = TRUE,
            packageResults = TRUE,
            maxCores = maxCores)
	```

4. Upload the file ```export/Results<DatabaseId>.zip``` in the output folder to the study coordinator:

	```r
	submitResults("export/Results<DatabaseId>.zip", key = "<key>", secret = "<secret>")
	```

	Where ```key``` and ```secret``` are the credentials provided to you personally by the study coordinator.

5. To view the results, use the Shiny app:

	```r
	prepareForEvidenceExplorer("Result<databaseId>.zip", "/shinyData")
	launchEvidenceExplorer("/shinyData", blind = TRUE)
	```

  Note that you can save plots from within the Shiny app. It is possible to view results from more than one database by applying `prepareForEvidenceExplorer` to the Results file from each database, and using the same data folder. Set `blind = FALSE` if you wish to be unblinded to the final results.

License
=======
The team3 package is licensed under Apache License 2.0

Development
===========
team3 was developed in ATLAS and R Studio.

### Development status

Unknown
