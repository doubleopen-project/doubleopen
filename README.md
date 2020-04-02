# Double Open project

Double Open project is working to create a proof of concept of open source compliance with open tools and open data for use in embedded Linux. In particular, the proof of concept focuses on Yocto. However, the architecture is aimed to be technology independent so that the process of proof of concept could be applied to any development environment.

## The process

The process is divided in five steps:

### 1. Identify source files

First step is to identify the source files that have been built in to the distributable image and get their hash values. We're currently working on this for Yocto in [our fork of Yocto](https://github.com/doubleopen-project/yocto). We're utilizing [a script to retrieve the information from the build](https://github.com/doubleopen-project/yocto-hash-list) after it is finished.

### 2. Get license conclusions

Next step is to get license conclusions for the source files from Fossology. This should be done by querying Fossology's REST API, which currently doesn't support querying for license conclusions by hash value. We're [working on implementing this feature](https://github.com/doubleopen-project/fossology) to Fossology.

### 3. Machine readable OS policy

Next step is to provide a machine readable OS policy. We're currently [working on the format for the policy](https://github.com/doubleopen-project/policy-engine/blob/master/Policy_Engine.md).

### 4. Process Files against OS Policy

Next step is to compare the output of steps 1 and 2 against the OS policy and trigger errors/warnings.

### 5. Create license notices

The last step is to create license notices based on the previous steps.
