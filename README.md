# UC4-Automic-AE-CLI-Binary-Repository

**Repository of all CLI Binaries (all compiled CLI projects)**

---
## Get the list of latest features added:

Consult the wiki page here for a list of recently added features:
https://github.com/brendanSapience/UC4-Automic-AE-CLI-Binary-Repository/wiki

---
## Getting Help and Examples for each command:

Consult the wiki page here for examples and help on specific commands:
https://github.com/brendanSapience/UC4-Automic-AE-CLI-Binary-Repository/wiki

---

## Improvement / Feature Requests:

Feel free to open "issues" (even if they arent issues) on this page to request improvements & features:
https://github.com/brendanSapience/UC4-Automic-AE-CLI-Binary-Repository/issues

---

## FAQ (Frequently Asked Questions):

### What parameter shows me the Connection Options i have?
> the '-help' option will display the Connection parameters to specify Login, Pwd, Dept, Client etc.:

> java -jar XXXX_XXXX.jar -help

### Can I use this on an active-active Automation Engine?
> Yes. The -X and -N options can be used to handle this natively. You can copy your existing uc4config.xml file where the CLI binaries are and use the option -N to specify which Connection Name to use (from the xml file). You can specify an alternate xml file using -X option.


> java -jar XXXX_XXXX.jar -help


### What parameter shows me the options i have for a particular utility?
> the '-h' option will display all parameters specific to the binary you are using:

> java -jar XXXX_XXXX.jar -h

### What are the '-f*' parameters for?

> All the parameters that start with '-f*' are used to filter out objects for processing with other parameters. '-f*' parameters tend to accept full Regular Expressions.

### What is the difference between the parameter -name and the parameter -f_name?

> The parameter -name only accept 'UC4 RegEx', meaning either full names or expressions with '*' and '?' but NOT full Regular Expressions.

> The parameter -f_name always accepts full Regular Expressions. 

### Why is there 2 different parameters to filter on Object Names (-name and -f_name)?

> For Performance. -name searches are done first on the backend, and they are much faster than any other search mechanism (but can only search on limited patterns). -name is used to make a fast preselection of Objects which are then looked at for further filtering by the -f_name option (or any combination of -f option).

> In small environments, you can use the -f_name option, but in larger environments the performance will be significantly better if using a combination of both.

### What is the commit parameter for?

> For Safety. Without it, update commands will only run a simulation of updates. Only of the commit parameter is specified will the update actually occur. This allows to test 'en-masse' updates before actually modifying anything.

### I dont want to pass the Password in the command in clear text. What can i do?

> You have several options. You can encrypt the password with the Automic utility (UCYBCRYPT.exe) and pass the encrypted password with option -W (just like you would do with a clear text password.)

> You can also pass the password in memory using an environment variable (AE_PWD)

> You can also specify the password in an encrypted form in the connection.config file

### Does this CLI support Single SignOn? 

> Yes. SSO needs to be configured on your system obviously. The corresponding option to use is '--sso', in which case you dont need to specify Dept, Login or Password anywhere.


### The command I'm using keep telling me the format of my parameter is wrong.. what do I do?

> Take a close look at the help of the binary you are using ('-h' option). Each option should explicitly require a specific format.

> some require "['Some RegEx','a string']" while some others are simpler and require only "[some value,some other value]"

> if you are using the strict format but are still getting an error, contact me please!


---
## Purpose:

provide a repository of **Command Line Interface Binaries** in order to extend the capabilities that the UI currently offers.

## Design:

**Each binary takes at least 2 possible flags/options:**
* **-help**: displays the list of AE Connection Parameters that can be specified (if overriding the parameters contained in connection.config file)
* **-h**:    displays the list of parameters (Mandatory, Optional etc.) required specifically for each binary

## Connection Parameters:

**Each binary needs to establish a connection to the Automation Engine. The connection parameters can be set either:**
* directly in the **connection.config** file
* in any other config file of your choice
* via CLI parameters (see **-help** option for list of available parameters: **-L login -W password -C client -D department -H hostname -P port -F filename ** etc.)
* Note: IF a parameter is specified both in connection.config and via a CLI parameter, the CLI parameter takes precedence. 
* Note: the Password can be passed in encrypted form both in the config file and thru the command line (see usage of ucybcryp.exe to encrypt your password)

## CONFIGURATION Files:

* **connection.config**: contains connection parameters. This file is used by all binaries. All the parameters specified in it can be overriden when using any of the binaries. use -help flag to review override parameters. Alternatively, you can create several config files and use the -F flag to use a specific one.
	
## Safety:

Each binary is designed to be SAFE and comes with a **mandatory COMMIT mechanism**: it will NOT modify / update / delete / create / execute anything **UNLESS the -commit flag is added as a parameter.**
## examples: 
* OBJECTS_Delete.jar -name "*" => will NOT delete any objects, but instead will run a simulation of Objects to be deleted
* OBJECTS_Delete.jar -name "*" -commit => will delete ALL objects or ALL types (you should probably never use this command as-is ;)).
* TASKS_Operations.jar -f_name ".*" -f_status ".*aborted.*" -u_restart "*" => will NOT restart anything, but instead will run a simulation of Tasks to be restarted
* TASKS_Operations.jar -f_name ".*" -f_status ".*aborted.*" -u_restart "*" -commit => will restart ALL Tasks marked Aborted in the Activities Window	

## Common Active Parameters: 

**Most binaries have at least 2 sets of parameters available:**
* **-f_**: Filter Parameters, parameters used to filter only specific objects (ex: -f_type "JOBS" or -f_title ".*[Mm]y [Tt]itle.*") => MOST f_* parameters support Regular Expressions
* **-u_**: Update Parameters, parapeters used to update various things (ex: -u_process ["192.168.1.123","192.168.1.987"], or -u_title [".+","My New Title"], or -u_priority 100) => most parameters requiring ["A","B"] as a format will accept Regular Expressions for "A". "B" is the substitute String.  

## Other information: 

* **All Binaries leverage the Java Automic API Simplified Framework. Source Code can be found here:** https://github.com/brendanSapience/UC4-Automic---Java-API-Framework-Simplified
	
* **All Binaries are self contained, all source code is available (see below), no guarantee is provided.**

## Compatibility:

	* Requires Java v1.7 minimum.
	* All Binaries compiled with most current version (v12.1) uc4.jar files (meaning it handles new objects like STORAGE, PERIOD, XML VARIABLE, AI EVENT, etc.)
	* All Binaries (unless indicated) are compatible with AE v9 and up, a version check is automatically performed before each command for safety.
	* It is strongly advised to test thoroughly in NON-PROD environments.

## List of Binaries (non exhaustive):

	**## OBJECT Specific CLI Binaries:**

		* CALE_Update.jar: Update / import CALE Objects from JSON files. 
		* AGENTS_Management.jar: Manage Agents from Client 0 (assign to other clients, delete, etc.) (ex: allow all clients to use Agent WIN01 for Execution)
		* HOSTGROUPS_Management.jar: Manage Hostgroups (add agents, run simulations, remove agents, etc.)
		* CHANGES_Show.jar: 	Display content of Audit Trail with filters (needs to be activated in Client 0 first)	
		* CONN_Update.jar:	Update CONN Objects (including RA CONN such as SOAP or REST CONNs)
		* JOBS_RA_Update.jar:	Update RA Jobs (SOAP, REST, FTP, etc.)
		* JSCH_Update.jar:	Update SCHED Objects
		* LOGIN_Update.jar:	Update LOGIN Objects
		* STORAGE_Update.jar:	Update STORAGE Objects (only v11.2 and up)
		* TASKS_Operations.jar: Handle Operations on Activities Window (Deactivate all "Completed" tasks, restart aborted JOBS tasks with title matching ".*DEV.*")
		* USER_Update.jar:	Update USER Objects
		* VARA_SQL_Update.jar:	Update SQL VARA Objects
		* VARA_SQLI_Update.jar:	Update SQLI VARA Objects
		* VARA_STATIC_Update.jar: Update STATIC & XML VARA Objects (XML VARA is a subtype of STATIC)
		* VARA_Update.jar:	Update General Properties of VARA Objects
		* JOBF_Update.jar:      Update JOBF Objects. (ex: change the pattern for Destination File in all JOBF objects at once)
		* JOBP_Update.jar:      Update JOBP Objects. (ex: update all task names for all Workflows at once)
		* JOBS_SQL_Update.jar:  Update JOBS_SQL Objects (ex: modify the Database Connection name in all JOBS_SQL containing "*SQL1*" in their name)
		* JOBS_Update.jar:      Update JOBS Objects (ex: modify the name of a variable in all JOBS containing "*ABC*" in their title)

	**## GENERAL CLI Binaries:**

		* OBJECTS_Delete.jar: Delete Objects of any type. (with regular expressions...)
		* OBJECTS_Create.jar: Create Objects of any type. (ex: create 200 JOBS Objects)
		* OBJECTS_Duplicate.jar: Duplicate Objects of any type. (ex: duplicate all objects matching name "*AB*PROD*" to "*AB*DEV*" at once)
		* OBJECTS_Export_Import.jar: Import & Export Objects and dependencies (v11.2 and up)
		* OBJECTS_Execute.jar: Execute now or schedule later (executable) Objects. (ex: run now all JOBP matching name "*DEV*JOBP*")
		* OBJECTS_Move.jar: Move Objects of any type anywhere (within the same client) (ex: move all objects matching "*PROD*ABC*" to Folder "SANDBOX")
		* OBJECTS_Rename.jar: Rename Objects of any type (ex: rename objects with title matching ".*Legacy.*" from "*ALPHA*" to "*BETA*")
		* UTIL_ClientAudit.jar: Extract an Audit Report on Specific Client Usage (ex: number of active objects defined, etc.) 
		* UTIL_EnvironmentAudit.jar: Extract Environment Wide Usage Report (ex: number of clients defined, etc.)
		* UTIL_SystemOverview_Show.jar: Extract Content of the System Overview into a parsable format (JSON)
		
		

