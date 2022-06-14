# The Obligatory Lambda Challenge

```
The Obligatory Lambda Challenge
300

Spoiler: It's not actually Lambda.

A colleague accidentally pasted some credentials to the wrong chat, but didn't change them after deleting the message. See if you can figure out what they go to.

username: 556973dd-ae65-499a-b19f-fafdc42d1884 tenant: 601fd801-c161-434e-add7-82dd3581b8cb password: qum8Q~P-nsLdUotALCCCYWM.5-~URbq3wW2-6aU9
```

We already have the username, tenant and password for this one. And the `Spoiler: It's not actually Lambda` might point to `AWS Lambda`. And since it's not Lambda, perhaps it's `Azure`? Trying to use the `Azure CLI` to login with the given credentials:
https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal

```
❯ az login --service-principal -u 556973dd-ae65-499a-b19f-fafdc42d1884 -p qum8Q~P-nsLdUotALCCCYWM.5-~URbq3wW2-6aU9 --tenant 601fd801-c161-434e-add7-82dd3581b8cb
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "601fd801-c161-434e-add7-82dd3581b8cb",
    "id": "5114ad5f-eba0-48cb-ac20-70d475fff746",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Azure subscription 1",
    "state": "Enabled",
    "tenantId": "601fd801-c161-434e-add7-82dd3581b8cb",
    "user": {
      "name": "556973dd-ae65-499a-b19f-fafdc42d1884",
      "type": "servicePrincipal"
    }
  }
]
```

Looks like this worked! Checking of there's any resources hooked up to this account:
https://docs.microsoft.com/en-us/cli/azure/resource?view=azure-cli-latest#az-resource-list
```
❯ az resource list
[
  {
    "changedTime": "2022-04-22T18:58:02.918528+00:00",
    "createdTime": "2022-04-20T16:43:40.121886+00:00",
    "extendedLocation": null,
    "id": "/subscriptions/5114ad5f-eba0-48cb-ac20-70d475fff746/resourceGroups/ctf/providers/Microsoft.Web/sites/tenable-ctf",
    "identity": null,
    "kind": "functionapp,linux",
    "location": "centralus",
    "managedBy": null,
    "name": "tenable-ctf",
    "plan": null,
    "properties": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "ctf",
    "sku": null,
    "tags": {
      "hidden-link: /app-insights-instrumentation-key": "2978f4fb-5240-4929-a847-b82ca86bb840",
      "hidden-link: /app-insights-resource-id": "/subscriptions/5114ad5f-eba0-48cb-ac20-70d475fff746/resourceGroups/ctf/providers/microsoft.insights/components/tenable-ctf"
    },
    "type": "Microsoft.Web/sites"
  }
]
```

Using this to get more information on the resource using `functionapp`:
https://docs.microsoft.com/en-us/cli/azure/functionapp/config/appsettings?view=azure-cli-latest#az-functionapp-config-appsettings-list

```
❯ az functionapp config appsettings list --name tenable-ctf --resource-group ctf
…
  {
    "name": "AzureWebJobsStorage",
    "slotSetting": false,
    "value": "DefaultEndpointsProtocol=https;AccountName=ctfbab2;AccountKey=ecPf/DkAyBJVWBRgV1cA/Fhqn/EfyL1RC/2YB8MQ91elxxWvf0PWv3Wa9MeZAeJuRG6u93tA+XpGr4fseVq5nw==;EndpointSuffix=core.windows.net"
  },

…
```

Well, this looks kind of interesting! Using `Microsoft Azure Storage Explorer` to connect to the storage container:
https://azure.microsoft.com/en-us/features/storage-explorer/

Once opening the `Azure Storage Explorer` application, I chose the option `Storage account or service` and then `Account name and key` to connect.
```
Display name: Tenable (just a display name)
Account name: ctfbab2
Account key: ecPf/DkAyBJVWBRgV1cA/Fhqn/EfyL1RC/2YB8MQ91elxxWvf0PWv3Wa9MeZAeJuRG6u93tA+XpGr4fseVq5nw==
Storage domain: Azure
```

Then just using the `Azure Storage Exporer` to browse the container. After some browsing, I stumbled over `scm-latest-tenable-ctf.zip` under `scm-releases`! Downloaded the file and extracted it with `7z x scm-latest-tenable-ctf.zip`:
```
❯ tree
.
├── getFlag
│   ├── __init__.py
│   ├── function.json
│   └── sample.dat
├── hi
├── host.json
├── oryx-manifest.toml
├── requirements.txt
```

Checking out the `__init__.py`:
```python
❯ cat __init__.py 
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('id')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('id')

    if name:
        if name == "Robert Paulsen":
            return "flag{azure_functions_are_w31rd}"
        return func.HttpResponse(f"Nice try, but I happen to know {name}. You look nothing like them.")
    else:
        return func.HttpResponse(
             "You're very clever. How's that working out for you?\n\n I need to see some ID before I'm allowed to talk to you. Try giving me an 'id' parameter",
             status_code=200
        )
```

And there it is!
> flag{azure_functions_are_w31rd}
