# Connect to Databricks

### Step 1: Create a Personal Access Token
Follow Databricks quick tutorial to generate a PAT that Artemis will use to gain access to your workspace.
https://docs.databricks.com/dev-tools/api/latest/authentication.html#token-management

### Step 2: Retrieve Cluster Connection Details

If you have a Cluster running, navigate to Advanced Options and select JBDC/OBDC.

Copy down the Server Hostname, Port and HTTP Path strings.

### Step 3: Complete Artemis Setup

Enter the required values into the form, and test connection.

Host - url to your workspace, ex: adb-3243212182xxxx.x.azuredatabricks.net 
SQL Path - path to your cluster, ex: sql/protocolv1/o/324321218256145/0822-190749-xxxxxxxx
Personal Access Token - ex: dapi9f2f0dbd05aef88xxxxxxxxxxxxx-x
