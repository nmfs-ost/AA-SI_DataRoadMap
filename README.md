# AA-SI_DataRoadMap
Our data road map is based on echoPype's data processing levels <a href="https://echolevels.readthedocs.io/en/latest/levels_proposed.html"> "echoPype processing levels"</a>, where each level represents a step from "raw" data in manufacturer-specified file formats to gridded data that are ready for input to advanced analytical models, such as, machine learning (ML), artificial intelligence (AI), Bayesian inverse (APES), and other advanced statistical models. Active acoustic data (echosounder, SONAR, multibeam) are our primary data set, but we include supplemental data, such as oceanographic, biological, and geological data that describe the environment, as well as metadata for all data streams.

For the AA-SI, we define the levels as:  
- **Level 0**
    - Input: raw data file in manufacturer-specified format located in the cloud or on-premise,
    - Harvest survey-level metadata (who, what, when, where, why, and how) the data were collected,
    - Determine the echosounder manufacturer,
    - Determine the acquistion hardware and software used to record the data,
    - Harvest file-level metadata (CW or FM mode, number of channels, ...),
    - Output: survey-level and file-level metadata.
- **Level 1**
    - Input: Level 0 data
    - Harvest all ancillary data (e.g., motion, GPS, sound speed, attenuation, ...) contained within the level 0 file,
    - Determine whether supplemental data are required (e.g., missing GPS), and if so, assemble those data,
    - reformat manufacturer-specified-format data to open-format data (<a href="https://htmlpreview.github.io/?https://github.com/ices-publications/SONAR-netCDF4/blob/master/Formatted_docs/crr341.html"> "ICES SONAR-netCDF4" format),
    - Collate all calibration data and metadata,
    - Output: Open-format data with all supplemental information to be used in subsequent processing.
- **Level 2**
    - blah
- **Level 3**
    - blah

```mermaid
flowchart TB
    subgraph DataSource["**Raw Data Source**"]
        direction TB
        idSrcOMAO@{shape: stadium, label: "OMAO Data Lake"} --> idOMAO@{ shape: tag-doc, label: "Retrieve Data from OMAO: link to instructions" }
        idSrcNCEI@{shape: stadium, label: "NCEI"} --> idNCEI@{ shape: tag-doc, label: "Retrieve Data from NCEI: link to instructions" }
        idSrcGCP@{shape: stadium, label: "GCP Prod Storage"} --> idGCP@{ shape: tag-doc, label: "Retrieve Data from GCP: link to instructions" }
        idSrcOP@{shape: stadium, label: "On-Prem Storage"} --> idOP@{ shape: tag-doc, label: "Retrieve Data from On-prem: link to instructions" }
    end
    subgraph SurveyMetaData["**Survey Metadata**"]
        direction LR
        idRF@{ shape: stadium, label: "Raw File" } --> idSMD@{ shape: database, label: "link to survey metadata" }
    end
    subgraph ESManufacturer["**Echosounder Manufacturer**"]
        direction LR
        idAL1@{shape: tag-doc, label: "AA Library Function: link to instructions"} --> idMan@{ shape: diamond, label: "Select Manufacturer" }
        idAL2@{shape: tag-doc, label: "EchoPype Function: link to instructions"} --> idMan
    end
    subgraph ESMetaData["**Data File Format \& Metadata**"]
        direction TB
        idKS@{ shape: rounded, label: "Kongsberg-Simrad" } --> idKSMD@{ shape: tag-doc, label: "link to Kongsberg-Simrad data file format and metadata" }
        idBS@{ shape: rounded, label: "BioSonics" } --> idBSMD@{ shape: tag-doc, label: "link to BioSonics data file format and metadata" }
        idASL@{ shape: rounded, label: "ASL" } --> idASLMD@{ shape: tag-doc, label: "link to ASL data file format and metadata" }
    end
    DataSource --> SurveyMetaData
    SurveyMetaData --> ESManufacturer
    ESManufacturer --> ESMetaData
```

Level 0 data are survey-level and file-level metadata and the raw data file.
