###
### Step 1: Create a user and storage integration for Artemis with a limited set of permissions.
### 
Copy and paste the following script into a snowflake worksheet and execute all lines. Replace <ENTER_PASSWORD_HERE> with your 
desired password for the Artemis user.

	set user_password = '<ENTER_PASSWORD_HERE>';

 	-- note the following variable values must be in uppercase
	set user_name = 'ARTEMIS_DATA_USER';
	set role_name = 'ARTEMIS_DATA_ROLE';
	set warehouse_name = 'ARTEMIS_DATA_WAREHOUSE';
	set artemis_database_name = 'ARTEMIS_DATA_DATABASE';
	set artemis_schema_name = 'ARTEMIS_DATA_SCHEMA';
	set storage_integration_name = 'ARTEMIS_DATA_STORAGE_INTEGRATION';
	
	use role accountadmin;
	
	-- create a role for Artemis
	create role if not exists identifier($role_name);
	
	-- create a user for Artemis
	create user if not exists identifier($user_name)
	password = $user_password
	default_role = $role_name
	default_warehouse = $warehouse_name;
	grant role identifier($role_name) to user identifier($user_name);
	
	-- create a warehouse for Artemis
	create warehouse if not exists identifier($warehouse_name)
	warehouse_size = xsmall
	auto_suspend = 60
	initially_suspended = true;
	grant MONITOR, OPERATE, USAGE, MODIFY on warehouse identifier($warehouse_name) 
	to role identifier($role_name);
	
	-- grant Artemis read privileges on snowflake metadata
	grant imported privileges on database snowflake to role identifier($role_name);
	
	-- create a util database for Artemis
	create database if not exists identifier($artemis_database_name);
	grant USAGE on database identifier($artemis_database_name) to role identifier($role_name);

 	use database identifier($artemis_database_name);
	
	-- create a util schema for Artemis
	create schema if not exists identifier($artemis_schema_name);
	grant USAGE, CREATE STAGE on schema identifier($artemis_schema_name) to role identifier($role_name);
	
	use schema identifier($artemis_schema_name);
	
	-- create a storage integration for Artemis
	-- this is used to export snowflake metadata directly to an azure container
	create storage integration if not exists identifier($storage_integration_name)
	    type = external_stage
	    storage_provider = 'AZURE'
	    azure_tenant_id = '916b55c7-ffe7-4a35-b6d1-9578352e3d42'
	    enabled = true
	    storage_allowed_locations = ('*');
	grant usage on integration identifier($storage_integration_name) to role identifier($role_name);
	
	-- grant Artemis monitor privileges on all warehouses
	CREATE OR REPLACE PROCEDURE grant_monitor_to_role(ROLE_NAME VARCHAR)
	RETURNS STRING
	LANGUAGE JAVASCRIPT
	EXECUTE AS CALLER
	AS
	$$
	    var show_warehouses_statement = snowflake.createStatement( {sqlText: "SHOW WAREHOUSES"} );
	    var result_set = show_warehouses_statement.execute();
	
	    while (result_set.next()) {
	        var warehouse_name = result_set.getColumnValue(1);
	        
	        var grant_command = `GRANT MONITOR ON WAREHOUSE ${warehouse_name} TO ROLE ${ROLE_NAME};`;
	        
	        var grant_statement = snowflake.createStatement( {sqlText: grant_command} );
	        grant_statement.execute();
	    }
	    
	    return 'Monitor privileges granted to role ' + ROLE_NAME + ' for all warehouses.';
	$$;
	CALL grant_monitor_to_role($role_name);
	
	-- grant Artemis usage/references privileges on all databases/shemas/tables/views
	-- note that usage/references only permits Artemis to view the structure of objects
	-- it does not allow Artemis to access any data stored within any object
	CREATE OR REPLACE PROCEDURE grant_usage_to_role(ROLE_NAME VARCHAR)
	RETURNS STRING
	LANGUAGE JAVASCRIPT
	EXECUTE AS CALLER
	AS
	$$
	    var show_databases_statement = snowflake.createStatement( {sqlText: "SHOW DATABASES"} );
	    var result_set = show_databases_statement.execute();
	
	    while (result_set.next()) {
	        var database_name = result_set.getColumnValue(2);
	        var origin = result_set.getColumnValue(5);
	
	        // Skip imported databases
	        if (origin != null && origin.trim() !== '') {
	            continue;
	        }
	
	        var sql_command1 = `GRANT USAGE ON DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement1 = snowflake.createStatement( {sqlText: sql_command1} );
	        grant_statement1.execute();
	        
	        var sql_command2 = `GRANT USAGE ON ALL SCHEMAS IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement2 = snowflake.createStatement( {sqlText: sql_command2} );
	        grant_statement2.execute();
	        
	        var sql_command3 = `GRANT USAGE ON FUTURE SCHEMAS IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement3 = snowflake.createStatement( {sqlText: sql_command3} );
	        grant_statement3.execute();
	
	        var sql_command4 = `GRANT REFERENCES ON ALL TABLES IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement4 = snowflake.createStatement( {sqlText: sql_command4} );
	        grant_statement4.execute();
	
	        var sql_command5 = `GRANT REFERENCES ON FUTURE TABLES IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement5 = snowflake.createStatement( {sqlText: sql_command5} );
	        grant_statement5.execute();
	
	        var sql_command6 = `GRANT REFERENCES ON ALL VIEWS IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement6 = snowflake.createStatement( {sqlText: sql_command6} );
	        grant_statement6.execute();
	
	        var sql_command7 = `GRANT REFERENCES ON FUTURE VIEWS IN DATABASE ${database_name} TO ROLE ${ROLE_NAME};`
	        var grant_statement7 = snowflake.createStatement( {sqlText: sql_command7} );
	        grant_statement7.execute();
	    }
	    
	    return 'Usage/References privileges granted to role ' + ROLE_NAME + ' for all databases/shemas/tables/views.';
	$$;
	CALL grant_usage_to_role($role_name);

###
### Step 2: Add network policy
###
If you have a network policy that blocks IP addresses, you will need to add the Artemis IP address to your allowed IP list 20.237.100.50\
If you would like to create a new network policy for Artemis, run the following commands.

	CREATE NETWORK RULE IF NOT EXISTS allow_artemis_data_access_rule
	    MODE = INGRESS
	    TYPE = IPV4
	    VALUE_LIST = ('20.237.100.50');
	CREATE NETWORK POLICY IF NOT EXISTS artemis_data_network_policy
	  ALLOWED_NETWORK_RULE_LIST = ('allow_artemis_access_rule');
	ALTER USER ARTEMIS_DATA_USER SET network_policy = artemis_data_network_policy;

###
### Step 3: Complete Artemis setup
###
Enter your snowflake account identifier in the account field.\
Enter the chosen Artemis user password in the password field.\
If you changed any of the default values when executing the script, be sure to update the default field values as well.
