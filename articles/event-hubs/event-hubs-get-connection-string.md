---
title: Pobieranie parametrów połączenia — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje dotyczące pobierania parametrów połączenia, używanego przez klientów do łączenia z usługą Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 744151a1ce8cde630e26c17ccf06569ebd0efb61
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365404"
---
# <a name="get-an-event-hubs-connection-string"></a>Pobieranie parametrów połączenia usługi Event Hubs

Aby korzystać z usługi Event Hubs, musisz utworzyć obszar nazw usługi Event Hubs. Przestrzeń nazw jest kontenerem określania zakresu dla wielu centrów zdarzeń lub tematów Kafka. Ta przestrzeń nazw zapewnia unikatową [nazwę FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po utworzeniu przestrzeni nazw, można uzyskać parametry połączenia wymagane do komunikacji z usługą Event Hubs.

Parametry połączenia dla usługi Azure Event Hubs zawiera następujące składniki, które są osadzone w nim,

* FQDN = nazwa FQDN utworzonej przestrzeni nazw EventHubs (zawiera nazwę przestrzeni nazw EventHubs, a następnie servicebus.windows.net)
* SharedAccessKeyName = nazwa, został wybrany dla kluczy sygnatury dostępu Współdzielonego usługi aplikacji
* SharedAccessKey = wygenerowaną wartość klucza.

Szablon parametrów połączenia wygląda następująco
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Przykładowe parametry połączenia mogą wyglądać jak `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

W tym artykule opisano za pośrednictwem różnych sposobów uzyskiwania parametrów połączenia.

## <a name="get-connection-string-from-the-portal"></a>Pobieranie parametrów połączenia z poziomu portalu
1. Zaloguj się w [portalu Azure](https://portal.azure.com). 
2. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie. 
3. Wybierz **Event Hubs** w sekcji **Analiza** . 
4. Na liście centrów zdarzeń wybierz centrum zdarzeń.
6. Na stronie **obszar nazw Event Hubs** wybierz pozycję **zasady dostępu współdzielonego** w menu po lewej stronie.

    ![Element menu zasad dostępu współdzielonego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Wybierz **zasady dostępu współdzielonego** na liście zasad. Wartość domyślna to o nazwie: **RootManageSharedAccessPolicy**. Można dodać zasady z odpowiednimi uprawnieniami (odczyt, zapis) i korzystać z tych zasad. 

    ![Event Hubs zasad dostępu współdzielonego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Wybierz przycisk **Kopiuj** obok pola **Parametry połączenia — klucz podstawowy** . 

    ![Event Hubs — uzyskiwanie parametrów połączenia](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Pobieranie parametrów połączenia z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można użyć [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) , aby pobrać parametry połączenia dla konkretnej nazwy zasad/reguły, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Pobieranie parametrów połączenia przy użyciu wiersza polecenia platformy Azure
Pobieranie parametrów połączenia dla przestrzeni nazw umożliwia następujące czynności:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Można też użyć poniższego elementu, aby uzyskać parametry połączenia dla jednostki EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure dla Event Hubs, zobacz [interfejs wiersza polecenia platformy Azure dla Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
