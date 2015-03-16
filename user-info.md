#cf user-info

```cf user-info``` command will list the roles of Orgs and Spaces the user has targeted. There should be a command which can list the roles of Orgs and Spaces which the user has targeted and the command should be fast unlike ```cf orgs-users``` and ```cf space-users```   


***Basic Requirements for running this command :***   
1. User should be logged in.  
2. User should target Org and Space(If the user has targeted only Org then user will be able to see the roles associated to only Org)  


***Output :***  
```
$ cf user-info
Getting user information......
User    Org       Space       Role
asif   asifOrg   asifSpace   ORG MANAGER, SPACE MANAGER, SPACE DEVELOPER
```


***Processing of the request :*** 

For fetching the roles of an Org which user has targeted cli will query the below three api which will return the org if the user has the particular role and on the basis of the response recieved cli will add the particular role to the role List.

```
ORG MANAGER 	:	/v2/organizations?q=manager_guid:UserGuid;q=name:OrgName
BILLING MANAGER : 	/v2/organizations?q=billing_manager_guid:UserGuid;q=name:OrgName
ORG AUDITOR		:	/v2/organizations?q=auditor_guid:UserGuid;q=name:OrgName
```

For fetching the roles of Space which user has targeted cli will query the below three api which will return the space if the particular role is associated with that user and on the basis of the response recieved cli will add the particular role to the role List.

```
SPACE DEVELOPER	:	/v2/spaces?q=developer_guid:UserGuid;q=name:SpaceName
SPACE MANAGER	:	/v2/users/UserGuid/managed_spaces?q=organization_guid:OrgGuid;q=name:SpaceName
SPACE AUDITOR	:	/v2/users/UserGuid/audited_spaces?q=organization_guid:OrgGuid;q=name:SpaceName
```

***Reasons for using different api's for fetching the roles of Space :***  

Since the /v2/spaces only provides ```developer_guid``` query parameter and /v2/spaces queries are much faster than /v2/users query so we decided to use the available
query parameter from /v2/spaces and for rest of the two we used /v2/users. Mainly this was used to optimize the query and make the response much faster.
