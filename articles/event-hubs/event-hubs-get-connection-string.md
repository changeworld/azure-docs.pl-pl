---
title: Pobieranie parametrów połączenia — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje dotyczące pobierania parametrów połączenia, używanego przez klientów do łączenia z usługą Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 263dba970b015d466b3fd46f390a063e87de2e2c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093321"
---
# <a name="get-an-event-hubs-connection-string"></a>Pobieranie parametrów połączenia usługi Event Hubs

Aby korzystać z usługi Event Hubs, musisz utworzyć obszar nazw usługi Event Hubs. Przestrzeń nazw jest kontenerem określania zakresu, które mogą znajdować się wiele centrów zdarzeń / tematów platformy Kafka. Ta przestrzeń nazw zapewnia unikatową [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po utworzeniu przestrzeni nazw, można uzyskać parametry połączenia wymagane do komunikacji z usługą Event Hubs.

Parametry połączenia dla usługi Azure Event Hubs zawiera następujące składniki, które są osadzone w nim,

* Nazwa FQDN = nazwę FQDN utworzoną przestrzeń nazw EventHubs (dotyczy to EventHubs nazwę przestrzeni nazw, a następnie servicebus.windows.net)
* SharedAccessKeyName = nazwa, został wybrany dla kluczy sygnatury dostępu Współdzielonego usługi aplikacji
* SharedAccessKey = wygenerowaną wartość klucza.

Szablon parametrów połączenia wygląda następująco
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Przykładowe parametry połączenia mogą wyglądać jak `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

W tym artykule opisano za pośrednictwem różnych sposobów uzyskiwania parametrów połączenia.

## <a name="get-connection-string-from-the-portal"></a>Pobieranie parametrów połączenia z poziomu portalu

Po utworzeniu przestrzeni nazw usługi Event Hubs, w sekcji Przegląd portalu daje ciąg połączenia jak pokazano poniżej:

![Parametry połączenia centrów zdarzeń](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Po kliknięciu łącza do parametrów połączenia w sekcji Przegląd Otwiera kartę zasady sygnatury dostępu Współdzielonego, jak pokazano na poniższej ilustracji:

![Zasady usługi Event Hubs sygnatury dostępu Współdzielonego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Można dodawać nowe zasady sygnatury dostępu Współdzielonego i pobieranie parametrów połączenia lub używać domyślnych zasad, który jest już utworzony. Po otwarciu zasady uzyskuje się parametry połączenia, jak pokazano na poniższych rysunku:

![Usługa Event Hubs pobieranie parametrów połączenia](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Pobieranie parametrów połączenia z programem Azure PowerShell
Get-AzureRmEventHubNamespaceKey umożliwia pobieranie parametrów połączenia dla nazwy zasady/reguły określ, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Zapoznaj się [modułu Azure PowerShell centra zdarzeń](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) Aby uzyskać więcej informacji.

## <a name="getting-the-connection-string-with-azure-cli"></a>Pobieranie parametrów połączenia przy użyciu wiersza polecenia platformy Azure
Pobieranie parametrów połączenia dla przestrzeni nazw umożliwia następujące czynności:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Zapoznaj się [wiersza polecenia platformy Azure dla usługi Event Hubs](https://docs.microsoft.com/cli/azure/eventhubs) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
