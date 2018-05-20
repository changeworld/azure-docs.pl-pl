---
title: Wysyłanie zdarzeń niestandardowych dla usługi Azure Event Grid do połączenia hybrydowego | Microsoft Docs
description: Za pomocą usługi Azure Event Grid i interfejsu wiersza polecenia platformy Azure można opublikować temat i zasubskrybować dane zdarzenie. Połączenie hybrydowe jest używane dla punktu końcowego.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 05/04/2018
ms.topic: article
ms.service: event-grid
ms.openlocfilehash: c95cfee787244367688b82959474e2a8028b7ff6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Kierowanie zdarzeń niestandardowych do połączeń hybrydowych usługi Azure Relay za pomocą interfejsu wiersza polecenia platformy Azure i usługi Event Grid

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Połączenia hybrydowe usługi Azure Relay to jedne z obsługiwanych procedur obsługi zdarzeń. Połączeń hybrydowych można używać jako procedur obsługi zdarzeń, jeśli zachodzi potrzeba przetwarzania zdarzeń z aplikacji, które nie mają publicznego punktu końcowego. Te aplikacje mogą znajdować się w sieci korporacyjnej firmy. W tym artykule omówiono tworzenie tematu niestandardowego, subskrybowanie go i wyzwalanie zdarzenia w celu wyświetlenia wyniku za pomocą interfejsu wiersza polecenia platformy Azure. Zdarzenia wysyła się do połączenia hybrydowego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już dostęp do połączenia hybrydowego i aplikacji odbiornika. Aby rozpocząć pracę z połączeniami hybrydowymi, zobacz [Wprowadzenie do połączeń hybrydowych usługi Relay — platforma .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) lub [Wprowadzenie do połączeń hybrydowych usługi Relay — oprogramowanie Node](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *gridResourceGroup* w lokalizacji *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat usługi Event Grid udostępnia zdefiniowany przez użytkownika punkt końcowy, w którym publikowane są zdarzenia. Poniższy przykład obejmuje tworzenie tematu niestandardowego w grupie zasobów. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ jest reprezentowana przez wpis DNS.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>Subskrybowanie tematu

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić. Poniższy przykład ilustruje subskrybowanie utworzonego tematu i przekazanie identyfikatora zasobu do połączenia hybrydowego punktu końcowego. Identyfikator połączenia hybrydowego ma następujący format:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Poniższy skrypt pobiera identyfikator zasobu przestrzeni nazw przekaźnika. Tworzy identyfikator połączenia hybrydowego i subskrybuje temat usługi Event Grid. Ustawia typ punktu końcowego na `hybridconnection` i używa identyfikatora połączenia hybrydowego punktu końcowego.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="send-an-event-to-your-topic"></a>Wysyłanie zdarzenia do tematu

Wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Po pierwsze uzyskajmy adres URL i klucz tematu niestandardowego. Ponownie jako `<topic_name>` użyj nazwy tematu.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Aby uprościć ten artykuł, użyj przykładowych danych zdarzenia do wysłania do tematu. Zwykle dane zdarzenia są wysyłane przez aplikację lub usługę platformy Azure. CURL to narzędzie, które wysyła żądania HTTP. W tym artykule do wysłania zdarzenia do tematu używane jest narzędzie CURL.  W poniższym przykładzie są wysyłane trzy zdarzenia do tematu usługi Event Grid:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Aplikacja odbiornika powinna odebrać komunikat zdarzenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zamierzasz kontynuować pracę z tym zdarzeniem, nie usuwaj zasobów utworzonych w tym artykule. W przeciwnym razie użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Event Grid — informacje](overview.md)
- [Kierowanie zdarzeń usługi Blob Storage do niestandardowego internetowego punktu końcowego](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)
