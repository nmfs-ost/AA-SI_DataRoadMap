# AA-SI Data Road Map
The AA-SI is developing a data pipeline to store, process, and analyze data, and generate products for fisheries management. By necessity, this pipeline will be in the AA-SI's storage and computing environment  with GCP. One of the goals of this pipeline is to automate as much as we can so that we can effectively and efficiently address the growing data volume that we record and store.  

Our data road map is based on echoPype's data processing levels <a href="https://echolevels.readthedocs.io/en/latest/levels_proposed.html"> "echoPype processing levels"</a>, where each level represents a step from "raw" data in manufacturer-specified file formats to gridded data that are ready for input to advanced analytical models, such as, machine learning (ML), artificial intelligence (AI), Bayesian inverse (APES), and other advanced statistical models. Active-acoustic data (e.g., echosounder, SONAR, multibeam) are our primary data set, but we include supplemental data, such as oceanographic, biological, and geological data that characterize the environment, as well as metadata for all data streams.

For active acoustic data, we define the levels and processes within those levels as:  
- **Level 0**  
    - **Input:** raw data file in manufacturer-specified format located in the cloud or on-premise</br>
    - **Processes:** </br>
      - Harvest survey-level metadata (who, what, when, where, why, and how) for the selected data,</br>
      - Determine the echosounder manufacturer,</br>
      - Determine the acquistion hardware and software used to record the data,</br>
      - Harvest file-level metadata (e.g., number of channels, ...).</br>
      - Harvest ping-level metadata (e.g., CW or FM, active or passive, ...)</br>
    - **Output:** </br>
      - survey-level metadata</br>
      - file-level metadata</br>
      - ping-level metadata</br></br>
- **Level 1**</br>
    - **Input:** Data from Level 0</br>
      - raw data file</br>
      - survey-level metadata</br>
      - file-level metadata</br>
      - ping-level metadata</br>
    - **Level 1A**</br>
      - **Processes**:</br>
        - Determine whether sufficient GPS data are recorded in the raw data file,</br>
        - Harvest supplemental data (e.g., motion, GPS, sound speed, attenuation, ...) recorded within the level 0 raw data file,</br>
    - **Level 1B**</br>
      - **Processes**:</br>
        - Apply quality assurance (QA)/quality control (QC) criteria,</br>
          - Merge supplemental data if needed (e.g., GPS),</br>
          - Apply time-coordinate corrections,</br>
          - Apply motion correction,</br>
          - Other QA/QC?</br>
        - Reformat manufacturer-specified-format active-acoustic data to "Echopype" and/or <a href="https://htmlpreview.github.io/?https://github.com/ices-publications/SONAR-netCDF4/blob/master/Formatted_docs/crr341.html"> "ICES SONAR-netCDF4"</a> open formats,</br>
    - **Output:** Data files in open-source formats</br>
      - The default is Echopype format, which we use as input to L2 and higher. </br>
      - Strict sonarNET-CDF4 format for coordination with other national and international groups.</br>
      - Supplemental data and metadata to be used for processing the active-acoustic data. </br></br>
- **Level 2** </br>
    - **Input:** </br>
      - Level 1B data - files in Echopype format (volume and point-backscatter in (<a href="https://docs.xarray.dev/en/stable/"> "Xarray"</a>) format), </br>
      - Supplemental data and metadata </br>
      - Calibration data and metadata </br>
    - **Level 2A** </br>
      - **Processes:** </br>
        - Apply validated calibration data, </br>
    - **Level 2B** </br>
      - **Processes:** </br>
        - Apply noise-reduction (impulse, transient, background noise) algorithms, </br>
        - Apply noise-reduction lines and regions - e.g., bubble exclusion, seabed echo exclusion, instrument exclusion (e.g., CTD echo), </br>
    - **Output:** </br>
      - Calibration-verified, noise-reduced active-acoustic data in echoPype (<a href="https://docs.xarray.dev/en/stable/"> "Xarray"</a>) format) at native resolution </br></br>
- **Level 3** </br>
  - **Input:** </br>
    - Level 2B data: calibrated, noise-reduced data</br>
  - **Level 3A** </br>
    - **Processes:** </br>
      - Grid the data at the selected spatial and/or temporal grid resolution, </br>
      - Provide validated data at the equivalent grid resolution, </br>
  - **Level 3B** </br>
    - **Processes:** </br>
      - Apply QA/QC criteria </br>  
  - **Output:** </br>
    - Data ready for ingest to advanced AI/ML and analytical models </br></br>
- **Level 4**
    - TBD - AI/ML models

## Level 0 Data
```mermaid
---
config:
    flowchart:
        subGraphTitleMargin:
            "bottom": 30
---
flowchart TB
    subgraph SG_DataSource["**Raw Data Source**"]
        direction TB
        node_SrcOMAO@{shape: lean-r, label: "OMAO Data Lake"} --> node_OMAO@{ shape: tag-doc, label: "Retrieve Data from OMAO: link to instructions" }
        node_SrcNCEI@{shape: lean-r, label: "NCEI"} --> node_NCEI@{ shape: tag-doc, label: "Retrieve Data from NCEI: link to instructions" }
        node_SrcGCP@{shape: lean-r, label: "GCP Prod Storage"} --> node_GCP@{ shape: tag-doc, label: "Retrieve Data from GCP: link to instructions" }
        node_SrcOP@{shape: lean-r, label: "On-Prem Storage"} --> node_OP@{ shape: tag-doc, label: "Retrieve Data from On-prem: link to instructions" }
        %%style node_SrcOMAO color:blue
    end
    subgraph SG_SurveyMetaData["**Survey Metadata**"]
        direction LR
        node_RF@{ shape: rounded, label: "Raw File" } --> node_SMD@{ shape: database, label: <a href="https://github.com/nmfs-ost/AA-SI_metadata"> "Survey Metadata"</a> }
    end
    subgraph SG_ESManufacturer["**Echosounder Manufacturer**"]
        direction TB
        node_AL1@{shape: tag-doc, label: "AA Library Function: link to instructions"} --> node_Man@{ shape: lean-r, label: "Manufacturer" }
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
    SG_ESMetaData --> node_AcceptReject@{ shape: diamond, label: "Accept or Reject File" }
    node_AcceptReject --> |Accept| SG_L0Data
    node_AcceptReject --> |Reject| node_Reject@{ shape: rounded, label: "Reject file flowchart: ??" }
```

Level 0 data are survey-level and file-level metadata and the raw data files.

## Level 1 Data
```mermaid
flowchart TB
    subgraph SG_L0Data["**Level 0 Data**"]
        direction TB
        node_L0RF@{ shape: rounded, label: "Raw File" }
        node_L0SMD@{ shape: database, label: "Survey Metadata" }
        node_RFMD@{ shape: database, label: "File-level Metadata" }
    end
    subgraph SG_AcceptFile["**QA/QC Accepted**"]
        direction TB
        node_L0RF1@{ shape: rounded, label: "Raw File" }
        node_RFSD@{ shape: database, label: "Supplemental Data" }
        node_EPF@{ shape: rounded, label: "Echopype File" }
        node_L0RF1 --> node_EPF
        node_RFSD --> node_EPF
    end
SG_L0Data --> node_QAQC@{ shape: rounded, label: "Apply QA/QC algorithms<br/>&bull; link to required supplemental data<br/>&bull; link to time correction<br/>&bull; link to missing data check<br/>&bull; etc..." }
style node_QAQC text-align:left
node_QAQC --> node_AcceptReject
node_AcceptReject --> |Accept| SG_AcceptFile
node_AcceptReject --> |Reject| node_Reject@{ shape: rounded, label: "Reject file flowchart: ??" }


```
Level 1 data are the Echopype netCDF4 data file, supplemental data files and metadata.
