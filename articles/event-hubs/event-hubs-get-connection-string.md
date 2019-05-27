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
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158914"
---
# <a name="get-an-event-hubs-connection-string"></a>Pobieranie parametrów połączenia usługi Event Hubs

Aby korzystać z usługi Event Hubs, musisz utworzyć obszar nazw usługi Event Hubs. Przestrzeń nazw jest kontenerem określania zakresu dla wielu usługi event hubs lub tematów platformy Kafka. Ta przestrzeń nazw zapewnia unikatową [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po utworzeniu przestrzeni nazw, można uzyskać parametry połączenia wymagane do komunikacji z usługą Event Hubs.

Parametry połączenia dla usługi Azure Event Hubs zawiera następujące składniki, które są osadzone w nim,

* Nazwa FQDN = nazwę FQDN utworzoną przestrzeń nazw EventHubs (zawiera nazwę przestrzeni nazw EventHubs następuje servicebus.windows.net)
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
2. Wybierz **wszystkich usług** w menu nawigacji po lewej stronie. 
3. Wybierz **usługi Event Hubs** w **Analytics** sekcji. 
4. Na liście usługi event hubs wybierz Centrum zdarzeń.
6. Na **Event Hubs Namespace** wybierz opcję **współużytkowane zasady dostępu** w menu po lewej stronie.

    ![Udostępniony element menu zasad dostępu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Wybierz **współużytkowane zasady dostępu** w listy zasad. Wartość domyślna jednego nosi nazwę: **RootManageSharedAccessPolicy**. Dodaj zasadę z odpowiednimi uprawnieniami (odczytu, zapisu) i użyć tych zasad. 

    ![Usługa Event Hubs współużytkowane zasady dostępu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Wybierz **kopiowania** znajdujący się obok **parametry połączenia — klucz podstawowy** pola. 

    ![Event Hubs — pobieranie parametrów połączenia](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Pobieranie parametrów połączenia z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz użyć [Get AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) można pobrać parametry połączenia dla nazwy określonej reguły, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Pobieranie parametrów połączenia przy użyciu wiersza polecenia platformy Azure
Pobieranie parametrów połączenia dla przestrzeni nazw umożliwia następujące czynności:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Aby uzyskać więcej informacji dotyczących poleceń interfejsu wiersza polecenia platformy Azure dla usługi Event Hubs, zobacz [wiersza polecenia platformy Azure dla usługi Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
