---
title: Funkcje szablonu — zasoby
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pobierania wartości dotyczących zasobów.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e9e1d700282652304f0bede5e697ba8625f5a5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156297"
---
# <a name="resource-functions-for-arm-templates"></a>Funkcje zasobów dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje uzyskiwania wartości zasobów w szablonie usługi Azure Resource Manager (ARM):

* [extensionResourceId](#extensionresourceid)
* [lista*](#list)
* [Dostawców](#providers)
* [Odwołanie](#reference)
* [grupa zasobów](#resourcegroup)
* [Resourceid](#resourceid)
* [Subskrypcji](#subscription)
* [identyfikator zasobów subskrypcji](#subscriptionresourceid)
* [identyfikator źródłu zewnętrznego tenant](#tenantresourceid)

Aby uzyskać wartości z parametrów, zmiennych lub bieżącego wdrożenia, zobacz [Funkcje wartości wdrażania](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Zwraca identyfikator zasobu dla [zasobu rozszerzenia,](../management/extension-resource-types.md)który jest typem zasobu zastosowanym do innego zasobu w celu dodania go do jego możliwości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceId |Tak |ciąg |Identyfikator zasobu, do który zasób rozszerzenia jest stosowany. |
| resourceType |Tak |ciąg |Typ zasobu, w tym obszar nazw dostawcy zasobów. |
| nazwa zasobu1 |Tak |ciąg |Nazwa zasobu. |
| nazwa zasobu2 |Nie |ciąg |Następny segment nazwy zasobu, w razie potrzeby. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Podstawowym formatem identyfikatora zasobu zwróconego przez tę funkcję jest:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Segment zakresu zależy od rozszerzonego zasobu.

Gdy zasób rozszerzenia jest stosowany do **zasobu,** identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia jest stosowany do **grupy zasobów,** format jest:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia jest stosowany do **subskrypcji,** format jest:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia jest stosowany do **grupy zarządzania,** format jest:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId przykład

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

## <a name="list"></a>lista*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Składnia tej funkcji różni się w zależności od nazwy operacji listy. Każda implementacja zwraca wartości dla typu zasobu, który obsługuje operację listy. Nazwa operacji musi `list`zaczynać się od . Niektóre typowe zwyczaje są `listKeys` i `listSecrets`.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| nazwa zasobu lub zasóbIdentyfikator |Tak |ciąg |Unikatowy identyfikator zasobu. |
| apiVersion |Tak |ciąg |Wersja interfejsu API stanu środowiska wykonawczego zasobu. Zazwyczaj w formacie **yyyy-mm-dd**. |
| functionWartues |Nie |obiekt | Obiekt, który ma wartości dla funkcji. Podaj ten obiekt tylko dla funkcji, które obsługują odbieranie obiektu z wartościami parametrów, takich jak **listAccountSas** na koncie magazynu. Przykład przekazywania wartości funkcji jest pokazany w tym artykule. |

### <a name="valid-uses"></a>Prawidłowe zastosowania

Funkcje listy mogą być używane tylko we właściwościach definicji zasobu i sekcji dane wyjściowe szablonu lub wdrożenia. W przypadku użycia z [iteracją właściwości](copy-properties.md)można `input` użyć funkcji listy, ponieważ wyrażenie jest przypisane do właściwości zasobu. Nie można ich używać, `count` ponieważ liczba musi być określona przed rozpoznawaniem funkcji listy.

### <a name="implementations"></a>Implementacje

Możliwe zastosowania listy* przedstawiono w poniższej tabeli.

| Typ zasobu | Nazwa funkcji |
| ------------- | ------------- |
| Microsoft.AnalysisSługa/serwery | [listaGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Klawisze list |
| Microsoft.Automation/automationKontacje | [klawisze list](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchKonkludy | [klawisze listkey](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/obszary robocze/eksperymenty/zadania | [pliki listoutput](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainCzłonek | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainCzłonek/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Pamięć podręczna/redis firmy Microsoft.Cache | [klawisze list](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [klawisze list](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/rejestry | [listaBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/rejestry | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/rejestry | [listaUży](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listaAwentyny](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listaLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listaSzczegóły](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminDedential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listaCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/zadania | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | przyciski listauthkeys |
| Microsoft.DataFactory/fabryki/integracjarunruntimes | [przyciski listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listaSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/konta/udziały | [Synchronizacje list](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [listaSourceShareSynchronizationSettings listSourceShareSynchronizations](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [szczegóły synchronizowania list](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [Synchronizacje list](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [klawisze listkey](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [klawisze listkey](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi/kluczy | [klawisze listkey](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi | [klawisze listkey](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/laboratoria | [ListVhds ( ListVhds )](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/harmonograms | [ListaZastosowalne](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListaApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListaApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseKonta kont | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseKonta kont | [klawisze list](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listaDosainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listaPozycje](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domeny | [klawisze list](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/tematy | [klawisze list](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/przestrzenie nazw/autoryzacjaRule | [klawisze listkey](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [klawisze listkey](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [klawisze listkey](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/zadania | [klawisze listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Klastry/Bazy danych | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/użytkownicy | [ListaŚrodowiska](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/użytkownicy | [ListLabs ( ListLabs )](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationKontacje/umowy | [listaContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationKontacje/zestawy | [listaContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationKontacje | [listaCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationKontacje | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationKontacje/mapy | [listaContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationKonta/partnerzy | [listaContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationKontacje/schematy | [listaContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/przepływy pracy | [listaCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/przepływy pracy | [listaWygoda](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listaWyjaśniacze](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/powtórzenia | [listaWyjaśniacze](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/przepływy pracy/wyzwalacze | [listaCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listaCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [klawisze listkey](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Obszary robocze | klawisze przestrzeni listwork |
| Microsoft.MachineLearningServices/workspaces/computes | [klawisze list](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listaNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningUsługi/obszary robocze | [klawisze list](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft. | [klawisze list](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listaKontainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listSlodatory lokalizatorów](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLokalizatory | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLokalizatory | [ścieżka list](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | konfiguracje listIpCon |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [klawisze listkey](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [klawisze listkey](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/obszary robocze | [klawisze list](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/koryguje | [listaDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [klawisze listkey](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | klawisze listkey |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [klawisze listkey](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [klawisze listkey](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchSługs | [klawisze listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchSługs | [klawisze listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [klawisze listkey](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [klawisze listkey](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [klawisze listkey](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [klawisze listkey](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalrService/Signalr | [klawisze listkey](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageKondyje | [listaKontakonsa](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageKondyje | [klawisze listkey](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageKondyje | [lista UsługiSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/menedżerów/urządzeń | [listaFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/menedżerów/urządzeń | [listaFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/menedżerowie | [klawisz listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/menedżerowie | [listaPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | Stan list |
| microsoft.web/połączenia | linki do listconsents |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/lokalizacje | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | klawisze listconnection |
| microsoft.web/apimanagementaccounts/apis/connections | sekretarze list |
| microsoft.web/sites/kopia zapasowa | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config Microsoft.Web/sites/config Microsoft.Web/sites/config Microsoft. | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/witryny/funkcje | [klawisze listkey](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/witryny/funkcje | [sekretarze list](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [klawisze listkey](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/witryny | [listyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions microsoft.web/sites/slots/functions microsoft.web/sites/slots/functions microsoft. | [sekretarze list](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups microsoft.web/sites/slots/backups microsoft.web/sites/slots/backups microsoft. | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config Microsoft.Web/sites/slots/config Microsoft.Web/sites/slots/config Microsoft. | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions microsoft.web/sites/slots/functions microsoft.web/sites/slots/functions microsoft. | [sekretarze list](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Aby określić, które typy zasobów mają operację listy, dostępne są następujące opcje:

* Wyświetl [operacje interfejsu API REST](/rest/api/) dla dostawcy zasobów i poszukaj operacji listy. Na przykład konta magazynu mają [operację listKeys](/rest/api/storagerp/storageaccounts).
* Użyj polecenia cmdlet [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. W poniższym przykładzie pobiera wszystkie operacje listy dla kont magazynu:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Użyj następującego polecenia interfejsu wiersza polecenia platformy Azure, aby filtrować tylko operacje listy:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt zależy od używanej funkcji listy. Na przykład klawisze list dla konta magazynu zwraca następujący format:

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

Inne funkcje listy mają różne formaty zwracane. Aby wyświetlić format funkcji, dołącz ją do sekcji danych wyjściowych, jak pokazano w przykładowym szablonie.

### <a name="remarks"></a>Uwagi

Określ zasób przy użyciu nazwy zasobu lub [funkcji resourceId](#resourceid). W przypadku korzystania z funkcji listy w tym samym szablonie, który wdraża zasób, do którego istnieje odwołanie, należy użyć nazwy zasobu.

Jeśli używasz funkcji **listy** w zasobie, który jest warunkowo wdrożony, funkcja jest oceniana, nawet jeśli zasób nie jest wdrażany. Zostanie wyświetlony błąd, jeśli funkcja **listy** odwołuje się do zasobu, który nie istnieje. Użyj if **funkcji,** aby upewnić się, że funkcja jest oceniana tylko wtedy, gdy zasób jest wdrażany. Zobacz [if funkcji](template-functions-logical.md#if) dla przykładowego szablonu, który używa if i listy z warunkowo wdrożony zasób.

### <a name="list-example"></a>Przykład listy

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) pokazuje, jak zwrócić klucze podstawowe i pomocnicze z konta magazynu w sekcji dane wyjściowe. Zwraca również token sygnatury dostępu Współdzielonego dla konta magazynu.

Aby uzyskać token sygnatury dostępu Współdzielonego, przekazać obiekt na czas wygaśnięcia. Czas wygaśnięcia musi być w przyszłości. W tym przykładzie jest przeznaczony do pokazania, jak używać funkcji listy. Zazwyczaj należy użyć tokenu sygnatury dostępu Współdzielonego w wartości zasobu, a nie zwracać go jako wartość wyjściową. Wartości danych wyjściowych są przechowywane w historii wdrażania i nie są bezpieczne.

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

## <a name="providers"></a>dostawców

```json
providers(providerNamespace, [resourceType])
```

Zwraca informacje o dostawcy zasobów i obsługiwanych przez nie typach zasobów. Jeśli nie podasz typu zasobu, funkcja zwraca wszystkie obsługiwane typy dla dostawcy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Tak |ciąg |Przestrzeń nazw dostawcy |
| resourceType |Nie |ciąg |Typ zasobu w określonym obszarze nazw. |

### <a name="return-value"></a>Wartość zwracana

Każdy obsługiwany typ jest zwracany w następującym formacie:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Kolejność tablic zwracanych wartości nie jest gwarantowana.

### <a name="providers-example"></a>Przykład dostawców

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) pokazuje, jak korzystać z funkcji dostawcy:

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

W przypadku dostawcy zasobów **firmy Microsoft.Web** i typu zasobu **witryn** w poprzednim przykładzie zwraca obiekt w następującym formacie:

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

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Zwraca obiekt reprezentujący stan środowiska wykonawczego zasobu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| nazwa zasobu lub zasóbIdentyfikator |Tak |ciąg |Nazwa lub unikatowy identyfikator zasobu. Podczas odwoływania się do zasobu w bieżącym szablonie podaj tylko nazwę zasobu jako parametr. Podczas odwoływania się do wcześniej wdrożonego zasobu lub gdy nazwa zasobu jest niejednoznaczna, podaj identyfikator zasobu. |
| apiVersion |Nie |ciąg |Wersja interfejsu API określonego zasobu. Uwzględnij ten parametr, gdy zasób nie jest aprowizowany w ramach tego samego szablonu. Zazwyczaj w formacie **yyyy-mm-dd**. Aby uzyskać prawidłowe wersje interfejsu API dla zasobu, zobacz [odwołanie do szablonu](/azure/templates/). |
| "Pełna" |Nie |ciąg |Wartość określająca, czy ma zwrócić pełny obiekt zasobu. Jeśli nie określisz, `'Full'`zwracany jest tylko obiekt właściwości zasobu. Pełny obiekt zawiera wartości, takie jak identyfikator zasobu i lokalizacja. |

### <a name="return-value"></a>Wartość zwracana

Każdy typ zasobu zwraca różne właściwości funkcji referencyjnej. Funkcja nie zwraca pojedynczego, wstępnie zdefiniowanego formatu. Ponadto zwracana wartość różni się w zależności od tego, czy określono pełny obiekt. Aby wyświetlić właściwości typu zasobu, zwróć obiekt w sekcji dane wyjściowe, jak pokazano w przykładzie.

### <a name="remarks"></a>Uwagi

Funkcja referencyjna pobiera stan środowiska uruchomieniowego wcześniej wdrożonego zasobu lub zasobu wdrożonego w bieżącym szablonie. W tym artykule przedstawiono przykłady dla obu scenariuszy.

Zazwyczaj funkcja **odwołania** służy do zwracania określonej wartości z obiektu, takiego jak identyfikator URI punktu końcowego obiektu blob lub w pełni kwalifikowana nazwa domeny.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Użyj, `'Full'` gdy potrzebujesz wartości zasobów, które nie są częścią schematu właściwości. Na przykład, aby ustawić zasady dostępu magazynu kluczy, pobierz właściwości tożsamości dla maszyny wirtualnej.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
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

Funkcja odwołania może być używana tylko we właściwościach definicji zasobu i sekcji dane wyjściowe szablonu lub wdrożenia. W przypadku użycia z [iteracją właściwości](copy-properties.md)można `input` użyć funkcji odwołania, ponieważ wyrażenie jest przypisane do właściwości zasobu. Nie można go używać, `count` ponieważ liczba musi być określona przed rozpoznawaniem funkcji odwołania.

Nie można użyć funkcji odwołania w danych wyjściowych [szablonu zagnieżdżonego,](linked-templates.md#nested-template) aby zwrócić zasób wdrożony w szablonie zagnieżdżonego. Zamiast tego użyj [połączonego szablonu](linked-templates.md#linked-template).

Jeśli używasz funkcji **odwołania** w zasobie, który jest warunkowo wdrożony, funkcja jest oceniana, nawet jeśli zasób nie jest wdrażany.  Zostanie wyświetlony błąd, jeśli funkcja **odwołania** odwołuje się do zasobu, który nie istnieje. Użyj if **funkcji,** aby upewnić się, że funkcja jest oceniana tylko wtedy, gdy zasób jest wdrażany. Zobacz [if funkcji](template-functions-logical.md#if) dla przykładowego szablonu, który używa if i odwołania z warunkowo wdrożony zasób.

### <a name="implicit-dependency"></a>Zależność niejawna

Za pomocą funkcji odwołania, niejawnie zadeklarować, że jeden zasób zależy od innego zasobu, jeśli zasób, do którego istnieje odwołanie jest aprowizowana w ramach tego samego szablonu i odwołać się do zasobu przez jego nazwę (nie identyfikator zasobu). Nie trzeba również używać dependsOn właściwości. Funkcja nie jest oceniana, dopóki zasób, do którego istnieje odwołanie, nie zakończy wdrożenia.

### <a name="resource-name-or-identifier"></a>Nazwa lub identyfikator zasobu

Podczas odwoływania się do zasobu, który jest wdrażany w tym samym szablonie, podaj nazwę zasobu.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Podczas odwoływania się do zasobu, który nie jest wdrożony w tym samym szablonie, podaj identyfikator zasobu.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Aby uniknąć niejednoznaczności co do zasobu, do którego odwołujesz się, można podać w pełni kwalifikowany identyfikator zasobu.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Podczas konstruowania w pełni kwalifikowane odwołanie do zasobu, kolejność łączenia segmentów z typu i nazwy nie jest po prostu łączenie dwóch. Zamiast tego po obszarze nazw należy użyć sekwencji par *typu/nazwy* od najmniej specyficznych do najbardziej specyficznych:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Przykład:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`jest `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` poprawna nie jest poprawna

Aby uprościć tworzenie dowolnego identyfikatora zasobu, `resourceId()` należy użyć `concat()` funkcji opisanych w tym dokumencie zamiast funkcji.

### <a name="get-managed-identity"></a>Uzyskaj tożsamość zarządzana

[Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) są [typy zasobów rozszerzenia,](../management/extension-resource-types.md) które są tworzone niejawnie dla niektórych zasobów. Ponieważ tożsamość zarządzana nie jest jawnie zdefiniowana w szablonie, należy odwołać się do zasobu, do którego jest stosowana tożsamość. Służy `Full` do uzyskania wszystkich właściwości, w tym niejawnie utworzonej tożsamości.

Na przykład, aby uzyskać identyfikator dzierżawy dla tożsamości zarządzanej, która jest stosowana do zestawu skalowania maszyny wirtualnej, należy użyć:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Przykład referencyjny

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) wdraża zasób i odwołuje się do tego zasobu.

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

W poprzednim przykładzie zwraca dwa obiekty. Obiekt właściwości jest w następującym formacie:

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

Pełny obiekt jest w następującym formacie:

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

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odwołuje się do konta magazynu, które nie jest wdrażane w tym szablonie. Konto magazynu już istnieje w ramach tej samej subskrypcji.

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

Zwraca obiekt reprezentujący bieżącą grupę zasobów.

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

Właściwość **managedBy** jest zwracana tylko dla grup zasobów, które zawierają zasoby zarządzane przez inną usługę. W przypadku aplikacji zarządzanych, databricks i AKS wartość właściwości jest identyfikatorem zasobu zasobu zarządzającego.

### <a name="remarks"></a>Uwagi

Funkcji `resourceGroup()` nie można używać w szablonie [wdrożonym na poziomie subskrypcji](deploy-to-subscription.md). Może być używany tylko w szablonach, które są wdrażane w grupie zasobów. Funkcji można `resourceGroup()` użyć w [szablonie połączonym lub zagnieżdżonym (z zakresem wewnętrznym),](linked-templates.md) który jest przeznaczony dla grupy zasobów, nawet wtedy, gdy szablon nadrzędny jest wdrażany w ramach subskrypcji. W tym scenariuszu połączony lub zagnieżdżony szablon jest wdrażany na poziomie grupy zasobów. Aby uzyskać więcej informacji dotyczących kierowania na grupę zasobów we wdrożeniu na poziomie subskrypcji, zobacz [Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

Typowym zastosowaniem resourceGroup funkcja jest tworzenie zasobów w tej samej lokalizacji co grupa zasobów. W poniższym przykładzie użyto lokalizacji grupy zasobów dla domyślnej wartości parametru.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Za pomocą funkcji resourceGroup można również zastosować znaczniki z grupy zasobów do zasobu. Aby uzyskać więcej informacji, zobacz [Stosowanie znaczników z grupy zasobów](../management/tag-resources.md#apply-tags-from-resource-group).

Korzystając z szablonów zagnieżdżonych do wdrażania w wielu grupach zasobów, można określić zakres oceny funkcji resourceGroup. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Przykład grupy zasobów

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) zwraca właściwości grupy zasobów.

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

W poprzednim przykładzie zwraca obiekt w następującym formacie:

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

Zwraca unikatowy identyfikator zasobu. Tej funkcji należy użyć, gdy nazwa zasobu jest niejednoznaczna lub nie jest aprowizowana w ramach tego samego szablonu. Format zwróconego identyfikatora różni się w zależności od tego, czy wdrożenie odbywa się w zakresie grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |(w formacie GUID) |Wartość domyślna to bieżąca subskrypcja. Określ tę wartość, gdy trzeba pobrać zasób w innej subskrypcji. Podaj tę wartość tylko podczas wdrażania w zakresie grupy zasobów lub subskrypcji. |
| resourceGroupName |Nie |ciąg |Wartością domyślną jest bieżąca grupa zasobów. Określ tę wartość, gdy trzeba pobrać zasób w innej grupie zasobów. Podaj tę wartość tylko podczas wdrażania w zakresie grupy zasobów. |
| resourceType |Tak |ciąg |Typ zasobu, w tym obszar nazw dostawcy zasobów. |
| nazwa zasobu1 |Tak |ciąg |Nazwa zasobu. |
| nazwa zasobu2 |Nie |ciąg |Następny segment nazwy zasobu, w razie potrzeby. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Gdy szablon jest wdrażany w zakresie grupy zasobów, identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

W przypadku użycia we [wdrożeniu na poziomie subskrypcji](deploy-to-subscription.md)identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

W przypadku użycia we [wdrożeniu na poziomie grupy zarządzania](deploy-to-management-group.md) lub wdrożenia na poziomie dzierżawy identyfikator zasobu jest zwracany w następującym formacie:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Aby uzyskać identyfikator w innych formatach, zobacz:

* [extensionResourceId](#extensionresourceid)
* [identyfikator zasobów subskrypcji](#subscriptionresourceid)
* [identyfikator źródłu zewnętrznego tenant](#tenantresourceid)

### <a name="remarks"></a>Uwagi

Liczba parametrów, które można podać zależy od tego, czy zasób jest zasobem nadrzędnym lub podrzędnym i czy zasób znajduje się w tej samej subskrypcji lub grupie zasobów.

Aby uzyskać identyfikator zasobu nadrzędnego dla zasobu nadrzędnego w tej samej subskrypcji i grupie zasobów, podaj typ i nazwę zasobu.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Aby uzyskać identyfikator zasobu podrzędnego, należy zwrócić uwagę na liczbę segmentów w typie zasobu. Podaj nazwę zasobu dla każdego segmentu typu zasobu. Nazwa segmentu odpowiada zasobowi, który istnieje dla tej części hierarchii.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Aby uzyskać identyfikator zasobu dla zasobu w tej samej subskrypcji, ale w innej grupie zasobów, podaj nazwę grupy zasobów.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla zasobu w innej grupie subskrypcji i zasobów, podaj identyfikator subskrypcji i nazwę grupy zasobów.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Często należy użyć tej funkcji podczas korzystania z konta magazynu lub sieci wirtualnej w alternatywnej grupie zasobów. W poniższym przykładzie pokazano, jak można łatwo używać zasobu z zewnętrznej grupy zasobów:

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

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) zwraca identyfikator zasobu dla konta magazynu w grupie zasobów:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| sameRGOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Ciąg | /subscriptions/1111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
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

Podczas korzystania z szablonów zagnieżdżonych do wdrażania w wielu subskrypcjach, można określić zakres oceny funkcji subskrypcji. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Przykład subskrypcji

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) przedstawia funkcję subskrypcji wywoływana w sekcji dane wyjściowe.

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

## <a name="subscriptionresourceid"></a>identyfikator zasobów subskrypcji

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie subskrypcji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |(w formacie GUID) |Wartość domyślna to bieżąca subskrypcja. Określ tę wartość, gdy trzeba pobrać zasób w innej subskrypcji. |
| resourceType |Tak |ciąg |Typ zasobu, w tym obszar nazw dostawcy zasobów. |
| nazwa zasobu1 |Tak |ciąg |Nazwa zasobu. |
| nazwa zasobu2 |Nie |ciąg |Następny segment nazwy zasobu, w razie potrzeby. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do uzyskania identyfikatora zasobu dla zasobów, które są [wdrażane w ramach subskrypcji,](deploy-to-subscription.md) a nie grupy zasobów. Zwrócony identyfikator różni się od wartości zwracane przez [resourceId](#resourceid) funkcji, nie wliczając wartości grupy zasobów.

### <a name="subscriptionresourceid-example"></a>przykład subscriptionResourceID

Poniższy szablon przypisuje wbudowaną rolę. Można go wdrożyć w grupie zasobów lub subskrypcji. Używa subscriptionResourceId funkcji, aby uzyskać identyfikator zasobu dla ról wbudowanych.

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

## <a name="tenantresourceid"></a>identyfikator źródłu zewnętrznego tenant

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie dzierżawy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceType |Tak |ciąg |Typ zasobu, w tym obszar nazw dostawcy zasobów. |
| nazwa zasobu1 |Tak |ciąg |Nazwa zasobu. |
| nazwa zasobu2 |Nie |ciąg |Następny segment nazwy zasobu, w razie potrzeby. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do uzyskania identyfikatora zasobu dla zasobu, który jest wdrażany w dzierżawcy. Zwrócony identyfikator różni się od wartości zwracanych przez inne funkcje identyfikatora zasobu, nie uwzględniając wartości grupy zasobów lub subskrypcji.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

