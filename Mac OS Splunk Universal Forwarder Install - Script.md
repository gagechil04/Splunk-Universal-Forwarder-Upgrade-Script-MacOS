#!/bin/bash

## Creating Splunk User and Group - Sometimes this can break the script, so manually input the below commands before running the script.
## Enter Commands Here

## Downloading the Splunk Universal Forwarder Software
cd /tmp &&
wget -O splunkforwarder-9.2.0.1-d8ae995bf219-darwin-64.tgz "https://download.splunk.com/products/universalforwarder/releases/9.2.0.1/osx/splunkforwarder-9.2.0.1-d8ae995bf219-darwin-64.tgz" &&
sleep 30

## Assign Splunk permissions
chown -R splunk:splunk /tmp/splunkforwarder-9.2.0.1-d8ae995bf219-darwin-64.tgz &&
mkdir /Applications/splunkforwarder &&

#Extracting splunk
sudo tar -zxvf /tmp/splunkforwarder-9.2.0.1-d8ae995bf219-darwin-64.tgz -C /Applications/ &&
sleep 25

#Assign Splunk permissions
chown -R splunk:splunk /Applications/splunkforwarder &&

#Install Splunk
/Applications/splunkforwarder/bin/splunk start --accept-license --answer-yes --no-promt --SPLUNKUSERNAME=splunk --gen-and-print-passwd &&
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
