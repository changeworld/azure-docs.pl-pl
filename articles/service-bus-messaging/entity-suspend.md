---
title: Usługa Azure Service Bus zawiesić jednostek obsługi komunikatów | Dokumentacja firmy Microsoft
description: Wstrzymywanie i ponowne aktywowanie jednostek komunikatów usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: bd75fd5c34a3518f6da0dbd56eca2ad152d60ba9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405720"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Wstrzymywanie i ponowne aktywowanie jednostek obsługi komunikatów (Wyłącz)

Kolejki, tematy i subskrypcje mogą być tymczasowo wstrzymane. Zawieszenie przełącza jednostkę w stanie wyłączenia, w którym wszystkie wiadomości są zachowywane w magazynie. Jednak wiadomości nie może być usunięte lub dodane, a operacje protokołu odpowiednich uzyskanie błędy.

Zawieszanie jednostki odbywa się zwykle z powodów pilne. Jeden scenariusz jest o wdrożeniu wadliwe odbiorcy, który przyjmuje komunikaty w kolejce, nie powiedzie się przetwarzanie i jeszcze niepoprawnie kończy komunikaty i usuwa je. Jeśli to zachowanie jest zdiagnozować, kolejki, można go wyłączyć dla odbiera aż poprawiony kod jest wdrażany, a dodatkowo można zapobiec utraty danych spowodowanej nieprawidłowy kod.

Zawieszenie lub ponownej aktywacji można wykonać przez użytkownika lub przez system. System wstrzymuje tylko jednostki z powodu poważnego z powodów takich jak naciśnięcie subskrypcji limit wydatków. Jednostki z wyłączonymi zabezpieczeniami systemu nie można ponownie uaktywnić przez użytkownika, ale zostaną przywrócone, gdy rozpoczęto przyczyna zawieszenia.

W portalu **właściwości** sekcji dla odpowiedniej jednostki umożliwia zmianę stanu; Poniższy zrzut ekranu przedstawia przełącznik dla kolejki:

![][1]

Portal umożliwia jedynie kolejki jest całkowicie wyłączany. Można również wyłączyć wysyłania i odbierania operacji oddzielnie za pomocą usługi Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API w zestawie SDK programu .NET Framework lub przy użyciu szablonu usługi Azure Resource Manager za pomocą wiersza polecenia platformy Azure lub programu Azure PowerShell.

## <a name="suspension-states"></a>Stany zawieszenia

Dostępne są następujące stany, które można ustawić dla kolejki:

-   **Aktywne**: kolejka jest aktywny.
-   **Wyłączone**: kolejka jest wstrzymana.
-   **SendDisabled**: kolejki częściowo jest wstrzymana, za pomocą receive są dozwolone.
-   **ReceiveDisabled**: kolejki częściowo jest wstrzymana, za pomocą wysyłania są dozwolone.

W przypadku subskrypcji i tematów, tylko **Active** i **wyłączone** można ustawić.

[Wymiary modułów EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) wyliczenie definiuje również zestaw przejściowe stanów, które można ustawić tylko przez system. Polecenie programu PowerShell, aby wyłączyć kolejki przedstawiono w poniższym przykładzie. Polecenie ponownej aktywacji jest równoważne, ustawienie `Status` do **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

