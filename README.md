# ridership-data-warehouse

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