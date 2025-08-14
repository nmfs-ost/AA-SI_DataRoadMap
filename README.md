# AA-SI Data Road Map
The AA-SI is developing a data pipeline to store, process, and analyze data, and generate products for fisheries management. By necessity, this pipeline will be in the AA-SI's storage and computing environment  with GCP. One of the goals of this pipeline is to automate as much as we can so that we can effectively and efficiently address the growing data volume that we record and store.  

Our data road map is based on echoPype's data processing levels <a href="https://echolevels.readthedocs.io/en/latest/levels_proposed.html"> "echoPype processing levels"</a>, where each level represents a step from "raw" data in manufacturer-specified file formats to gridded data that are ready for input to advanced analytical models, such as, machine learning (ML), artificial intelligence (AI), Bayesian inverse (APES), and other advanced statistical models. Active-acoustic data (echosounder, SONAR, multibeam) are our primary data set, but we include supplemental data, such as oceanographic, biological, and geological data that characterize the environment, as well as metadata for all data streams.

For the AA-SI, we define the levels as:  
- **Level 0**
    - Input: raw data file in manufacturer-specified format located in the cloud or on-premise,
    - Harvest survey-level metadata (who, what, when, where, why, and how) for the selected data,
    - Determine the echosounder manufacturer - this is the first step towards determining the file format,
    - Determine the acquistion hardware and software used to record the data - this determines the file format,
    - Harvest file-level metadata (CW or FM mode, number of channels, ...),
    - Output: survey-level and file-level metadata.
- **Level 1**
    - Input: Level 0 data - raw data file, survey-level, and file-level metadata,
    - Apply quality assurance (QA)/quality control (QC) criteria,
    - Harvest all ancillary data (e.g., motion, GPS, sound speed, attenuation, ...) recorded within the level 0 raw data file,
    - Determine whether supplemental data are required (e.g., missing GPS), and if so, assemble those data,
    - Reformat manufacturer-specified-format active-acoustic data to open-format (<a href="https://htmlpreview.github.io/?https://github.com/ices-publications/SONAR-netCDF4/blob/master/Formatted_docs/crr341.html"> "ICES SONAR-netCDF4"</a>) format,
    - Reformat supplemental data to open-format, 
    - Collate validated calibration data and metadata for supplemental data,
    - Output: Open-format, SONAR-netCDF4 for active-acoustic data, data with supplemental data and metadata to be used for processing the active-acoustic data.
- **Level 2**
    - Input: Level 1 data - files in open data formats,
    - Apply missing ancillary data (e.g., missing GPS),
    - Apply motion, sound-speed, and attenuation corrections,
    - Apply validated calibration data,
    - Apply noise-reduction (impulse, transient, background noise) algorithms,
    - Apply noise-reduction lines and regions - e.g., bubble exclusion, seabed echo exclusion, instrument exclusion (e.g., CTD echo),
    - Format active-acoustic data to echoPype, <a href="https://xarray.dev/"> "xArray"</a> based, format, 
    - Output: Calibration-verified, noise-reduced active-acoustic data in echoPype format at native resolution
- **Level 3**
    - Input: Level 2 data,
    - Grid the active-acoustic data at the selected spatial and/or temporal grid resolution,
    - Provide validated data at the equivalent grid resolution,
    - Output: Data ready for ingest to advanced AI/ML and analytical models
- **Level 4**
    - TBD - AI/ML models

## Level 0 Data
```mermaid
flowchart TB
    subgraph SG_DataSource["**Raw Data Source**"]
        direction TB
        node_SrcOMAO@{shape: lean-r, label: "OMAO Data Lake"} --> node_OMAO@{ shape: tag-doc, label: "Retrieve Data from OMAO: link to instructions" }
        node_SrcNCEI@{shape: lean-r, label: "NCEI"} --> node_NCEI@{ shape: tag-doc, label: "Retrieve Data from NCEI: link to instructions" }
        node_SrcGCP@{shape: lean-r, label: "GCP Prod Storage"} --> node_GCP@{ shape: tag-doc, label: "Retrieve Data from GCP: link to instructions" }
        node_SrcOP@{shape: lean-r, label: "On-Prem Storage"} --> node_OP@{ shape: tag-doc, label: "Retrieve Data from On-prem: link to instructions" }
    end
    subgraph SG_SurveyMetaData["**Survey Metadata**"]
        direction LR
        node_RF@{ shape: rounded, label: "Raw File" } --> node_SMD@{ shape: database, label: <a href="https://github.com/nmfs-ost/AA-SI_metadata"> "Survey Metadata"</a> }
    end
    subgraph SG_ESManufacturer["**Echosounder Manufacturer**"]
        direction LR
        node_AL1@{shape: tag-doc, label: "AA Library Function: link to instructions"} --> node_Man@{ shape: diamond, label: "Select Manufacturer" }
        node_AL2@{shape: tag-doc, label: "EchoPype Function: link to instructions"} --> node_Man
    end
    subgraph SG_ESMetaData["**Data File Format \& Metadata**"]
        direction TB
        node_KS@{ shape: rounded, label: "Kongsberg-Simrad" } --> node_KSMD@{ shape: tag-doc, label: "link to Kongsberg-Simrad data file format and metadata" }
        node_BS@{ shape: rounded, label: "BioSonics" } --> node_BSMD@{ shape: tag-doc, label: "link to BioSonics data file format and metadata" }
        node_ASL@{ shape: rounded, label: "ASL" } --> node_ASLMD@{ shape: tag-doc, label: "link to ASL data file format and metadata" }
    end
    subgraph SG_L0Data["**Level 0 Data**"]
        direction TB
        node_L0RD@{ shape: rounded, label: "Raw File" }
        node_L0SMD@{ shape: database, label: "Survey Metadata" }
        node_RFMD@{ shape: database, label: "File-level Metadata" }
    end
    SG_DataSource --> SG_SurveyMetaData
    SG_SurveyMetaData --> SG_ESManufacturer
    SG_ESManufacturer --> SG_ESMetaData
    SG_ESMetaData --> SG_L0Data
```

Level 0 data are survey-level and file-level metadata and the raw data file.

# Level 1 Data
```mermaid
flowchart TB
    subgraph SG_L0Data["**Level 0 Data**"]
        direction TB
        node_L0RF@{ shape: rounded, label: "Raw File" }
        node_L0SMD@{ shape: database, label: "Survey Metadata" }
        node_RFMD@{ shape: database, label: "File-level Metadata" }
    end
    subgraph SG_AcceptFile["**QA/QC Accepted**"]
        direction LR
        node_L0RF1@{ shape: rounded, label: "Raw File" } --> node_RFAD@{ shape: database, label: "Raw File Ancillary Data" }
    end
SG_L0Data --> node_QAQC@{ shape: rounded, label: "Apply QA/QC algorithms" }
node_QAQC --> node_AcceptReject@{ shape: diamond, label: "Accept or Reject File" }
node_AcceptReject --> |Accept| SG_AcceptFile
node_AcceptReject --> |Reject| node_Reject@{ shape: rounded, label: "Reject file flowchart: ??" }


```
