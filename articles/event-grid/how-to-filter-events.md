---
title: Jak filtrować zdarzenia usługi Azure Event Grid
description: Przedstawia sposób tworzenia subskrypcji usługi Azure Event Grid, dla których filtrowanie zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 5bb95b80e12c818641e2be2b929cdfd01f8f5b5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304230"
---
# <a name="filter-events-for-event-grid"></a>Filtrowanie zdarzeń usługi Event Grid

W tym artykule przedstawiono sposób filtrowania zdarzeń podczas tworzenia subskrypcji usługi Event Grid. Aby dowiedzieć się więcej o opcjach do filtrowania zdarzeń, zobacz [zdarzeń omówienie filtrowania dla subskrypcji usługi Event Grid](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtruj według typu zdarzenia

Podczas tworzenia subskrypcji usługi Event Grid, można określić, które [typy zdarzeń](event-schema.md) wysyłać do punktu końcowego. Przykłady w tej sekcji Tworzenie subskrypcji zdarzeń dla grupy zasobów, ale ograniczenia zdarzeń, które są wysyłane do `Microsoft.Resources.ResourceWriteFailure` i `Microsoft.Resources.ResourceWriteSuccess`. Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzenia według typów zdarzeń, zobacz Filtruj według Zaawansowane operatory i pola danych.

W przypadku programu PowerShell, użyj `-IncludedEventType` parametru podczas tworzenia subskrypcji.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

W przypadku wiersza polecenia platformy Azure, użyj `--included-event-types` parametru. W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure, w powłoce Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Szablon usługi Resource Manager można użyć `includedEventTypes` właściwości.

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

## <a name="filter-by-subject"></a>Filtruj według tematu

Można filtrować zdarzenia według tematu w danych zdarzenia. Można określić wartość do dopasowania do początku lub na końcu tematu. Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzeń według tematu, zobacz Filtruj według Zaawansowane operatory i pola danych.

W poniższym przykładzie programu PowerShell utworzysz subskrypcję zdarzeń filtrujące według początku tematu. Możesz użyć `-SubjectBeginsWith` parametr w celu ograniczenia zdarzeń do tych dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń są przekazywane.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

W następnym przykładzie programu PowerShell tworzy subskrypcję dla usługi blob storage. Ogranicza zdarzeń do tych z podmiotem, który kończy się `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

W poniższym przykładzie wiersza polecenia platformy Azure utworzysz subskrypcję zdarzeń filtrujące według początku tematu. Możesz użyć `--subject-begins-with` parametr w celu ograniczenia zdarzeń do tych dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń są przekazywane.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

W następnym przykładzie wiersza polecenia platformy Azure tworzy subskrypcję dla usługi blob storage. Ogranicza zdarzeń do tych z podmiotem, który kończy się `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

W poniższym przykładowym szablonie usługi Resource Manager opisano tworzenie subskrypcji zdarzeń, która filtruje przy początku tematu. Możesz użyć `subjectBeginsWith` właściwości w celu ograniczenia zdarzeń do tych dla określonego zasobu. Identyfikator zasobu sieciowej grupy zabezpieczeń są przekazywane.

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

Kolejny przykład szablonu usługi Resource Manager tworzy subskrypcję dla usługi blob storage. Ogranicza zdarzeń do tych z podmiotem, który kończy się `.jpg`.

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

## <a name="filter-by-operators-and-data"></a>Filtruj według operatory i danych

Aby uzyskać większą elastyczność filtrowania można użyć operatorów i właściwości danych, aby filtrować zdarzenia.

### <a name="subscribe-with-advanced-filters"></a>Subskrybuj, korzystając z zaawansowanych filtrów

Aby dowiedzieć się więcej na temat operatorów i klucze, które umożliwiają filtrowanie zaawansowane, zobacz [zaawansowanego filtrowania](event-filtering.md#advanced-filtering).

Te przykłady tworzenie tematu niestandardowego. One subskrybowanie do tematu niestandardowego i filtrowanie według wartości w obiekcie danych. Zdarzenia, które mają color, ustawioną na niebieski, czerwonego lub zielonego, są wysyłane do subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
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

```azurepowershell-interactive
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

### <a name="test-filter"></a>Filtr testu

Aby przetestować filtr, wysyłanie zdarzeń za pomocą pola kolor na zielony. Ponieważ jest zielony, jedna z wartości w filtrze, zdarzenia są dostarczane do punktu końcowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
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

Aby przetestować scenariusz, w którym zdarzenie nie jest wysyłana, wysyłanie zdarzeń za pomocą pola kolor na żółty. Żółty nie jest jedną z wartości określone w ramach subskrypcji, dzięki czemu zdarzenia nie są dostarczane do Twojej subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
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

## <a name="next-steps"></a>Kolejne kroki

* Informacje o monitorowaniu dostarczenie zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
