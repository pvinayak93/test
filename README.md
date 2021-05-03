# APC Module

`apc_module` is a utility for working with APC datasets at SEPTA. The module operates on stop level datasets from the two vendors - Infodev and UTA, and pulls in data outputs from the `gtfs_module` and `traffic_check_module`. 

The `apc_module` is built using two main Python packages `pandas` and `gtfs-kit`. Additional packages such as `openpyxl` and `fastparquet` (compatiable with `pandas`) are used for I/O operations.    

There are four main functions included in the module - 

* [process_apc_infodev](###process_apc_infodev)
* [process_apc_uta](###process_apc_uta)
* [expansion_apc_tc](###expansion_apc_tc)
* [generate_derived_dataset](###generate_derived_dataset)

The intent is to run each of these functions sequentially, after the `gtfs_module` (more information here) and `traffic_check` modules (more information here). **APC** and **traffic check** datasets provide measures of ridership on the sampled trips, which is a subset of the total scheduled trips. The universe of scheduled trips are sourced from the [GTFS rosters](###).  

The end product of this process are ridership datasets (**derived datasets**) that can be queried to obtain ridership by route, block id, direction, stop, time of day and day of week for a specific month/signup. Currently, the processing is done at the sign-up level i.e. there is one **derived dataset** file per sign-up.  

 As of 4/30/2021, all these functions are configured to work with parameters defined in the `run_parameters.py` - parameters include input/output folder paths, lookups, and definitions used in imputation algorithms. The module also references auxiliary functions that can be found in python files prefixed with *auxFuncs*.         

## Overall Process

The flowchart shows sequence of steps involved in generating **derived datasets** and linkages between the `apc_module`, `traffic_check_module`, and `gtfs_module`. The flow controlled within the `apc_module` is shown within the gray box.

* **APC Processing** block comprises of the *process_apc_infodev* and *process_apc_uta* functions
* **Expansion** block is contained in the *expansion_apc_tc* function
* **Derived Dataset** and **Gap Filling** blocks are contained in the *generate_derived_dataset* function

**Diagnostic summaries** can be generated for several functions. These serve as breakpoints in the data flow and allow the users to review high level statistics to flag and troubleshoot data issues at the source. 

All along the process flow intermediate files are generated (saved as highly efficient [parquet](https://fastparquet.readthedocs.io/en/latest/) files) that can be forked to support other analysis. As a result, the process generates the folder structure described in the [Folders and Files](##Folders-and-Files).  


![FlowChart](https://github.com/septadev/ridership-data-warehouse/blob/3d44294f46f646b2792fc5779f1a6ef85fb0e698/reference_files/documentation/FlowChart_sprint1.jpg) 


## Function Descriptions


### process_apc_infodev

This function operates on the stop level data files from Infodev vendor, pulled using the FTP site. These files follow the nomenclature *Stop-by-Stop.csv* and there exists one file per signup. Data dictionary for the available columns is housed [here](#addlink). The function implements the following operations: 

* Map APC route IDs to GTFS route IDs using the `MasterRouteList_v3_withGTFS` lookup table maintained by SEPTA
* Merge the APC dataset with `GTFS trip roster`, which generates the list of scheduled trips for every day of the year using the latest applicable GTFS release (see [gtfs_module]() for more details.) This match is based on the following columns : `date, route_id, block_id, direction, and scheduled_start_time`.
* Generate diagnostic summaries

```python
from apc_module.process_apc_infodev import process_apc_infodev

# Paths to data and output folder are specified in run_parameters.py
# Signup specified as a string "signupname_year", where 
# SEPTA defined signup name (e.g. spring, summer) and year as YYYY.  
signup = 'fall_2018'
process_apc_infodev(signup, diagnostics = True)

```
### process_apc_uta

**Infodev APC** data structure is assumed to supersede the **UTA APC** data structure, and hence the latter is 

## Folders and Files






### ControlScript.py



#### Instructions
This script serves as the control script to sequentially run the apc and gtfs modules. Replaces the deprecated 'RunSummaries.py'. Configurations for the run are specified in 'RunParameters.py'. 

Refer to docstrings for details on each function.

Sequence of modules to be run:
* **GTFS Modules**
	* *get_latest_git_versions* : Latest version of GTFS packages on SEPTA public repository.
	* *download_gtfs_versions* : Download zipped files. Skips versions already in the folder.
	* *get_trip_roster* : Generate daily trip roster using the latest available GTFS version for each date.
	* <<>> : Generates shell for the derived dataset (described below).

* APC modules
	* *process_infodev* : Process infodev dataset and map to GTFS trip records. See diagnostics file for summaries.
	* *process_UTA_APC* : Process UTA dataset and restructure to mirror infodev datasets. Map to GTFS trip records. See diagnostics file for summaries.
	* *expansionAllAPC* : Read both APC datasets, generate expansion matrix, populate with sample information, and save combined APC dataset with expansion factors. 
	* *generateDerivedDataset* : Generates stop-level ridership dataset populated with expanded APC data. Gaps are filled using heuristics described below.  


#### Terminologies





Definition of a trip -- rip is definied as a combination of these variables









These **derived datasets**  
* Start with GTFS stop-level roster, which lists every trip in GTFS (see definition of [trip](###)) with the sequence of stops   








### processInfodev
### generateDerivedDataset