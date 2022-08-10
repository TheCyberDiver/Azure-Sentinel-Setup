# Azure-Sentinel-Setup

#########################################
#              Azure Sentinel           #
#                  Set-up               #
#########################################

Disclaimer: This Set-up requires basic knowledge of creating and configuring Virtual Machines, Virtual Networks, Resource groups, and Virtual Machines.


1.0. Creating an Windows 10 endpoint virtual machine.

    1.1. Start by setting up a standard Windows 10 virtual machine. I suggest making a new resource group.

    1.2. I will be naming mine honeypot-endpoint for a demonstration at the end. NOTE: I suggest making strong credentials!

    1.3. In later steps we will open the firewall on the honeypot-endpoint to allow ANY:ANY

2.0. Creating a Log Analytics Workspace and setting up data flow

    2.1. In the Azure Portal navigate to Log Analytics Workspace.

    2.2. Hit the create button and put it in your resource group and name it.

    2.3. Now to allow the logs to go from your VM to the Logs Analytics workspace we will need to head over to Microsoft Defender for Cloud in azure portal

    2.4. Once you are in Microsoft Defender for cloud scroll to Enviornment Settings. Now expand the tree until you see your Log Analytics workspace group you created!

    2.5. Click into Defender Plans and Turn on Microsoft Defender for Servers but leave everything else as off and save in the top left.

    2.6. Now under Defender plans click Data Collection > and choose All Events and save again.

    2.7. In your Azure Portal navigate back to your logs analytics workspace and lets add the VM!

    2.8. Once in your Workspace scroll until you see "virtual machine" and click it. You will be shown ALL of your VMs. Select your VM and then press "connect"

3.0. Setting up Azure Sentinel (SIEM)

    3.1. In your Azure Portal navigate to Microsoft Sentinel. Once in the Sentinel page click Create.

    3.2. Microsoft offers a 31 day free trial and choose your log analytics workspace. BOOM... You now should be able to use up to 10Gb of data per day for 31 days!

4.0. FIREWALL AND NETWORK RULES DISCLAIMER: THIS WILL OPEN YOUR VM UP TO THE PUBLIC INTERNET.

    4.1. Now you need to make an ANY:ANY firewall rule on the Windows VM. Do so by looking up wf.msc and disabling it. 

    4.2. Now we need to make an ANY ANY rule on the Azure portal for the VM. Add an inbound rule of ANY:ANY

    4.3. So once you have your network rule in Azure created and the Firewall in the VM turned off. You should be able to successfully ping your VM from anywhere

5.0. This portion is optional, however it is for real world viewing and creating a map.

    5.1. Within this Github repo I will add a powershell script 

    5.2. Download your FREE API key from ipgeolocation.io and insert that in the script and run the powershell script

    5.3. Now we will navigate back to Log Analytics work space >Custom logs

    5.4. Go back to the VM and locate the log file in ProgramData and copy the premade logs

    5.5. Notice: Right away my machine was being bruteforced from Netherlands

    5.6 Create a Notepad of the logs and upload that into the custom logs in Log analytics work space

    5.7. In the collection path put C:\ProgramData\failed_rdp.log as that is where the logs are in the VM and name it note: you will use the name to query it

    5.8. Now navigate to Logs in the Log Analytics and try some basic Queries while we wait

  Examples: Security Event | Where EventID==4625  <-- 4625 is the event viewers failed logon
  
6.0. Making the data ingest

    6.1. Run your query for me it was FAILED_RDP_WITH_IP_GEOLOCATION_CL and expand one of the logs right click and select Extract Fields from ""

    6.2. It will show all the fields in the Raw data and we need to extract them

    6.3. Highlight the VALUES of one fields and name it and then save. These will save in "Custom logs > Custom Fields" incase you need to delete an extracted word.

    6.4. Repeat that process for each value.

    6.5. Now go back to Azure Sentinel and make a new workbook. Go to "Workbooks > Add new Workbook > Add new query"

    6.6. Add a query within the workbook and paste this query
      FAILED_RDP_WITH_IP_GEOLOCATION_CL | summarize event_count=count() by sourcehost_CF, latitude_CF, longitude_CF, country_CF, label_CF, destinationhost_CF
        | where destinationhost_CF != "samplehost"
        | where destinationhost_CF != ""

  and change the visualization to map. Make sure you change the query name to what you named it. Now you will need to add the fields in there. 
  I chose to use the country instead of longitude and latitude!
  
  ANOTHER EDIT: YOU NEED to play around with your Map settings to get the numbers and bubbles to display right!!!!!




 Snippit of Map
 
 ![image](https://user-images.githubusercontent.com/77608692/183999885-8dde071a-a041-49d6-8966-58e840ca39bd.png)


  

  
  
  
  
