⚠️ __Please note that while the information and materials in this repository reflect the progress of the project, they may currently be in some respects OUTDATED.__

# Double Open project

Double Open project is working to create a proof of concept of open source compliance with open tools and open data for use in embedded Linux. In particular, the proof of concept focuses on Yocto. However, the architecture is aimed to be technology independent so that the process of proof of concept could be applied to any development environment.

## The process

The process is divided in five steps:

### 1. Identify source files

First step is to identify the source files that have been built in to the distributable image and get their hash values. We're using [our fork of Yocto](https://github.com/doubleopen-project/yocto) for this. The fork contains one patch to give us more data on the actual source fils going into build. Therafter we're utilizing [a script to retrieve the information from the build](https://github.com/doubleopen-project/yocto-hash-list) after it is finished.  

Example output from Yocto can be found from [01_Identify_source_files\yocto_output](01_Identify_source_files/yocto_output).

### 2. Get license conclusions

Next step is to get license conclusions for the source files from Fossology. This should be done by querying Fossology's REST API, which currently doesn't support querying for license conclusions by hash value. We're [working on implementing this feature](https://github.com/doubleopen-project/fossology) to Fossology.

### 3. Machine readable OS policy

Next step is to provide a machine readable OS policy.

### 4. Process Files against OS Policy

Next step is to compare the output of steps 1 and 2 against the OS policy and trigger errors/warnings.

### 5. Create license notices

The last step is to create license notices based on the previous steps.
