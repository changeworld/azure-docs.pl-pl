---
title: Usługa Azure Service Bus — zawieszanie jednostek obsługi wiadomości
description: W tym artykule wyjaśniono, jak tymczasowo zawiesić i ponownie aktywować jednostki komunikatów usługi Azure Service Bus (kolejki, tematy i subskrypcje).
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760389"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Zawieszanie i ponowne aktywowanie jednostek obsługi wiadomości (wyłączanie)

Kolejki, tematy i subskrypcje mogą zostać tymczasowo zawieszone. Zawieszenie umieszcza jednostki w stanie wyłączonym, w którym wszystkie wiadomości są przechowywane w magazynie. Jednak wiadomości nie można usunąć lub dodać, a odpowiednie operacje protokołu dają błędy.

Zawieszenie jednostki odbywa się zazwyczaj z pilnych względów administracyjnych. Jednym ze scenariuszy jest wdrożenie wadliwego odbiornika, który odbiera wiadomości z kolejki, nie przetwarza, a mimo to niepoprawnie kończy wiadomości i usuwa je. Jeśli to zachowanie zostanie zdiagnozowane, kolejka może być wyłączona dla odbiera, dopóki nie zostanie wdrożony poprawiony kod i można zapobiec dalszej utracie danych spowodowanej przez wadliwy kod.

Zawieszenie lub reaktywacja może być wykonana przez użytkownika lub przez system. System zawiesza tylko jednostki z poważnych przyczyn administracyjnych, takich jak osiągnięcie limitu wydatków na subskrypcję. Jednostki wyłączone z systemu nie mogą być ponownie aktywowane przez użytkownika, ale są przywracane, gdy przyczyna zawieszenia została rozwiązana.

W **portalu, Właściwości** sekcji dla odpowiedniej jednostki umożliwia zmianę stanu; poniższy zrzut ekranu przedstawia przełącznik dla kolejki:

![][1]

Portal zezwala tylko na całkowite wyłączenie kolejek. Operacje wysyłania i odbierania można również wyłączyć oddzielnie przy użyciu interfejsów API [menedżera nazw usługi](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus w programie .NET Framework SDK lub za pomocą szablonu usługi Azure Resource Manager za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Stany zawieszenia

Stany, które można ustawić dla kolejki są:

-   **Aktywny:** Kolejka jest aktywna.
-   **Wyłączone**: Kolejka jest zawieszona.
-   **SendDisabled**: Kolejka jest częściowo zawieszona, a odbieranie jest dozwolone.
-   **ReceiveDisabled:** Kolejka jest częściowo zawieszona, a wysyłanie jest dozwolone.

W przypadku subskrypcji i tematów można ustawić tylko **aktywne** i **wyłączone.**

Wyliczanie [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definiuje również zestaw stanów przejściowych, które mogą być ustawiane tylko przez system. Polecenie programu PowerShell, aby wyłączyć kolejkę jest wyświetlany w poniższym przykładzie. Polecenie reaktywacji jest `Status` równoważne, ustawienie **active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

