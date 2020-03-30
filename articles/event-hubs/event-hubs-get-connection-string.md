---
title: Pobierz ciąg połączenia — Usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera instrukcje dotyczące uzyskiwania ciągu połączenia, którego klienci mogą używać do łączenia się z centrum zdarzeń platformy Azure.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264936"
---
# <a name="get-an-event-hubs-connection-string"></a>Uzyskaj ciąg połączenia usługi Event Hubs

Aby korzystać z centrów zdarzeń, należy utworzyć obszar nazw centrów zdarzeń. Obszar nazw jest kontenerem zakresu dla wielu centrów zdarzeń lub tematów platformy Kafka. Ten obszar nazw zapewnia unikatową [nazwę FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po utworzeniu obszaru nazw można uzyskać parametry połączenia wymagane do komunikowania się z centrum zdarzeń.

Parametry połączenia dla usługi Azure Event Hubs mają następujące składniki osadzone w nim,

* FQDN = nazwa FQDN utworzonego obszaru nazw EventHubs (zawiera nazwę obszaru nazw EventHubs, po której następuje servicebus.windows.net)
* SharedAccessKeyName = nazwa wybrana dla kluczy SAS aplikacji
* SharedAccessKey = wygenerowana wartość klucza.

Szablon ciągu połączenia wygląda
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Przykładowy ciąg połączenia może wyglądać`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

W tym artykule przedstawiono różne sposoby uzyskiwania ciągu połączenia.

## <a name="get-connection-string-from-the-portal"></a>Pobierz ciąg połączenia z portalu
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com). 
2. Wybierz **pozycję Wszystkie usługi** w menu nawigacyjnym po lewej stronie. 
3. Wybierz **Centra zdarzeń** w sekcji **Analytics.** 
4. Na liście centrów zdarzeń wybierz centrum zdarzeń.
6. Na stronie **Obszar nazw centrów zdarzeń** wybierz polecenie Zasady dostępu **udostępnionego** w menu po lewej stronie.

    ![Element menu Zasady dostępu udostępnionego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Wybierz **zasadę dostępu współdzielonego** na liście zasad. Domyślna nazwa: **RootManageSharedAccessPolicy**. Można dodać zasady z odpowiednimi uprawnieniami (odczyt, zapis) i użyć tej zasady. 

    ![Zasady dostępu współdzielonego w centrach zdarzeń](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Wybierz przycisk **kopiowania** obok pola **Ciąg połączenia -klucz podstawowy.** 

    ![Centra zdarzeń — pobierz ciąg połączenia](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Uzyskiwanie ciągu połączenia za pomocą programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Za pomocą [funkcji Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) można uzyskać parametry połączenia dla określonej nazwy zasad/reguł, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Uzyskiwanie ciągu połączenia za pomocą interfejsu wiersza polecenia platformy Azure
Aby uzyskać ciąg połączenia dla obszaru nazw, można użyć następujących czynności:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Możesz też użyć następującego ciągu połączenia dla jednostki EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure dla centrów zdarzeń, zobacz [interfejsu wiersza polecenia platformy Azure dla centrów zdarzeń](/cli/azure/eventhubs).

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
