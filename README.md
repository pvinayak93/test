# ridership-data-warehouse

### ControlScript.py

#### Instructions
This script serves as the control script to sequentially run the apc and gtfs modules. Replaces the deprecated 'RunSummaries.py'. Configurations for the run are specified in 'RunParameters.py'.

Sequence of modules to be run:
* GTFS Modules
	* get_latest_git_versions : Latest version of GTFS packages on SEPTA public repository.
	* download_gtfs_versions : Download zipped files. Skips versions already in the folder.
	* get_trip_roster : Generate daily trip roster

* APC modules
	* process_infodev :
	* process_UTA_APC :
	* expansionAllAPC : 