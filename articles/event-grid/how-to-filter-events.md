---
title: Jak filtrować zdarzenia dla usługi Azure Event Grid
description: W tym artykule pokazano, jak filtrować zdarzenia (według typu zdarzenia, według tematu, przez operatorów i dane itp.) podczas tworzenia subskrypcji usługi Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454027"
---
# <a name="filter-events-for-event-grid"></a>Filtrowanie zdarzeń dla siatki zdarzeń

W tym artykule pokazano, jak filtrować zdarzenia podczas tworzenia subskrypcji usługi Event Grid. Aby dowiedzieć się więcej o opcjach filtrowania zdarzeń, zobacz [Opis filtrowania zdarzeń w przypadku subskrypcji usługi Event Grid](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrowanie według typu zdarzenia

Podczas tworzenia subskrypcji usługi Event Grid można określić [typy zdarzeń,](event-schema.md) które mają być wysyłane do punktu końcowego. Przykłady w tej sekcji tworzą subskrypcje zdarzeń dla grupy zasobów, `Microsoft.Resources.ResourceWriteFailure` `Microsoft.Resources.ResourceWriteSuccess`ale ograniczają zdarzenia wysyłane do i . Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzeń według typów zdarzeń, zobacz Filtrowanie według zaawansowanych operatorów i pól danych.

W przypadku programu PowerShell należy użyć tego parametru `-IncludedEventType` podczas tworzenia subskrypcji.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

W przypadku interfejsu `--included-event-types` wiersza polecenia platformy Azure należy użyć tego parametru. W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure w powłoce Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

W przypadku szablonu Menedżera `includedEventTypes` zasobów użyj właściwości.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrowanie według tematu

Zdarzenia można filtrować według tematu w danych zdarzenia. Można określić wartość dopasowaną do początku lub końca tematu. Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzeń według tematu, zobacz Filtrowanie według zaawansowanych operatorów i pól danych.

W poniższym przykładzie programu PowerShell należy utworzyć subskrypcję zdarzeń, która filtruje według początku tematu. Parametr służy `-SubjectBeginsWith` do ograniczania zdarzeń do zdarzeń dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń jest przekazywalny.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Następny przykład programu PowerShell tworzy subskrypcję magazynu obiektów blob. Ogranicza zdarzenia do tych z tematem, który kończy się na `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

W poniższym przykładzie interfejsu wiersza polecenia platformy Azure należy utworzyć subskrypcję zdarzeń, która filtruje według początku tematu. Parametr służy `--subject-begins-with` do ograniczania zdarzeń do zdarzeń dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń jest przekazywalny.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Następny przykład interfejsu wiersza polecenia platformy Azure tworzy subskrypcję magazynu obiektów blob. Ogranicza zdarzenia do tych z tematem, który kończy się na `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

W poniższym przykładzie szablonu Menedżera zasobów należy utworzyć subskrypcję zdarzeń, która filtruje według początku tematu. Właściwości służy `subjectBeginsWith` do ograniczania zdarzeń do zdarzeń dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń jest przekazywalny.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

Następny przykład szablonu Menedżera zasobów tworzy subskrypcję magazynu obiektów blob. Ogranicza zdarzenia do tych z tematem, który kończy się na `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrowanie według operatorów i danych

Aby uzyskać większą elastyczność filtrowania, można użyć operatorów i właściwości danych do filtrowania zdarzeń.

### <a name="subscribe-with-advanced-filters"></a>Subskrybuj zaawansowane filtry

Aby dowiedzieć się więcej o operatorach i kluczach, których można używać do filtrowania zaawansowanego, zobacz [Filtrowanie zaawansowane](event-filtering.md#advanced-filtering).

Te przykłady tworzą temat niestandardowy. Subskrybują temat niestandardowy i filtrują według wartości w obiekcie danych. Zdarzenia, które mają właściwość color ustawioną na niebieską, czerwoną lub zieloną, są wysyłane do subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Należy zauważyć, że ustawiono [datę wygaśnięcia](concepts.md#event-subscription-expiration) subskrypcji.

W przypadku programu PowerShell użyj polecenia:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Filtr testowy

Aby przetestować filtr, wyślij zdarzenie z polem koloru ustawionym na zielony. Ponieważ zielony jest jedną z wartości w filtrze, zdarzenie jest dostarczane do punktu końcowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

W przypadku programu PowerShell użyj polecenia:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Aby przetestować scenariusz, w którym zdarzenie nie jest wysyłane, wyślij zdarzenie z polem koloru ustawionym na żółty. Żółty nie jest jedną z wartości określonych w subskrypcji, więc zdarzenie nie jest dostarczane do subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
W przypadku programu PowerShell użyj polecenia:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące monitorowania dostaw zdarzeń, zobacz [Monitorowanie dostarczania komunikatów w sieci zdarzeń](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
