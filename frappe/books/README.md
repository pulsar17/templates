# An in-production invoice template for Frappe Books
This is the source of the invoice template used by my dad for his business 💼  
Frappe Books version: `0.19.0`

## Features
- 🙏 Custom Salutation
- ℹ️ PAN No. and Contact Details below Company Name
- 📅 P.O. No. and P.O. Date support
- 📍 Party State and State Code support
- ⚖️ Units for individual Invoice Items
- 🙈 Ability to hide Quantity, Unit, and Rate columns for individual Invoice Items (useful for items for which these columns don't make sense, like Freight)
- 🗒️ Custom Invoice Notes
- ☑️ Stamp and Signing section

## How to add these features in your Frappe Books
Most of these features make use of the Custom Form Fields feature which can be enabled using   
`Settings > General > ☑️ Enable Form Customization` ([Official Docs](https://docs.frappebooks.com/miscellaneous/customize-form.html#custom-fields))

1. You first need to select the page on which these custom fields will appear in `Setup > Customize Form`. For example, to add P.O. No. you would select `Sales Invoice` in the dropdown.

2. Then you need to add the custom field one by one. There is also an edit button (last column of the row) which shows more options that are not present in the default list view.
Click the Edit button, and fill values according to the `CustomField` table I've provided below.

You would need to mark some fields as required and provide a default value while creating them even though they *should* be optional. This is an idiosyncrasy of Books that I haven't found a workaround for.

A partial and slightly modified dump of my `CustomField` table which should help as a reference. Most of the columns should have same names in the UI.

|                    label                     |     fieldname      | fieldtype | isRequired |  default   | section |  tab   |     parent      |
|----------------------------------------------|--------------------|-----------|------------|------------|---------|--------|-----------------|
| P.O. No                                      | pono               | Data      | ⬜         |            | Default | Main   | SalesInvoice    |
| P.O. Date                                    | poDate             | Date      | ⬜         |            | Default | Main   | SalesInvoice    |
| Salutation                                   | salutation         | Data      | ☑️         | Any val    | Default | Custom | SalesInvoice    |
| Custom Invoice Notes (Added after all Items) | customInvoiceNotes | Data      | ⬜         |            | Default | Main   | SalesInvoice    |
| P.A.N No                                     | panNo              | Data      | ☑️         | CGIPK8332F | Default | Custom | SalesInvoice    |
| Show Unit                                    | showUnit           | Data      | ☑️         | true       | Default | Custom | Item            |
| Show Quantity                                | showQuantity       | Data      | ☑️         | true       | Default | Custom | Item            |
| showRate                                     | showRate           | Data      | ☑️         | true       | Default | Custom | Item            |

After adding all the custom fields, the `SalesInvoice` page would look something like:

![image](https://github.com/pulsar17/templates/assets/42387090/e49eaf1d-3531-4098-bdec-9a8118d8457c)

and `Item` page like:

![image](https://github.com/pulsar17/templates/assets/42387090/446eefb4-ef90-4bd8-a407-a5155dd52260)


## Example Invoice
![SINV-1001](https://github.com/pulsar17/templates/assets/42387090/dfa2bfff-1eac-4a3c-82a1-24575717cfef)

## License
If a license is not provided in the curent directory, the one at the root of this repo shall be the license of this template.

