---
title: Usługa Azure Service Bus zawiesić jednostek obsługi komunikatów | Dokumentacja firmy Microsoft
description: Wstrzymywanie i ponowne uaktywnianie jednostki komunikatów usługi Azure Service Bus.
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 1984b113f695107f8d4d80e5bbf25c7dc39d13f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197030"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Wstrzymywanie i ponownie uaktywnić jednostek obsługi komunikatów (wyłączone)

Kolejki, tematy i subskrypcje może być tymczasowo wstrzymane. Zawieszenie umieszcza jednostki w stanie wyłączenia, w którym wszystkie wiadomości są zachowywane w magazynie. Jednak wiadomości nie może być usunięte lub dodane, a operacje odpowiednich protokołu yield błędy.

Wstrzymywanie jednostki jest zazwyczaj wykonywane pilnych powodów. Jednym ze scenariuszy o wdrożeniu odbiornik uszkodzony pobierającej komunikatów kolejki, niepowodzenia przetwarzania, jeszcze niepoprawnie zakończeniu wiadomości i usuwa je. Jeśli to zachowanie jest zdiagnozowaniu, kolejki można wyłączyć dla odbiera dopóki poprawiony kod nie zostanie wdrożony i dodatkowo można zapobiec utraty danych spowodowanej nieprawidłowy kod.

Zawieszenie lub ponownej aktywacji mogą być wykonywane przez użytkownika lub przez system. System zawiesza tylko jednostek poważne przyczyn administracyjnych na przykład naciśnięcie subskrypcji limit wydatków. Wyłączone systemu jednostek nie można ponownie uaktywnić przez użytkownika, ale zostaną przywrócone, gdy został rozwiązany przyczynę zawieszenia.

W portalu **właściwości** sekcji odpowiednich jednostek umożliwia zmianę stanu; Poniższy zrzut ekranu przedstawia Przełącz kolejki:

![][1]

Portalu zezwala tylko na całkowite wyłączenie kolejek. Można również wyłączyć wysyłania i odbierania operacji oddzielnie za pomocą usługi Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API w .NET Framework SDK lub za pomocą szablonu usługi Azure Resource Manager za pomocą wiersza polecenia platformy Azure lub programu Azure PowerShell.

## <a name="suspension-states"></a>Stany zawieszenia

Dostępne są następujące stany, które można ustawić dla kolejki:

-   **Aktywne**: kolejki jest aktywny.
-   **Wyłączone**: kolejki jest wstrzymana.
-   **SendDisabled**: kolejki częściowo jest wstrzymana z receive jest dozwolone.
-   **ReceiveDisabled**: kolejki częściowo jest wstrzymana z send jest dozwolone.

Dla subskrypcji i tematy, tylko **Active** i **wyłączone** można ustawić.

[Wymiary modułów EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) wyliczenie definiuje również zestawu przejściowego stanów, które można ustawić tylko przez system. W poniższym przykładzie przedstawiono polecenia programu PowerShell, aby wyłączyć kolejki. Polecenie ponownej aktywacji jest równoważne ustawienie `Status` do **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

