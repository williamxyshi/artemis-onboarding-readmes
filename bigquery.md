# Connect to BigQuery

## Step 1: Generate Service Account

Generate an Artemis managed service account for your organization by clicking the **Generate Service Account** button on the warehouse connection onboarding page.

## Step 2: (Optional) Update Domain Restricted Sharing Policy

If you have configured your Domain Restricted Sharing policy to limit access based on organization ID, you will need to add the Artemis organization resource ID to the list of allowed values. This will allow you to add the Artemis service account to IAM policies in your organization.

1. In the google cloud console, navigate to the **Organization Policies**
2. Select **Domain Restricted Sharing** from the list of constraints.
3. Click **Manage Policy**
4. Click on your existing **Allow** rule
5. Click **ADD VALUE**
6. Enter the Artemis organization resource ID in the value field: **principalSet://iam.googleapis.com/organizations/996999934235**
7. Click **Done**
8. Click **SET POLICY**

## Step 3: Assign Metadata IAM Roles

Copy the service account email generated in Step 1. Assign the following roles to the Artemis service account at the organization level. Alternatively you can assign these roles at the project level to limit BigQuery access to select projects.

- bigquery.metadataViewer
- bigquery.resourceViewer
- bigquery.jobUser

Note the above roles only permit Artemis to view the structure of objects and BigQuery workloads. They do not allow Artemis to view any data stored within objects.

1. In the google cloud console, select your organization from the dropdown in the top left
2. Go to IAM from the navigation menu
3. Click **GRANT ACCESS**
4. Enter the copied service account email as the principal
5. Add the 3 roles listed above
6. Click **SAVE**

## Step 4: Enable Billing Export

If you do not have Standard Usage Cost and Pricing billing exports enabled on your billing account used for BigQuery, you will need to enable both. Note that the exported tables can take up to 48 hours to be created. We recommend exporting to a multi-region BigQuery dataset to guarantee that the billing tables include data from the past month. Documentation for enabling billing exports can be found [here](https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup).

## Step 5: Assign Read Access To Billing Export Tables

Assign the following role to the Artemis service account on the exported standard usage cost and pricing tables. This will allow Artemis to read the contents of both tables and retrieve your BigQuery usage and pricing data.

- **roles/bigquery.dataViewer**

The exported tables use the following naming scheme:

- **gcp_billing_export_v1_BILLING_ACCOUNT_ID**
- **cloud_pricing_export**

For each table:

1. In the google cloud console, select the project where you have chosen to export your billing table from the dropdown in the top left
2. Go to BigQuery from the navigation menu
3. Click on the table name from the resource viewer on the left
4. Click the **SHARE** button in the table header
5. Click **ADD PRINCIPAL**
6. Enter the Artemis service account email as the principal
7. Add the **BigQuery Data Viewer** role
8. Click **SAVE**

## Step 6: Enter Billing Export Information In Form

Fill in the fields in the warehouse connection form with your billing export information. Note the billing account id has the format XXXXXX-XXXXXX-XXXXXX. Refer to this guide for getting your billing account id [here](https://cloud.google.com/billing/docs/how-to/find-billing-account-id).

## Step 7: Click Confirm and Test

Click **Confirm and Test**. Note this can take a few minutes. You may receive a message stating you need to enable additional google cloud APIs. If you do, simply follow the link and click **ENABLE** and try again.
	  
