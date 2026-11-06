# Deploying the Partner Sales solution to a fresh scratch org

## What the project contains

| Component | Why it's needed |
|---|---|
| `flows/Partner_Sales_New_Opportunity_with_Products` | The screen flow (main flow). |
| `flows/Partner_Sales_Get_Available_Entries` | Subflow used by the Products screen action. |
| `flows/Accountsand_Leads` | Lead flow (unchanged). |
| `objects/Lead/recordTypes/MerchantSalesEU`, `objects/Lead/businessProcesses/Merchant Sales Lead Process`, `standardValueSets/LeadStatus` | Needed by `Accountsand_Leads`, which filters on Lead record type. A fresh org has no Lead record types, so the flow doesn't deploy without these. |
| `objects/Opportunity/fields/MainCompetitors__c` | Main Competitor(s), written by the flow. |
| `objects/PricebookEntry/fields/ProductFamily__c`, `IsProductActive__c` | Formula fields the subflow filters on. |
| `standardValueSets/OpportunityType` | Adds "New Customer" (shows Main Competitor(s)). Replaces the Type values with this org's list. |
| `standardValueSets/Product2Family` | Hardware, Software, Services. |
| `flexipages/AccountRecordPage` | Account page with the flow component. |
| `applications/standard__LightningSales` | Makes AccountRecordPage the Account page in the Sales app. |
| `flexipages/LightningSales_UtilityBar` | Utility bar the Sales app refers to. |
| `profiles/Admin` | System Administrator: field access for the three new fields only (Main Competitor(s) read and edit; the two formula fields read). |
| `profiles/Custom: Sales Profile` | Only the permissions the solution needs (see below). Profile deploys merge, so other settings stay as they are. |

**Profile permissions:**
- Objects:
  - Account: Read.
  - Opportunity: Read, Create, Edit. Edit is needed to add Opportunity Products.
  - Price Book and Product: Read.
- Fields:
  - Opportunity Account, Type and Main Competitor(s): read and edit.
  - Product Family and Product Code: read.
  - The two price book entry formula fields: read.
- Flow access: Partner_Sales_New_Opportunity_with_Products.
- The Sales app is visible, and the Account, Opportunity, Price Book and Product tabs are on.

## Deploy

```bash
sf org create scratch --definition-file config/project-scratch-def.json --alias partner_sales --duration-days 7
sf project deploy start --source-dir force-app --target-org partner_sales --wait 20
```

**Verified on 2026-11-06:** the whole project deployed in one go (16 components) to a new scratch org created from `config/project-scratch-def.json`. In that org:
- the profile permissions were in place;
- the Type and Product Family values were in place;
- both Partner Sales flows were Draft.

## After the deploy

1. **Activate the flows:** subflow first, then the main flow. Both are in source as Draft. An inactive flow on the Account page shows an error to anyone who isn't an admin.
2. **Create the data. Records aren't metadata, so the deploy doesn't create them:**
   - products with a Product Family;
   - their entries in the Standard Price Book;
   - an active price book **named "Standard"** with entries for those products.

   Without it, the flow shows "price book not found".
3. **Create a user** with the `Custom: Sales Profile` profile to test as a Partner Sales user.
