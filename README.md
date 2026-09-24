# Formula 1 Data Engineering Pipeline

#### A Databricks-based Formula 1 data engineering project that transforms raw Formula 1 data into analytics-ready Delta tables using a Medallion Architecture.

#### **Architecture**

<img width="1448" height="1086" alt="9e02fa7c-5396-41c6-8883-a3f4778214ca" src="https://github.com/user-attachments/assets/47bff3b8-911a-4dca-881f-229dddeb82f2" />

- **Landing** — Raw Formula 1 CSV files.
- **Bronze** — Raw data ingested into Delta tables with ingestion metadata.
- **Silver** — Cleaned, standardized, typed, and deduplicated datasets.
- **Gold** — Business-ready dimensions, facts, and reference data for analytics.

#### Tech Stack

- Databricks
- Apache Spark / PySpark
- Spark SQL
- Delta Lake
- Unity Catalog
- Azure Data Lake Storage Gen2

#### Repository Structure
```
Formula-1-DBP/
│
├── 00-common/
│ ├── 01.environment-config.ipynb
│ └── 02.bronze-helpers.ipynb
│
├── 01-setup/
│ └── 01. Setup Project Environment.ipynb
│
├── 02-Bronze/
│ ├── 01. Ingest Circuits File.ipynb
│ ├── 02. Ingest Races File.ipynb
│ ├── 03. Ingest Constructors File.ipynb
│ ├── 04. Ingest Drivers File.ipynb
│ ├── 05. Ingest Results File.ipynb
│ └── 06. Ingest Sprints File.ipynb
│
├── 03-Silver/
│ ├── 01. Transform Circuits File.ipynb
│ ├── 02. Transform Races File.ipynb
│ ├── 03. Transform Constructors File.ipynb
│ ├── 04. Transform Drivers File.ipynb
│ ├── 05. Transform Results File.ipynb
│ └── 06. Transform Sprints File.ipynb
│
└── 04-Gold/
├── 01.Build Races Dimension.ipynb
├── 02.Build Constructors Dimension.ipynb
├── 03.Build Drivers Dimension.ipynb
├── 04.Build Results Fact.ipynb
└── 91.Build Nationality Region Reference.ipynb
```

#### Environment Configuration

The project uses the following Unity Catalog objects:
```
Object|    Value
Catalog|  "formula1"
Schemas|   "landing", "bronze", "silver", "gold"
Landing Volume| "/Volumes/formula1/landing/files"
```
The common environment notebook centralizes configuration values used throughout the pipeline.

**Bronze Layer**

    The Bronze layer ingests raw Formula 1 CSV files into Delta tables.
    
    **Source Datasets**
    
    - Circuits
    - Races
    - Constructors
    - Drivers
    - Results
    - Sprints
    
    During ingestion, metadata is added to the datasets, including:
    
    - "injestion_timestamp"
    - "source_file"
    
    This provides basic data lineage and helps identify when and from which source file records were ingested.

**Silver Layer**

    The Silver layer prepares Bronze data for analytical use.
    
    Transformations include:
    
    - Data type standardization
    - Column cleanup and normalization
    - Data quality transformations
    - Deduplication
    - Preparation of consistent datasets for downstream modeling

    The Silver layer acts as the clean and standardized foundation for the Gold data model.

**Gold Layer**

    The Gold layer contains analytics-ready tables following a dimensional modeling approach.
    
    Table| Description
    "formula1.gold.dim_races"| Race dimension enriched with circuit information
    "formula1.gold.dim_constructors"| Constructor dimension enriched with nationality and region
    "formula1.gold.dim_drivers"| Driver dimension enriched with nationality and region
    "formula1.gold.fact_session_results"| Race and sprint session results
    "formula1.gold.ref_nationality_region"| Nationality-to-region reference mapping

    **Race Dimension**
    
        "dim_races" combines race information with circuit information to provide a consolidated race dimension.
    
    **Constructor Dimension**
    
        "dim_constructors" enriches constructor information with regional information using the nationality reference table.
    
    **Driver Dimension**
    
        "dim_drivers" enriches driver information with regional information using the nationality reference table.
    
    **Session Results Fact**
    
        "fact_session_results" combines:
        
        - Race results
        - Sprint results
        
        The fact table identifies the type of session using:
        
        RACE
        SPRINT
        
        It also derives analytical indicators such as:
        
        - "is_win"
        - "is_podium"
        - "has_points"
    
    **Nationality Reference**
    
        "ref_nationality_region" contains a manually curated mapping between nationality and broader geographical region.
    
        This reference data is used to enrich driver and constructor dimensions.

#### **Data Model**

<img width="1448" height="1086" alt="929aa7d1-d9a0-4d08-853b-0e59e3aa0835" src="https://github.com/user-attachments/assets/3b51c995-a422-484f-b84a-8e7cdb4d6f3d" />

**Getting Started**

    1. Set up the Databricks environment
    
    Run:
    
    01-setup/01. Setup Project Environment.ipynb
    
        This creates the required Unity Catalog objects and landing volume.
    
    2. Load the source data
    
        Place the required Formula 1 CSV files in:
    
            /Volumes/formula1/landing/files
    
    3. Run the Bronze notebooks
    
        Execute the notebooks in:
    
            02-Bronze/
    
        This ingests the raw source files into Delta tables.
    
    4. Run the Silver notebooks
    
        Execute the corresponding transformation notebooks in:
    
             03-Silver/
    
        This cleans and standardizes the Bronze datasets.
    
    5. Build the Gold layer
    
        Execute the notebooks in:
    
              04-Gold/
    
        Build the nationality-region reference table before the dependent driver and constructor dimensions.
    
  **Design Principles**
  
      Medallion Architecture
      
          The pipeline separates data processing into Landing, Bronze, Silver, and Gold layers.
      
      Delta Lake
      
          Delta tables are used as the storage layer for structured data throughout the pipeline.
      
      Unity Catalog
      
          Unity Catalog provides centralized organization and governance for catalogs, schemas, and volumes.
      
      Reusable Components
      
          Common configuration and ingestion metadata logic are centralized in the "00-common" directory.
      
      Dimensional Modeling
      
          The Gold layer separates descriptive dimensions from the session-results fact table, making the data suitable for analytical workloads.

#### **Future Improvements**

**Potential extensions include**:

    - Automated pipeline orchestration
    - Additional Formula 1 datasets
    - Automated data quality checks
    - Incremental data processing
    - More reference datasets
    - Historical performance analysis
    - Power BI or other BI-tool integration
    - Automated testing and validation
    - CI/CD for Databricks notebooks

**Project Status**

    This repository contains a Databricks notebook-based Formula 1 data engineering pipeline built around a Medallion Architecture.
    
    The pipeline can be extended with additional datasets, orchestration, data quality checks, and downstream BI/analytics workloads.

---

Built with Databricks, Apache Spark, PySpark, Delta Lake, and Unity Catalog.
