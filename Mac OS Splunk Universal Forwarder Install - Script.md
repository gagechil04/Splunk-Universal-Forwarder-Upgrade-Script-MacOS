 Note - Using && after each command ensures that the subsequent command will only run if the previous one succeeded (i.e., returned exit status 0). However, it's not strictly necessary, especially if you want the script to continue executing regardless of the success or failure of the previous command. If you remove && after each command, the script will execute each command sequentially, regardless of the success or failure of the preceding command. Here's the script without &&:

### Creating Splunk User and Group - Sometimes this can break the script, so manually input the below commands before running the script.
## "sudo dscl . -create /Users/splunk"
## "sudo dscl . -create /Users/splunk UserShell /bin/bash"
## sudo dscl . -create /Users/username RealName “Splunk ServiceAccount"
## Type “sudo dscl . -create /Users/splunk UniqueID 1001” and press “Enter.” The number after “UniqueID” must be an ID number that is not assigned to another user.
## sudo dscl . -create /Users/username PrimaryGroupID 1000
## "sudo dscl . -create /Users/username NFSHomeDirectory /Local/Users/username"
## "sudo dscl . -passwd /Users/username password" Replace “password” with the desired password that the new user will use when logging in to the computer.
## Type “sudo dscl . -append /Groups/admin GroupMembership splunk” and press “Enter” to give the new user administrative privileges. To make the new account a limited user account, skip this step.
## sudo dscl . create /Groups/splunk
## sudo dscl . create /Groups/servsupport RealName "Splunk ServiceAccount"
## sudo dscl . create /Groups/splunk passwd "*"
## For the next command use an unused groupID number as gid, you can list them out via the below command:
## dscl . list /Groups PrimaryGroupID | tr -s ' ' | sort -n -t ' ' -k2,2
## sudo dscl . create /Groups/splunk gid 799
## sudo dscl . create /Groups/splunk GroupMembership admin



## Downloading the Splunk Universal Forwarder Software
cd /tmp &&
curl -o /tmp/splunkforwarder-9.2.0.1-darwin-64.tgz "https://download.splunk.com/products/universalforwarder/releases/9.2.0.1/osx/splunkforwarder-9.2.0.1-d8ae995bf219-darwin-64.tgz" &&
sleep 30

## Assign Splunk permissions - Note your filename in the curl command
chown -R splunk:splunk /tmp/splunkforwarder-9.2.0.1-darwin-64.tgz &&
mkdir /Applications/splunkforwarder &&

#Extracting splunk
sudo tar -zxvf /tmp/splunkforwarder-9.2.0.1-darwin-64.tgz -C /Applications/ &&
sleep 25

#Assign Splunk permissions
chown -R splunk:splunk /Applications/splunkforwarder &&

#Install Splunk
/Applications/splunkforwarder/bin/splunk start --accept-license --answer-yes --no-prompt --SPLUNKUSERNAME=splunk --gen-and-print-passwd &&
sleep 40

#Configure the Splunk universal forwarder - Create deploymentclient.conf
touch /Applications/splunkforwarder/etc/system/local/deploymentclient.conf &&

#Echo the configurations into deploymentclient.conf
echo "[target-broker:deploymentServer]" >> /Applications/splunkforwarder/etc/system/local/deploymentclient.conf &&
echo "targetUri = <DS IP ADDRESS>:8089" >> /Applications/splunkforwarder/etc/system/local/deploymentclient.conf &&

#Stop the UF service to enable boot start
/Applications/splunkforwarder/bin/splunk stop &&
sleep 30

#Enable boot start
/Applications/splunkforwarder/bin/splunk enable boot-start &&
sleep 10

#Start Splunk
/Applications/splunkforwarder/bin/splunk restart &&
sleep 30
