---
title: Posting via Financial Integration Service
layout: reference
---

## ERP Integration Methods and Customer Onboarding

When using SAP Concur Financial Integration Service (FIS) data should you choose a JSON response or the SAP Concur extract files? Some ERP systems will require a file import to book Spend journal entries. You should plan to create a file based on the data returned from SAP Concur FIS when necessary. SAP Concur extract files are not always necessary.

FIS data should be used in the following situations:

* Data integration straight into the ERP as a web transaction.
* File import into the ERP. You should be ready to create this file based on data received from FIS.

Using FIS data for either of the above situations will simplify processes and enable FIS benefits.

### New Customers

We expect all new SAP Concur customers will use FIS data instead of extract files (*Life Science customers probably will need the Attendee Detail extract file created until an API is released for Attendee transactions*).

### Existing Customers

Prior to making the decision to only use FIS data with an existing customer, complete the Kickoff Questionnaire with the customer. Existing customers likely have integrations in place based on SAP Concur extracts. You and the customer will determine if FIS data can replace all of the customer's integrations. For example, customers may use the extract file content for import into their ERP and into analytical databases.

By default, SAP Concur extract files are disabled when FIS is enabled. Your review of the questionnaire with the customer will determine if they need any extracts created in addition to enabling FIS:

* Standard Accounting Extract (SAE)
  * FIS will replace the need to use the SAE for journal entries.
  * Does the customer use the SAE for any analytical requirements? If yes, then you will review the FIS data to determine if it can be used instead of the SAE.
* Payment Request Accounting Extract (PRAE) - same as above.
* Attendee Details Extract
  * Used for compliance reporting purposes. This file can still be created even if FIS is used.
  * You will submit a case for this file to be continued as an "informational extract." SAP Concur will need to setup this informational extract based on the customer's request.
* Expense Pay Confirmation Extract

## API Sequence Flow

The flow consists of calling the API in this sequence:

1. [Get Financial Transactions](/api-reference/financial-integration/v4.financial-integration.html#get-transactions) - Obtain final-approved reports (or invoices) from the FIS queue.
1. [Post Financial Transaction Acknowledgements](/api-reference/financial-integration/v4.financial-integration.html#post-acknowledgements) - Acknowledge each report or invoice has been obtained.
1. [Post Financial Transactions Confirmations](/api-reference/financial-integration/v4.financial-integration.html#post-confirmations) - Post the status of the ERP integration for each report (success or failure) back into SAP Concur after integrating into the customer's ERP.
1. [Post Financial Payment Confirmations](/api-reference/financial-integration/v4.financial-integration.html#payment-confirmations) - Post the financial payment results into SAP Concur.

The following are the recommended steps when you create a file based on FIS data prior to importing into the ERP:

1. You will remove any report (or invoice) from your file if it is rejected at the ERP.
1. You will then post a rejected status for that report back into SAP Concur via FIS post confirmation endpoint. (see step 3 above)
1. The customer will then address any rejected reports directly in SAP Concur where they will be re-processed at a later date and eventually included in FIS.
1. You will re-try your file without the rejected reports. Upon 100% successful import of the remaining reports, you will post successful statuses of those reports back into SAP Concur via FIS.

The above steps will maintain consistency between the customer's ERP and their SAP Concur Spend Management service. If they cannot be performed due to error-handling logistics between you and customer, then you can post successes for the file content back into SAP Concur. The customer will handle the errors directly with the ERP. However, their ERP and SAP Concur will not be in sync at this point.

## Timing to Run FIS

You will review the timing of the FIS API requests to ensure they are not interfering with the customer administrator's ability to send an expense report (or invoice) back to the employee prior to ERP integration.

This may require you to develop a button in the UI of your integration to allow the customer to initiate the FIS process on demand. This would free you and the customer from coordinating the timing.

For example, the SAP Concur workflow typically includes a final approval step that is completed by Finance/Accounting. Once the accountant final-approves a report (or invoice), the report is queued into FIS. If necessary, the accountant can pull this report back and send it back to the employee for adjustment prior to ERP integration, but only if you have not yet picked up the report. So, the process should include awareness of the timing between you and your customer.
