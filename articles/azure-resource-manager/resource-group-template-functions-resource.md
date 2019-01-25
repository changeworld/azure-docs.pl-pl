---
title: Funkcje szablonu usługi Resource Manager platformy Azure — zasoby | Dokumentacja firmy Microsoft
description: Opisuje funkcje, aby użyć w szablonie usługi Azure Resource Manager można pobrać wartości dotyczące zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: tomfitz
ms.openlocfilehash: f5824dd575514329f2363535d35f5197f81ec2a0
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901058"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funkcje zasobów dla szablonów usługi Azure Resource Manager

Usługa Resource Manager zapewnia następujące funkcje w celu uzyskania wartości zasobu:

* [Lista *](#list)
* [dostawcy](#providers)
* [Odwołanie](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [Subskrypcja](#subscription)

Aby uzyskać wartości parametrów, zmienne lub bieżącego wdrożenia, zobacz [funkcji wartość wdrożeniowych](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>Lista *

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Składnia tej funkcji zależy od nazwy operacji listy. Każda implementacja zwraca wartości dla typu zasobu, który obsługuje operację listy. Nazwa operacji musi rozpoczynać się `list`. Niektóre typowego użycia są `listKeys` i `listSecrets`. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Yes |ciąg |Unikatowy identyfikator dla zasobu. |
| apiVersion |Yes |ciąg |Wersja interfejsu API stanu środowiska uruchomieniowego zasobu. Zazwyczaj w formacie **rrrr mm-dd**. |
| functionValues |Nie |obiekt | Obiekt, który zawiera wartości dla funkcji. Podaj tylko ten obiekt funkcji, które obsługują odbieranie obiekt o wartości parametrów, takich jak **listAccountSas** na koncie magazynu. | 

### <a name="implementations"></a>Implementacje

Możliwości wykorzystania listy * są wyświetlane w poniższej tabeli.

| Typ zasobu | Nazwa funkcji |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | listGatewayStatus |
| Microsoft.Automation/automationAccounts | klucze list |
| Microsoft.AzureStack/registrations/products | listDetails |
| Microsoft.Batch/batchAccounts | klucze list |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.BingMaps/mapApis | listSecrets |
| Microsoft.BingMaps/mapApis | listSingleSignOnToken |
| Microsoft.Cache/redis | klucze list |
| Microsoft.Cache/redis | listUpgradeNotifications |
| Microsoft.CognitiveServices/accounts | klucze list |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries | listBuildSourceUploadUrl |
| Microsoft.ContainerRegistry/registries | listCredentials |
| Microsoft.ContainerRegistry/registries | listPolicies |
| Microsoft.ContainerRegistry/registries | listUsages |
| Microsoft.ContainerRegistry/registries/runs | listLogSasUrl |
| Microsoft.ContainerRegistry/registries/tasks | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | listEvents |
| Microsoft.ContainerService/managedClusters/accessProfiles | listCredential |
| Microsoft.ContainerService/managedClusters | listClusterAdminCredential |
| Microsoft.ContainerService/managedClusters | listClusterUserCredential |
| Microsoft.ContentModerator/applications | listSecrets |
| Microsoft.ContentModerator/applications | listSingleSignOnToken |
| Microsoft.ContentModerator | listCommunicationPreference |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | listauthkeys |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | listSasTokens |
| Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys | klucze list |
| Microsoft.Devices/elasticPools/iotHubTenants | klucze list |
| Microsoft.Devices/iotHubs/iotHubKeys | klucze list |
| Microsoft.Devices/iotHubs | klucze list |
| Microsoft.Devices/provisioningServices/keys | klucze list |
| Microsoft.Devices/provisioningServices | klucze list |
| Microsoft.DevSpaces/controllers | listConnectionDetails |
| Microsoft.DevTestLab/labs | ListVhds |
| Microsoft.DevTestLab/labs/schedules | ListApplicable |
| Microsoft.DevTestLab/labs/users/serviceFabrics | ListApplicableSchedules |
| Microsoft.DevTestLab/labs/virtualMachines | ListApplicableSchedules |
| Microsoft.DocumentDB/databaseAccounts | listConnectionStrings |
| Microsoft.DocumentDB/databaseAccounts | klucze list |
| Microsoft.DomainRegistration | listDomainRecommendations |
| Microsoft.DomainRegistration/topLevelDomains | listAgreements |
| Microsoft.EventGrid/topics | klucze list |
| Microsoft.EventHub/namespaces/authorizationRules | klucze list |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | klucze list |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | klucze list |
| Microsoft.ImportExport/jobs | listBitLockerKeys |
| Microsoft.Insights | ListMigrationDate |
| Microsoft.LabServices/users | ListEnvironments |
| Microsoft.LabServices/users | ListLabs |
| Microsoft.LocationBasedServices/accounts | klucze list |
| Microsoft.LocationServices/accounts | klucze list |
| Microsoft.Logic/integrationAccounts/agreements | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/assemblies | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts | listCallbackUrl |
| Microsoft.Logic/integrationAccounts | listKeyVaultKeys |
| Microsoft.Logic/integrationAccounts/maps | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/partners | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/schemas | listContentCallbackUrl |
| Microsoft.Logic/workflows/accessKeys | list |
| Microsoft.Logic/workflows | listCallbackUrl |
| Microsoft.Logic/workflows | listSwagger |
| Microsoft.Logic/workflows/runs/actions | listExpressionTraces |
| Microsoft.Logic/workflows/runs/actions/repetitions | listExpressionTraces |
| Microsoft.Logic/workflows/triggers | listCallbackUrl |
| Microsoft.Logic/workflows/versions/triggers | listCallbackUrl |
| Microsoft.MachineLearning/webServices | klucze list |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningCompute/operationalizationClusters | klucze list |
| Microsoft.MachineLearningServices/workspaces/computes | klucze list |
| Microsoft.MachineLearningServices/workspaces | klucze list |
| Microsoft.Maps/accounts | klucze list |
| Microsoft.MarketplaceApps/ClassicDevServices | listSecrets |
| Microsoft.MarketplaceApps/ClassicDevServices | listSingleSignOnToken |
| Microsoft.Media/mediaservices/assets | listContainerSas |
| Microsoft.Media/mediaservices/assets | listStreamingLocators |
| Microsoft.Media/mediaservices/streamingLocators | listContentKeys |
| Microsoft.Media/mediaservices/streamingLocators | listPaths |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| microsoft.network/vpngateways | listvpnconnectionshealth |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | klucze list |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | klucze list |
| Microsoft.OperationalInsights/workspaces | klucze list |
| Microsoft.OperationalInsights/workspaces | klucze list |
| Microsoft.PolicyInsights/remediations | listDeployments |
| Microsoft.Relay/namespaces/authorizationRules | klucze list |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | klucze list |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | klucze list |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | klucze list |
| Microsoft.SaaS/saasresources | listaccesstoken |
| Microsoft.Search/searchServices | listAdminKeys |
| Microsoft.Search/searchServices | listQueryKeys |
| Microsoft.ServiceBus/namespaces/authorizationRules | klucze list |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | klucze list |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | klucze list |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | klucze list |
| Microsoft.SignalRService/SignalR | listFeatures |
| Microsoft.SignalRService/SignalR | klucze list |
| Microsoft.Storage/storageAccounts | listAccountSas |
| Microsoft.Storage/storageAccounts | klucze list |
| Microsoft.Storage/storageAccounts | listServiceSas |
| Microsoft.StorSimple/managers/devices | listFailoverSets |
| Microsoft.StorSimple/managers/devices | listFailoverTargets |
| Microsoft.StorSimple/managers | listActivationKey |
| Microsoft.StorSimple/managers | listPublicEncryptionKey |
| Microsoft.Web/apimanagementaccounts/APIs/Connections | listconnectionkeys |
| Microsoft.Web/apimanagementaccounts/APIs/Connections | listsecrets |
| Microsoft.Web/connectionGateways | ListStatus |
| Microsoft.Web/Connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/sites/backups | list |
| Microsoft.Web/sites/config | list |
| Microsoft.Web/Sites/Functions | listsecrets |
| Microsoft.Web/Sites/hybridconnectionnamespaces/relays | klucze list |
| Microsoft.Web/Sites | listsyncfunctiontriggerstatus |
| microsoft.web/sites/slots/backups | list |
| Microsoft.Web/sites/slots/config | list |
| Microsoft.Web/Sites/Slots/Functions | listsecrets |


### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest zależna od funkcji listy, której używasz. Na przykład klucze listy dla konta magazynu zwraca następujący format:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Inne funkcje listy mają różne formaty zwrotu. Aby wyświetlić format funkcji, dołączyć sekcję danych wyjściowych przedstawiony przykładowy szablon. 

### <a name="remarks"></a>Uwagi

[Sygnatury dostępu Współdzielonego listy konta](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) operacja wymaga parametrów treści żądania, takie jak *signedExpiry*. Aby użyć tej funkcji w szablonie, Udostępnij obiekt treści wartości parametrów.

Aby ustalić, typów zasobów, które mają operacja listy, dostępne są następujące opcje:

* Widok [operacji interfejsu API REST](/rest/api/) dla dostawcy zasobów i wygląd na potrzeby operacji listy. Na przykład konta magazynu mają [operacji listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Użyj [polecenia Get](/powershell/module/azurerm.resources/get-azurermprovideroperation) polecenia cmdlet programu PowerShell. Poniższy przykład pobiera wszystkie operacje dotyczące list dla kont magazynu:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Użyj następującego polecenia wiersza polecenia platformy Azure, aby filtrować Wyświetl listę operacji:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Określ zasób, używając nazwy zasobu lub [funkcja resourceId](#resourceid). Podczas korzystania z tej funkcji, w tym samym szablonie, który wdraża przywoływany zasób, należy użyć nazwy zasobu.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) przedstawia sposób zwrócenia klucze podstawowe i pomocnicze z konta magazynu w sekcji danych wyjściowych. Zwraca token sygnatury dostępu Współdzielonego dla konta magazynu. Aby uzyskać token, przekazuje obiekt listAccountSas funkcji. W tym przykładzie jest przeznaczona do korzystania z funkcji listy. Zazwyczaj można będzie użyć tokenu sygnatury dostępu Współdzielonego w wartości zasobów zamiast zwracanie go jako wartość wyjściową. Wartości wyjściowe są zapisywane w historii wdrożenia i nie są bezpieczne. Należy określić czas wygaśnięcia w przyszłości do pomyślnego wdrożenia.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
``` 

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storagename=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storagename <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>dostawcy
`providers(providerNamespace, [resourceType])`

Zwraca informacje o dostawcy zasobów i jego obsługiwane typy zasobów. Jeśli nie podasz zasobu o typie, funkcja zwraca obsługiwane typy dla dostawcy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |ciąg |Namespace dostawcy |
| Typ zasobu |Nie |ciąg |Typ zasobu w ramach określonego obszaru nazw. |

### <a name="return-value"></a>Wartość zwracana

Każdego obsługiwanego typu, jest zwracany w następującym formacie: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Kolejność zwracanych wartości tablicy nie jest gwarantowana.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) pokazuje, jak korzystać z funkcji dostawcy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Aby uzyskać **Microsoft.Web** dostawcy zasobów i **witryn** typ zasobu w poprzednim przykładzie zwracany obiekt w następującym formacie:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>Odwołanie
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Zwraca obiekt reprezentujący stan czasu wykonywania zasobu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Yes |ciąg |Nazwa lub identyfikator zasobu. |
| apiVersion |Nie |ciąg |Wersja interfejsu API określonego zasobu. Ten parametr należy uwzględnić, jeśli zasób nie jest zainicjowana obsługa administracyjna w obrębie tego samego szablonu. Zazwyczaj w formacie **rrrr mm-dd**. |
| "Full" |Nie |ciąg |Wartość określająca, czy należy zwrócić obiekt wszystkich zasobów. Jeśli nie określisz `'Full'`, zwracany jest tylko obiekt do właściwości zasobu. Pełny obiekt zawiera wartości, takie jak identyfikator zasobu i lokalizacji. |

### <a name="return-value"></a>Wartość zwracana

Każdy typ zasobu zwraca różne właściwości odwołanie funkcji. Funkcja nie zwraca jednego, wstępnie zdefiniowany format. Ponadto zwrócona wartość różni się zależnie od tego, czy określony obiekt pełne. Aby wyświetlić właściwości dla typu zasobu, należy przywrócić obiektu w sekcji danych wyjściowych, jak pokazano w przykładzie.

### <a name="remarks"></a>Uwagi

Funkcja odwołanie pobiera stan środowiska uruchomieniowego wdrożonej wcześniej zasobów lub zasobu, wdrożonych w bieżącym szablonie. W tym artykule przedstawiono przykłady oba scenariusze. Podczas odwoływania się do zasobów w bieżącym szablonie, podaj nazwę zasobu jako parametr. Podczas odwoływania się do wcześniej wdrożony zasób, należy podać wersję interfejsu API i identyfikator zasobu dla zasobu. Należy określić prawidłowe wersje interfejsu API dla zasobu w [odwołanie do szablonu](/azure/templates/).

Odwołanie funkcji należy używać tylko w właściwości definicji zasobu i sekcję danych wyjściowych szablonu lub wdrożenia.

Za pomocą funkcji odwołania, niejawnie Deklarujesz, jeden zasób jest zależny od innego zasobu, jeśli przywoływany zasób jest obsługiwana w ramach tego samego szablonu i odwołania do zasobu według jego nazwy (a nie identyfikator zasobu). Nie musisz również użyć właściwości dependsOn. Funkcja nie jest obliczane, dopóki nie zakończy się przywoływany zasób wdrożenia.

Aby wyświetlić nazwy właściwości i wartości dla typu zasobu, należy utworzyć szablon, który zwraca obiekt, w sekcji danych wyjściowych. W przypadku istniejącego zasobu tego typu szablonu zwraca obiekt bez wdrażania wszystkie nowe zasoby. 

Zazwyczaj można użyć **odwołania** funkcja zwraca określoną wartość z obiektu, takie jak identyfikator URI punktu końcowego obiektu blob lub w pełni kwalifikowaną nazwę domeny.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Użyj `'Full'` potrzebne wartości zasobów, które nie są częścią schematu właściwości. Na przykład aby ustawić zasady dostępu magazynu kluczy, pobieranie właściwości tożsamości dla maszyny wirtualnej.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Aby uzyskać kompletny przykład powyższy szablon, zobacz [Windows do usługi Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Podobny przykład jest dostępny dla [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) wdraża zasobu, a następnie odwołuje się do tego zasobu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Powyższy przykład zwraca dwa obiekty. Obiekt właściwości znajduje się w następującym formacie:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Pełny obiekt znajduje się w następującym formacie:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odwołuje się do konta magazynu, który nie jest wdrożony w tym szablonie. Konto magazynu już istnieje w ramach tej samej subskrypcji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageResourceGroup=<rg-for-storage> storageAccountName=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageResourceGroup <rg-for-storage> -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Zwraca obiekt, który reprezentuje bieżącą grupę zasobów. 

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Uwagi

`resourceGroup()` Nie można użyć funkcji w szablonie, który jest [wdrożone na poziomie subskrypcji](deploy-to-subscription.md). Można można używać tylko w szablonach, które są wdrożone w grupie zasobów.

Typowym zastosowaniem funkcji resourceGroup jest do tworzenia zasobów w tej samej lokalizacji co grupa zasobów. W poniższym przykładzie użyto lokalizacji grupy zasobów, aby przypisać lokalizacji dla witryny sieci web.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) zwraca właściwości grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Powyższy przykład zwraca obiekt, w następującym formacie:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Zwraca unikatowy identyfikator zasobu. Aby użyć tej funkcji, jeśli nazwa zasobu jest niejednoznaczny lub nie jest aprowizowany w ramach tego samego szablonu. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (format identyfikatora GUID w) |Wartością domyślną jest bieżąca subskrypcja. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej subskrypcji. |
| resourceGroupName |Nie |ciąg |Wartość domyślna to bieżącej grupie zasobów. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej grupie zasobów. |
| Typ zasobu |Yes |ciąg |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Yes |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny zasobu Nazwa segment Jeśli zasób jest zagnieżdżony. |

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Gdy jest używane z [wdrażania na poziomie subskrypcji](deploy-to-subscription.md), `resourceId()` funkcji można pobrać jedynie identyfikator zasobami wdrożonymi na tym samym poziomie. Na przykład możesz uzyskać identyfikator definicji zasad lub definicji roli, ale nie identyfikator konta magazynu. W przypadku wdrożeń w grupie zasobów przeciwieństwo ma wartość true. Nie można pobrać Identyfikatora zasobu zasobów wdrożonych na poziomie subskrypcji.

Wartości parametrów, które określisz, zależą od tego, czy zasób jest w tej samej subskrypcji i grupie zasobów co bieżącego wdrożenia. Aby uzyskać identyfikator zasobu dla konta magazynu w tej samej subskrypcji i grupie zasobów, użyj:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla konta magazynu w tej samej subskrypcji, ale inną grupę zasobów, użyj:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla konta magazynu w innej subskrypcji i grupie zasobów, użyj:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla bazy danych w innej grupie zasobów, należy użyć:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Aby uzyskać identyfikator zasobu zasobów poziom subskrypcji, w przypadku wdrażania w zakresie subskrypcji, użyj:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Często należy użyć tej funkcji, korzystając z konta magazynu lub sieci wirtualnej w grupie zasobów alternatywne. Poniższy przykład pokazuje, jak łatwo można używać zasobów z grupy zasobów zewnętrznych:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) zwraca identyfikator zasobu dla konta magazynu w grupie zasobów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| sameRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Ciąg | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>subskrypcja
`subscription()`

Zwraca szczegółowe informacje o subskrypcji dla bieżącego wdrożenia. 

### <a name="return-value"></a>Wartość zwracana

Funkcja zwraca następujący format:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) przedstawia funkcję subskrypcji o nazwie w sekcję danych wyjściowych. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

