# Statuspage.io ServiceNow Integration

## Statuspage.io Content

    - After updating header.html, footer.html, or custom.css, copy / paste them into the statuspage management portal: https://manage.statuspage.io/pages/htpwsxgm14yp/full-customize.

## ServiceNow Application

To test / deploy changes see [ServiceNow Development](https://confluence.expedia.biz/pages/viewpage.action?spaceKey=EITSM&title=ServiceNow+Development)

### Manual Testing

    - Use the "Scripts - Background" page in the dev environemnt.
    - Copy / paste code (edit as necessary) from backgroundScriptTest.js and click "Run Script"


### Update Incident Templates

- StatuspageContent.js is compiled using generateStatuspageContent.js to combine the incident template files.
- After updating the .template files, run:

```javascript
# from ServiceNow directory
npm install
node generateStatuspageContent.js
```

## Manage EG Major Incident Statuspage Configuration in ServiceNow

- User must have the role 'statuspageadmin' in ServiceNow
- Under StatusPage in ServiceNow, click on "StatusPage Components" (ServiceNow table is x_expe2_statuspage_statuspage_components)
- Sort the list by "Status Grid Group Order" and "Status Grid Group Row Order" 
- **Any changes made to the Statuspage Components table in ServiceNow must be exported to Statuspage.** 
    - Export the table in ServiceNow by right clicking any column header, then "Export", then "JSON"
    - Copy the entire JSON text `{"records":[{"inc...` to line 8 in the Statuspage Custom header HTML
    - Line 8 should look like `const component_configs = {"records":[{"inc...`
    - Then click "Publish Changes"
- To add a new component in Statuspage, insert a new row with the appropriate configuration, then click "Sync Components (dry run)" to preview the changes. To proceed, click "Sync Components"
- To remove a component in Statuspage, delete the row in ServiceNow, then delete the component from Statuspage
- To rename a component, rename it in Statuspage and update the row in ServiceNow. Once finished you can validate they are in sync by running "Sync Components (dry run)" which should display the message "No component updates needed."
- Component groups are managed the same way as components, however all rows for a group in ServiceNow must be updated at the same time.

### ServiceNow Configuration Field Definitions

|             Field Name                 |  Description |
|----------------------------------------|--------------|
| Component                              | The name of the Component in Statuspage. |
| Component Group                        | The Component Group in Statuspage the Component belongs to. |
| Incident Brands                        | A comma separated list (no spaces) of Brands associated to an Incident to map to this Component. If no LOBs are configured, then this Component will be matched any time one of Brands is associated to an incident.|
| Incident LOBs                          | A comma separated list (no spaces) of LOBs associated to an Incident to map to this Component. If no Brands are configured, then this Component will be matched any time one of LOBs is associated to an incident. If both Brands and LOBs are configured, at least one of each must be associated to an incident for the Component to be matched. |
| Incident Category                      | Incident category |
| Incident DLs                           | A comma separated list (no spaces) of DLs associated to an Incident to map to this Component. |
| Status Grid Group                      | Top level (unnested) rows in the Statuspage Grid (UI). Multiple Component Groups may share the sname Status Grid Group. |
| Status Grid Group Row                  | The nested rows in the Statuspage Grid (UI). Only one Component Group can be mapped to a Status Grid Group Row.|
| Status Grid Group Column               | The column to display this component's status under. Must be blank (no header) or one of: Lodging,Flights,Cars,Packages,Activities,Cruise,Other |
| Status Grid Group Order                | The order to sort the Status Grid Groups. Must be the same value for each Status Grid Group.|
| Status Grid Group Row Order            | The order to sort the Status Grid Group Rows. Must be the same value for each Status Grid Group Row.|

### Troubleshooting

[View ServiceNow logs in production](https://expedia.service-now.com/syslog_app_scope_list.do?sysparm_query=sys_scope.nameSTARTSWITHStatuspage&sysparm_view=)
- Logs are only visible to admins and members of "Event Management" or "Incident Management" ServiceNow groups.
