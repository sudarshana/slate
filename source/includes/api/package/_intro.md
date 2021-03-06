# Package

The packaging API allows you to move a Flow from one tenant to another. This can be handy for a few example reasons:

- You have Sub-Tenants that you use for testing or team based development of Flows. You then have a master tenant for all "production" Flows.
- You have a library of useful Flows that you want to distribute as templates.
- You want to create a "store" of Flows that can be used by your customers to help them leverage and adapt your existing intellectual property.

A few things to consider with packaging:

1. The source Tenant should be used for editing. If you make changes in the target Tenant and attempt to apply those back to the source, you can accidentially overwrite changes in the source, not applied in the target.
2. Packaging overwrites the existing Flow with the same Flow identifier. As a result, you can apply upgrades to a target Tenant.
3. If you plan to use a Service in multiple Tenants, make sure you create the Service in the source Tenant and distribute a Flow package for all target Tenants. If a Service is installed in each Target Tenant, it will be duplicated as we do not "merge" Services even if they point to the same Service end-point.
4. Do not refresh the Service in the Target Tenant if you are simply changing the endpoint URL. Packaging only pulls in the Types that are used by the Flow. If you refresh the whole Service in the Target Tenant, you can end up with duplicate Types.
5. When you export a Flow Package, you will receive a String that contains all the information needed to execute your Flow.
6. We do not automatically Package sub-Flows.
7. You can only export a package of the Flow if that Flow has a SnapShot (generated by either Running or Publishing the Flow).

The package file should never be altered. Please refer to the SnapShot APIs if you need to get information about your Flows.

## Export Latest Package Version

This API will package the latest version of a Flow SnapShot.

#### HTTP Request

`GET /api/package/1/flow/{id}?nullPasswords={null_passwords}`

#### Parameters

Key | Description
--- | -----------
**id**<br/>string | Unique identifier for the Flow you wish to Package.
**null_passwords**<br/>boolean | Indicates if the Package should inlude any Configuration Values that contain Password content typed values.

## Export Specific Package Version

This API will Package a specific version of a Flow SnapShot.

#### HTTP Request

`GET /api/package/1/flow/{id}/{version_id}?nullPasswords={null_passwords}`

Key | Description
--- | -----------
**id**<br/>string | Unique identifier for the Flow you wish to Package.
**version_id**<br/>string | Unique identifier for the Flow Version you wish to Package.
**null_passwords**<br/>boolean | Indicates if the Package should inlude any Configuration Values that contain Password content typed values.

## Import Package

> Example Request

```http
POST https://flow.manywho.com/api/package/1/flow?uriMapping[0][from]=https://flow.manywho.com/plugins/manywho/api/identity/1/authentication&uriMapping[0][to]=https://myservice.mycompany.com/plugins/api/identity/1/authentication
```

This allows you to import a Flow Package back into a Tenant. It's important to note that this is not the same as cloning a Flow. If you import a Flow Package into a Tenant that contains a Flow with the same Flow identifier, the Flow in the Target Tenant will be overwritten with the Flow Package being imported.

#### HTTP Request

`POST /api/package/1/flow/{id}/{version_id}?isActive={isActive}&isDefault={isDefault}&uriMapping[n][from]={from}&uriMapping[n][to]={to}`

Key | Description
--- | -----------
**isActive**<br/>boolean | Optional. A boolean value indicating if the Package should be imported as an active SnapShot.
**isDefault**<br/>boolean | Optional. A boolean value indicating if the Package should be imported as an active SnapShot that should be immediately made the default version.
**uriMapping[n][from]**<br/>string | Optional. At position n in an array of uriMapping values, this is the Uri that any Services in the Package currently point to.
**uriMapping[n][to]**<br/>string | Optional. At position n in an array of uriMapping values, this is the Uri that the matching Service (as per the from) should point to once the Package has been imported. This allows you to map Services to a different Uri as part of the packaging process.