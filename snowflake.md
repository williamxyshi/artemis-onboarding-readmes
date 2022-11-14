# Connect to Snowflake

###
### Step 1: Run Script in Snowflake warehouse
### 
copy and paste the following script into a snowflake worksheet and execute all lines. 


	  begin;

	   -- create variables for user / password / role / warehouse / 		
	   -- database (needs to be uppercase for objects)
	   set role_name = 'ARTEMIS_ROLE';
	   set user_name = 'ARTEMIS_USER';
	   set user_password = 'password123';
	   set warehouse_name = 'ARTEMIS_WAREHOUSE';
	   set database_name = 'ARTEMIS_DATABASE';

	   -- change role to securityadmin for user / role steps
	   use role securityadmin;

	   -- create role for Artemis
	   create role if not exists identifier($role_name);
	   grant role identifier($role_name) to role SYSADMIN;

	   -- create a user for Artemis
	   create user if not exists identifier($user_name)
	   password = $user_password
	   default_role = $role_name
	   default_warehouse = $warehouse_name;

	   grant role identifier($role_name) to user identifier($user_name);

	   -- change role to sysadmin for warehouse / database steps
	   use role sysadmin;

	   -- create a warehouse for Artemis
	   create warehouse if not exists identifier($warehouse_name)
	   warehouse_size = xsmall
	   warehouse_type = standard
	   auto_suspend = 60
	   auto_resume = true
	   initially_suspended = true;

	   -- create database for Artemis
	   create database if not exists identifier($database_name);

	   -- grant Artemis role access to warehouse
	   grant USAGE
	   on warehouse identifier($warehouse_name)
	   to role identifier($role_name);

	   -- grant Artemis access to database
	   grant CREATE SCHEMA, MONITOR, USAGE
	   on database identifier($database_name)
	   to role identifier($role_name);

	   -- change role to ACCOUNTADMIN for STORAGE INTEGRATION support (only needed for Snowflake on GCP)
	   use role ACCOUNTADMIN;
	   grant CREATE INTEGRATION on account to role identifier($role_name);
	   use role sysadmin;

	  commit;

To connect Artemis to an existing snowflake warehouse, replace the `warehouse_name` string with the name of your warehouse. 

###
### Step 2: Add network policy
###
If you have an existing network policy execute the following command after replacing <your_network_policy_name>

    ALTER NETWORK POLICY <your_network_policy_name> SET {[ALLOWED_IP_LIST] = ('35.227.135.0/29', '35.234.176.144/29')]};

If you do not have an existing network policy or would like to create a new one, execute the following command after replacing <your_network_policy_name>

    CREATE NETWORK POLICY <artemis_ip_whitelist> ALLOWED_IP_LIST = ('35.227.135.0/29', '35.234.176.144/29');

###
### Step 3: Complete Artemis setup
###
fill in the required fields with the values used in the script. 

#### host - account string thats made up of id, region and provier, ex: zu9xxxx.canada-central.azure
#### database - DATABASE_NAME created by script, ex: ARTEMIS_DATABASE
#### username - user_name created by script: ex: ARTEMIS_USER
#### password - password created by script: ex: password123