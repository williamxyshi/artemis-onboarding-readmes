# Connect to Big Query

###
### Step 1: Locate Project ID
### 
Locate the project you want to connect's ID from the Google Cloud Consoles project list.

###
### Step 2: Create Google Service Account
### 
To connect to your warehouse, Artemis requires a Google service account. For more information, see Google's [Creating a service account documentation](https://cloud.google.com/iam/docs/creating-managing-service-accounts#creating).

After generating a service account, create a private key. For more information, see Google's [private key documentation](https://cloud.google.com/iam/docs/creating-managing-service-account-keys#creating).

The Private Key file should look something like the example below. Paste the entire thing ({} and all) into the Service Account field.


	{
	  "type": "service_account",
	  "project_id": "apt-bonbon-51234",
	  "private_key_id": "e84142131231241a0925a60e512341242ca2",
	  "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDSa2WOz5JB6HB+\nnlZCp63cmaEXQDbKtUKUnjSfCW3Srp/JRcObhbzmg3I3MBquzPHy0Hz/t/OyJd1x\n6zsbwu3e0+ov2xIZgc7CK9XQ6sSrxUHcJcpTt7QgpU1exZS4NDJTaF9pXfjJp+3B\nnW1GYiuWyYUhcsDpluaiDRbP6Sw27H8BiRJMnZWF/F9gTVgRRNHILgRfbk5ASCd1\nr3nwLrGyAWWf+tTLx3SyxnGVpUJt4Zslh8MW2sA481eVG/OfPk2XBMgR7vhvPIJZ\nBjBDQBfRTRT5pl5aruXmojkOTH1FaB4gA0I3v+Y5hng95G/TF7J62WpDzo8FndR9\nCEojB1VzAgMBAAECggEANuMKx7i/Vu/RwEV/DL3+bQep7I20fSXQ60FlSoLEIeup\nBHBtOLkm8B4jgd/LZgNRrVvS70VFjRexbtDER1aOg8xVTwxe6UZh72ZNzMQgbwny\nRwGYiEPn+YgE6Y6yX2ZGJlmYYbzAcalksdjgalYgE6Y6yX2ZGJlmYYbzAcalksdjgalYgE6Y6yX2ZGJlmYYbzAcalksdjgalYgE6Y6yX2ZGJlmYYbzAcalksdjgal;kdglk;asjdfakl;jsdfklasjf;lkjs;lkajdlf0htQN2RCB/TqFuDtFQXiOC1hRd+yhTJwhZF2H9Znp1y+G\nSKSOQlT1aJ3oiogJy9Dnzq215+OEh9uN0Qy6aHC3EkTT4Y3qUu24wwPKElOY+wup\nkNIRvW2eAQoDnYEKqBBxHYrZs09EmlEBxXreOL0CgYEA9GBNlKDo3ZOwB1fQz6Ar\nSe29VdFs/N2D/nf4ZNSAOvJA5rkIIj1yFv3QTg4Pyr4u0ZVaQ6fluvuqCSQlmTjL\nAf/iD+e4d+wIaJiZzL6RV/Q8WufGVT0Cv6WLmcOcR3W4vewNjorD19wUImGQfUWt\nsNzgx4otw3bY8lyYppU6b+0=\n-----END PRIVATE KEY-----\n",
	  "client_email": "sample@apt-bonbon-51234.iam.gserviceaccount.com",
	  "client_id": "wfsasdfaee2f",
	  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
	  "token_uri": "https://oauth2.googleapis.com/token",
	  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
	  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/artemis%40apt-bonbon-51234.iam.gserviceaccount.com"
	}

###
### Step 3: Configure Service Account
### 

Go to the IAM & Admin page and click "+add".

Enter the client_email from your generated private key (ex: "sample@apt-bonbon-51234.iam.gserviceaccount.com") in the "New Principals" Field

Choose "BigQuery User" as the Role

###
### Step 3: Complete Artemis Setup
### 
Navigate back to the Artemis App Page, and enter your project ID from Step 1 into the "Project ID" field 

Copy and paste the contents of the entire service account principal key file into the Service Account field
	  
