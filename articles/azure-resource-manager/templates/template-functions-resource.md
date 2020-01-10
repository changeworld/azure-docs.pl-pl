---
title: Funkcje szablonu — zasoby
description: Opisuje funkcje, aby użyć w szablonie usługi Azure Resource Manager można pobrać wartości dotyczące zasobów.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 2dd55be9f9daa25662903fbb1bd0c2c6666974b3
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708569"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funkcje zasobów dla szablonów usługi Azure Resource Manager

Usługa Resource Manager zapewnia następujące funkcje w celu uzyskania wartości zasobu:

* [extensionResourceId](#extensionresourceid)
* [Lista *](#list)
* [dostawcy](#providers)
* [Odwołanie](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [Subskrypcja](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Aby uzyskać wartości parametrów, zmienne lub bieżącego wdrożenia, zobacz [funkcji wartość wdrożeniowych](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Zwraca identyfikator zasobu dla [zasobu rozszerzenia](extension-resource-types.md), który jest typem zasobu, który jest stosowany do innego zasobu, aby można go było dodać do jego możliwości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceId |Tak |string |Identyfikator zasobu dla zasobu, do którego zastosowano zasób rozszerzenia. |
| resourceType |Tak |string |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Tak |string |Nazwa zasobu. |
| resourceName2 |Nie |string |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Podstawowy format identyfikatora zasobu zwracanego przez tę funkcję to:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Segment zakresu zależy od rozszerzonego zasobu.

Gdy zasób rozszerzenia zostanie zastosowany do **zasobu**, identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **grupy zasobów**, format jest:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **subskrypcji**, format jest:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **grupy zarządzania**, format jest:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>przykład extensionResourceId

Poniższy przykład zwraca identyfikator zasobu dla blokady grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>staw

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Składnia tej funkcji różni się od nazwy operacji na liście. Każda implementacja zwraca wartości dla typu zasobu, który obsługuje operację listy. Nazwa operacji musi rozpoczynać się od `list`. Niektóre typowe zastosowania są `listKeys` i `listSecrets`. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Tak |string |Unikatowy identyfikator dla zasobu. |
| apiVersion |Tak |string |Wersja interfejsu API stanu środowiska uruchomieniowego zasobu. Zazwyczaj w formacie **rrrr mm-dd**. |
| functionValues |Nie |obiekt | Obiekt, który zawiera wartości dla funkcji. Podaj tylko ten obiekt funkcji, które obsługują odbieranie obiekt o wartości parametrów, takich jak **listAccountSas** na koncie magazynu. Przykład przekazywania wartości funkcji przedstawiono w tym artykule. | 

### <a name="valid-uses"></a>Prawidłowe zastosowania

Funkcji list można używać tylko we właściwościach definicji zasobu i w sekcji dane wyjściowe szablonu lub wdrożenia. Gdy jest używany z [iteracją właściwości](create-multiple-instances.md#property-iteration), można użyć funkcji listy dla `input`, ponieważ wyrażenie jest przypisane do właściwości zasobów. Nie można ich używać z `count`, ponieważ liczba musi być określona przed rozliczeniem funkcji list.

### <a name="implementations"></a>Implementacje

W poniższej tabeli przedstawiono możliwe zastosowania list *.

| Typ zasobu | Nazwa funkcji |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [klucze list](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. Batchai Job/obszary robocze/eksperymenty/zadania | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. łańcucha bloków/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. łańcucha bloków/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [klucze list](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [klucze list](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/rejestry/elementy webhook | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/zadania | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft. DataFactory/Factors/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. dataudział/konta/udziały | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) | 
| Microsoft. datashare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. datashare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. datashare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [klucze list](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domeny | [klucze list](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/tematy | [klucze list](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/klastry/bazy danych | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/schematy | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/przepływy pracy/uruchomienia/akcje | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/przepływy pracy/wyzwalacze | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/przepływy pracy/wersje/wyzwalacze | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/obszary robocze | listworkspacekeys |
| Microsoft. MachineLearningServices/obszary robocze/obliczenia | [klucze list](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/obszary robocze/obliczenia | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [klucze list](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/konta | [klucze list](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/zasoby | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/zasoby | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [klucze list](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft. Relay/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/przestrzenie nazw/kolejki/reguł autoryzacji | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/menedżerowie | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/menedżerowie | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| Microsoft. Web/Connections | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/lokalizacje | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia | listsecrets |
| Microsoft. Web/Sites/kopie zapasowe | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft. Web/Sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft. Web/Sites/Functions | [listkeys]()
| Microsoft. Web/Sites/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/Przekaźniki | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| Microsoft. Web/witryny | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/Slots/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| Microsoft. Web/Sites/gniazda/kopie zapasowe | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft. Web/Sites/szczeliny/konfiguracja | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| Microsoft. Web/Sites/Slots/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Aby ustalić, typów zasobów, które mają operacja listy, dostępne są następujące opcje:

* Widok [operacji interfejsu API REST](/rest/api/) dla dostawcy zasobów i wygląd na potrzeby operacji listy. Na przykład konta magazynu mają [operacji listKeys](/rest/api/storagerp/storageaccounts).
* Użyj polecenia cmdlet programu PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Poniższy przykład pobiera wszystkie operacje dotyczące list dla kont magazynu:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Użyj następującego polecenia wiersza polecenia platformy Azure, aby filtrować Wyświetl listę operacji:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt różni się w zależności od używanej funkcji list. Na przykład listKeys dla konta magazynu zwraca następujący format:

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

Określ zasób, używając nazwy zasobu lub [funkcja resourceId](#resourceid). W przypadku używania funkcji list w tym samym szablonie, który wdraża przywoływany zasób, należy użyć nazwy zasobu.

Jeśli używasz funkcji **list** w zasobie, który jest wdrażany warunkowo, funkcja zostanie oceniona, nawet jeśli zasób nie zostanie wdrożony. Występuje błąd, jeśli funkcja **list** odwołuje się do zasobu, który nie istnieje. Użyj funkcji **if** , aby upewnić się, że funkcja jest obliczana tylko wtedy, gdy zasób jest wdrażany. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa elementu if i z użyciem warunkowo wdrożonego zasobu.

### <a name="list-example"></a>Przykład listy

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) przedstawia sposób zwrócenia klucze podstawowe i pomocnicze z konta magazynu w sekcji danych wyjściowych. Zwraca token sygnatury dostępu Współdzielonego dla konta magazynu. 

Aby uzyskać token SAS, należy przekazać obiekt przez czas wygaśnięcia. Czas wygaśnięcia musi przypadać w przyszłości. W tym przykładzie jest przeznaczona do korzystania z funkcji listy. Zazwyczaj można będzie użyć tokenu sygnatury dostępu Współdzielonego w wartości zasobów zamiast zwracanie go jako wartość wyjściową. Wartości wyjściowe są zapisywane w historii wdrożenia i nie są bezpieczne.

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

## <a name="providers"></a>dostawcy

```json
providers(providerNamespace, [resourceType])
```

Zwraca informacje o dostawcy zasobów i jego obsługiwane typy zasobów. Jeśli nie podasz zasobu o typie, funkcja zwraca obsługiwane typy dla dostawcy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Tak |string |Namespace dostawcy |
| resourceType |Nie |string |Typ zasobu w ramach określonego obszaru nazw. |

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

### <a name="providers-example"></a>Przykład dostawcy

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

## <a name="reference"></a>Odwołanie

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Zwraca obiekt reprezentujący stan czasu wykonywania zasobu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Tak |string |Nazwa lub identyfikator zasobu. Podczas odwoływania się do zasobów w bieżącym szablonie, podaj nazwę zasobu jako parametr. W przypadku odwoływania się do wcześniej wdrożonego zasobu podaj identyfikator zasobu. |
| apiVersion |Nie |string |Wersja interfejsu API określonego zasobu. Ten parametr należy uwzględnić, jeśli zasób nie jest zainicjowana obsługa administracyjna w obrębie tego samego szablonu. Zazwyczaj w formacie **rrrr mm-dd**. Aby uzyskać prawidłowe wersje interfejsu API dla zasobu, zobacz [Dokumentacja szablonu](/azure/templates/). |
| "Full" |Nie |string |Wartość określająca, czy należy zwrócić obiekt wszystkich zasobów. Jeśli nie określisz `'Full'`, zwracany jest tylko obiekt do właściwości zasobu. Pełny obiekt zawiera wartości, takie jak identyfikator zasobu i lokalizacji. |

### <a name="return-value"></a>Wartość zwracana

Każdy typ zasobu zwraca różne właściwości odwołanie funkcji. Funkcja nie zwraca jednego, wstępnie zdefiniowany format. Ponadto zwrócona wartość różni się zależnie od tego, czy określony obiekt pełne. Aby wyświetlić właściwości dla typu zasobu, należy przywrócić obiektu w sekcji danych wyjściowych, jak pokazano w przykładzie.

### <a name="remarks"></a>Uwagi

Funkcja odwołanie pobiera stan środowiska uruchomieniowego wdrożonej wcześniej zasobów lub zasobu, wdrożonych w bieżącym szablonie. W tym artykule przedstawiono przykłady oba scenariusze.

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

### <a name="valid-uses"></a>Prawidłowe zastosowania

Odwołanie funkcji należy używać tylko w właściwości definicji zasobu i sekcję danych wyjściowych szablonu lub wdrożenia. Gdy jest używany z [iteracją właściwości](create-multiple-instances.md#property-iteration), można użyć funkcji reference dla `input`, ponieważ wyrażenie jest przypisane do właściwości Resource. Nie można użyć go z `count`, ponieważ liczba musi być określona przed rozwiązanym funkcją referencyjną.

Nie można użyć funkcji Reference w danych wyjściowych [szablonu zagnieżdżonego](linked-templates.md#nested-template) , aby zwrócić zasób wdrożony w szablonie zagnieżdżonym. Zamiast tego należy użyć [połączonego szablonu](linked-templates.md#linked-template).

Jeśli używasz funkcji **Reference** w zasobie, który jest wdrażany warunkowo, funkcja jest oceniana, nawet jeśli zasób nie zostanie wdrożony.  Występuje błąd, jeśli funkcja **referencyjna** odwołuje się do zasobu, który nie istnieje. Użyj funkcji **if** , aby upewnić się, że funkcja jest obliczana tylko wtedy, gdy zasób jest wdrażany. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa if i Reference z wdrożonym warunkowo zasobem.

### <a name="implicit-dependency"></a>Niejawna zależność

Za pomocą funkcji odwołania, niejawnie Deklarujesz, jeden zasób jest zależny od innego zasobu, jeśli przywoływany zasób jest obsługiwana w ramach tego samego szablonu i odwołania do zasobu według jego nazwy (a nie identyfikator zasobu). Nie musisz również użyć właściwości dependsOn. Funkcja nie jest obliczane, dopóki nie zakończy się przywoływany zasób wdrożenia.

### <a name="resource-name-or-identifier"></a>Nazwa lub identyfikator zasobu

W przypadku odwoływania się do zasobu, który jest wdrożony w tym samym szablonie, podaj nazwę zasobu.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

W przypadku odwoływania się do zasobu, który nie jest wdrożony w tym samym szablonie, podaj identyfikator zasobu.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Aby uniknąć niejednoznaczności dotyczącej danego zasobu, możesz podać w pełni kwalifikowaną nazwę zasobu.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

W przypadku konstruowania w pełni kwalifikowanego odwołania do zasobu kolejność łączenia segmentów z typu i nazwy nie jest po prostu połączeniem obu. Zamiast tego, po przestrzeni nazw, należy użyć sekwencji par *typu/nazwa* od najmniej określonych do najbardziej szczegółowych:

**{Resource-Provider-Namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-resource-name}]**

Przykład:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` jest prawidłowy `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` jest niepoprawny

### <a name="reference-example"></a>Przykład odwołania

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

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Zwraca obiekt, który reprezentuje bieżącą grupę zasobów. 

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Właściwość **zarządzane** jest zwracana tylko dla grup zasobów zawierających zasoby, które są zarządzane przez inną usługę. W przypadku aplikacji zarządzanych, datakostki i AKS wartość właściwości jest identyfikator zasobu zasobu zarządzania.

### <a name="remarks"></a>Uwagi

Funkcji `resourceGroup()` nie można użyć w szablonie, który został [wdrożony na poziomie subskrypcji](deploy-to-subscription.md). Może być używany tylko w szablonach wdrożonych w grupie zasobów. Możesz użyć funkcji `resourceGroup()` w [połączonym lub zagnieżdżonym szablonie (z zakresem wewnętrznym)](linked-templates.md) , który jest przeznaczony dla grupy zasobów, nawet jeśli szablon nadrzędny został wdrożony w subskrypcji. W tym scenariuszu szablon połączony lub zagnieżdżony jest wdrażany na poziomie grupy zasobów. Aby uzyskać więcej informacji na temat określania docelowych grup zasobów w ramach wdrożenia na poziomie subskrypcji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

Typowym zastosowaniem funkcji resourceGroup jest do tworzenia zasobów w tej samej lokalizacji co grupa zasobów. Poniższy przykład używa lokalizacji grupy zasobów dla domyślnej wartości parametru.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Można również użyć funkcji grupy zasobów, aby zastosować do zasobu Tagi ze źródła danych. Aby uzyskać więcej informacji, zobacz [stosowanie tagów z grupy zasobów](../management/tag-resources.md#apply-tags-from-resource-group).

W przypadku użycia szablonów zagnieżdżonych do wdrożenia w wielu grupach zasobów można określić zakres oceniania funkcji ResourceManager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Przykład grupy zasobów

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
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Zwraca unikatowy identyfikator zasobu. Aby użyć tej funkcji, jeśli nazwa zasobu jest niejednoznaczny lub nie jest aprowizowany w ramach tego samego szablonu. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (format identyfikatora GUID w) |Wartością domyślną jest bieżąca subskrypcja. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej subskrypcji. |
| resourceGroupName |Nie |string |Wartość domyślna to bieżącej grupie zasobów. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej grupie zasobów. |
| resourceType |Tak |string |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Tak |string |Nazwa zasobu. |
| resourceName2 |Nie |string |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

W przypadku użycia w ramach [wdrożenia na poziomie subskrypcji](deploy-to-subscription.md)identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Aby uzyskać identyfikator w innych formatach, zobacz:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Uwagi

Liczba parametrów, które należy podać, zależy od tego, czy zasób jest zasobem nadrzędnym, czy podrzędnym, oraz czy zasób znajduje się w tej samej subskrypcji lub grupie zasobów.

Aby uzyskać identyfikator zasobu dla zasobu nadrzędnego w tej samej subskrypcji i grupie zasobów, podaj typ i nazwę zasobu.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Aby uzyskać identyfikator zasobu dla zasobu podrzędnego, należy zwrócić uwagę na liczbę segmentów w typie zasobu. Podaj nazwę zasobu dla każdego segmentu typu zasobu. Nazwa segmentu odpowiada zasobowi, który istnieje dla tej części hierarchii.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Aby uzyskać identyfikator zasobu dla zasobu w tej samej subskrypcji, ale w innej grupie zasobów, podaj nazwę grupy zasobów.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla zasobu w innej subskrypcji i grupie zasobów, podaj identyfikator subskrypcji i nazwę grupy zasobów.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
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
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
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

### <a name="resource-id-example"></a>Przykład identyfikatora zasobu

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| sameRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Ciąg | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subskrypcja

```json
subscription()
```

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

### <a name="remarks"></a>Uwagi

W przypadku używania szablonów zagnieżdżonych do wdrażania w wielu subskrypcjach można określić zakres oceniania funkcji subskrypcji. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Przykład subskrypcji

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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie subskrypcji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (w formacie identyfikatora GUID) |Wartością domyślną jest bieżąca subskrypcja. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej subskrypcji. |
| resourceType |Tak |string |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Tak |string |Nazwa zasobu. |
| resourceName2 |Nie |string |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do pobierania identyfikatora zasobu dla zasobów [wdrożonych w subskrypcji](deploy-to-subscription.md) , a nie w grupie zasobów. Zwrócony identyfikator różni się od wartości zwracanej przez funkcję [ResourceID](#resourceid) przez nie uwzględniając wartości grupy zasobów.

### <a name="subscriptionresourceid-example"></a>przykład subscriptionResourceID

Poniższy szablon przypisuje wbudowaną rolę. Można wdrożyć je w grupie zasobów lub w ramach subskrypcji. Używa funkcji subscriptionResourceId, aby uzyskać identyfikator zasobu dla ról wbudowanych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie dzierżawy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceType |Tak |string |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Tak |string |Nazwa zasobu. |
| resourceName2 |Nie |string |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do pobierania identyfikatora zasobu dla zasobu, który jest wdrażany w dzierżawie. Zwrócony identyfikator różni się od wartości zwracanych przez inne funkcje identyfikatora zasobu, nie uwzględniając wartości grup zasobów lub subskrypcji.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](create-multiple-instances.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](deploy-powershell.md).

