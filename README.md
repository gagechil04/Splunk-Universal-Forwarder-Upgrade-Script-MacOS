# Splunk-Universal-Forwarder-Install-Script-MacOS

*Script Note* - Using && after each command in the script ensures that the subsequent command will only run if the previous one succeeded (i.e., returned exit status 0).

### Creating Splunk User and Group - Sometimes this can break the script, so manually input the below commands before running the script. (Tested on MacOS 13.1)<br /><br />
````
sudo dscl . -create /Users/splunk
````
````
sudo dscl . -create /Users/splunk UserShell /bin/bash"
````
````
sudo dscl . -create /Users/username RealName “Splunk ServiceAccount"
````
The number after “UniqueID” must be an ID number that is not assigned to another user
````
sudo dscl . -create /Users/splunk UniqueID 1001
````

<br />

````
sudo dscl . -create /Users/username PrimaryGroupID 1000
````
````
sudo dscl . -create /Users/username NFSHomeDirectory /Local/Users/username"
````
````
sudo dscl . -passwd /Users/username password" Replace “password” with the desired password that the new user will use when logging in to the computer.
````

<br />

You can give the new 'splunk' user administrative privileges with the command below (Required for Splunk UF).
````
sudo dscl . -append /Groups/admin GroupMembership splunk
````

<br />

````
sudo dscl . create /Groups/splunk
````
````
sudo dscl . create /Groups/servsupport RealName "Splunk ServiceAccount"
````
````
sudo dscl . create /Groups/splunk passwd "*"
````
For the next command use an unused groupID number as gid, you can list them out via the below command:
````
dscl . list /Groups PrimaryGroupID | tr -s ' ' | sort -n -t ' ' -k2,2
````

<br />


````
sudo dscl . create /Groups/splunk gid 799
````
````
sudo dscl . create /Groups/splunk GroupMembership admin
````
