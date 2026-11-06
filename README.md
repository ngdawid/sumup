# Partner Sales: New Opportunity with Products

A Salesforce DX project with two pieces of work:

- **Partner Sales screen flow:** on an Account record page, a seller creates an Opportunity and its Opportunity Products (quantity 1, from the price book named "Standard") in one all-or-nothing save.
- **Lead flow:** the record-triggered flow `Accountsand_Leads`, kept unchanged.

## Layout

| Path | Contents |
|---|---|
| `force-app/main/default/flows/` | `Partner_Sales_New_Opportunity_with_Products` (screen flow), `Partner_Sales_Get_Available_Entries` (its screen-action subflow), `Accountsand_Leads` |
| `force-app/main/default/objects/` | New fields on Opportunity and Price Book Entry; Lead record type and sales process |
| `force-app/main/default/standardValueSets/` | Opportunity Type, Product Family and Lead Status values |
| `force-app/main/default/flexipages/`, `applications/` | Account record page with the flow, and its assignment in the Sales app |
| `force-app/main/default/profiles/` | Access for System Administrator and `Custom: Sales Profile` (only the permissions this solution needs) |
| `config/project-scratch-def.json` | Scratch org definition |
| `docs/` | Deployment guide |

## Deploy to a new scratch org

Prerequisites: Salesforce CLI and an authenticated Dev Hub.

```bash
sf org create scratch --definition-file config/project-scratch-def.json --alias partner_sales --duration-days 7
sf project deploy start --source-dir force-app --target-org partner_sales
```

Then, in the org:

1. Activate `Partner_Sales_Get_Available_Entries`, then `Partner_Sales_New_Opportunity_with_Products`. Both are deployed as Draft.
2. Create the data:
   - products with a Product Family;
   - their standard prices;
   - an active price book named **Standard** with entries for those products.
3. Create a user with `Custom: Sales Profile` to test as a Partner Sales user.

Details are in [docs/Deployment.md](docs/Deployment.md).

## Docs

- [docs/Deployment.md](docs/Deployment.md): what each component is for, and the deploy and post-deploy steps.

## Tooling

`package.json` provides Prettier (including XML), ESLint and Jest from the Salesforce project template. Run `npm install` to use them. That also enables the Husky pre-commit hook, which formats staged files.
