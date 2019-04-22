---
title: Usługa Azure Service Bus zawiesić jednostek obsługi komunikatów | Dokumentacja firmy Microsoft
description: Wstrzymywanie i ponowne aktywowanie jednostek komunikatów usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: e2ffda3141462d19557af3af26c117ee505c40ab
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047351"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Wstrzymywanie i ponowne aktywowanie jednostek obsługi komunikatów (Wyłącz)

Kolejki, tematy i subskrypcje mogą być tymczasowo wstrzymane. Zawieszenie przełącza jednostkę w stanie wyłączenia, w którym wszystkie wiadomości są zachowywane w magazynie. Jednak wiadomości nie może być usunięte lub dodane, a operacje protokołu odpowiednich uzyskanie błędy.

Zawieszanie jednostki odbywa się zwykle z powodów pilne. Jeden scenariusz jest o wdrożeniu wadliwe odbiorcy, który przyjmuje komunikaty w kolejce, nie powiedzie się przetwarzanie i jeszcze niepoprawnie kończy komunikaty i usuwa je. Jeśli to zachowanie jest zdiagnozować, kolejki, można go wyłączyć dla odbiera aż poprawiony kod jest wdrażany, a dodatkowo można zapobiec utraty danych spowodowanej nieprawidłowy kod.

Zawieszenie lub ponownej aktywacji można wykonać przez użytkownika lub przez system. System wstrzymuje tylko jednostki z powodu poważnego z powodów takich jak naciśnięcie subskrypcji limit wydatków. Jednostki z wyłączonymi zabezpieczeniami systemu nie można ponownie uaktywnić przez użytkownika, ale zostaną przywrócone, gdy rozpoczęto przyczyna zawieszenia.

W portalu **właściwości** sekcji dla odpowiedniej jednostki umożliwia zmianę stanu; Poniższy zrzut ekranu przedstawia przełącznik dla kolejki:

![][1]

Portal umożliwia jedynie kolejki jest całkowicie wyłączany. Można również wyłączyć wysyłania i odbierania operacji oddzielnie za pomocą usługi Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API w zestawie SDK programu .NET Framework lub przy użyciu szablonu usługi Azure Resource Manager za pomocą wiersza polecenia platformy Azure lub programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Stany zawieszenia

Dostępne są następujące stany, które można ustawić dla kolejki:

-   **Aktywne**: Kolejka jest aktywny.
-   **Wyłączone**: Kolejka jest zawieszone.
-   **SendDisabled**: Kolejka jest częściowo zawieszone z receive są dozwolone.
-   **ReceiveDisabled**: Kolejka jest częściowo zawieszone z Wyślij są dozwolone.

W przypadku subskrypcji i tematów, tylko **Active** i **wyłączone** można ustawić.

[Wymiary modułów EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) wyliczenie definiuje również zestaw przejściowe stanów, które można ustawić tylko przez system. Polecenie programu PowerShell, aby wyłączyć kolejki przedstawiono w poniższym przykładzie. Polecenie ponownej aktywacji jest równoważne, ustawienie `Status` do **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

