# Enable Cloud Billing data export to BigQuery

ref: <https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#how-to-enable>

## Steps

### Permissions required for this task

ref: <https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#required_permissions>

For Cloud Billing, you need either the Billing Account Costs Manager role or the Billing Account Administrator role on the target Cloud Billing account.

ps. This step needs resellers to grant permission to us.(<gcp_m200_sre@sinyi.com.tw>)

### Enable BigQuery Data Transfer API

ref: <https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#api>

### Create a BigQuery dataset

ref: <https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#create-bq-dataset>

### Enable Cloud Billing export to the BigQuery dataset

ref: <https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#enable-bq-export>
It might take up to 5 days for the exported data to fully catch up to your current usage data.

## Visualize your costs with Looker Studio

ref: <https://cloud.google.com/billing/docs/how-to/visualize-data>
