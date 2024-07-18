# Connect to Redshift

###
### Step 1: Locate connection information 
### 

Locate the cluster you want to connect to, click on the "Properties" tab and copy the Endpoint field string.

This contains the host, port and database of your Redshift cluster.

###
### Step 2: Configure Redshift cluster
### 

From the cluster "Properties" tab, locate "VPC security group" in the "Network and Security" section and click on it. 

From within the "VPC security group", click on the "Inbound Rules" tab and click on "Edit inbound rules".

Add a new custom TCP rule with your Redshift port number, and the following IP addresses:
	
	 20.228.195.55

###
### Step 3: Create database user 
### 

Run the following script from your Redshift query editor to create a new user with username artemis_agent:

	CREATE USER artemis_agent WITH PASSWORD <password>;
	GRANT USAGE ON SCHEMA public TO artemis_agent;
	GRANT CREATE ON DATABASE your_database_name TO artemis_agent;
	GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO artemis_agent;
	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO artemis_agent;

The script assumes your DBT model tables are in the public schema. If they are in a different schema, replace `public` with the correct schema name.

###
### Step 3: Complete Artemis Setup
### 

In the Redshift connection form in Artemis, fill in the host, port, database, username and password fields. 

