---
title: Automatyczne przekazywanie jednostki obsługi komunikatów usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Jak połączyć w łańcuch kolejki usługi Service Bus lub subskrypcji do innej kolejki lub tematu.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 86fa7f62230c0ae0530b67ff2384942c876083d4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098596"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Tworzenie łańcuchów jednostek usługi Service Bus przy użyciu autoforwarding

Usługa Service Bus *autoforwarding* pozwala połączyć w łańcuch kolejki lub subskrypcji do innej kolejki lub tematu, który jest częścią tej samej przestrzeni nazw. Po włączeniu autoforwarding usługi Service Bus automatycznie usuwa wiadomości, które są umieszczane w pierwszej kolejki lub subskrypcji (źródło) i umieszcza je w drugim kolejki lub tematu (docelowy). Jest nadal możliwe bezpośrednio wysyłać wiadomości do jednostki docelowej.

## <a name="using-autoforwarding"></a>Za pomocą autoforwarding

Aby umożliwić autoforwarding przez ustawienie [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] lub [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] właściwości [QueueDescription] [ QueueDescription] lub [SubscriptionDescription] [ SubscriptionDescription] obiektów dla źródła, podobnie jak w Poniższy przykład:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Jednostki docelowej musi istnieć w momencie utworzenia jednostki źródłowej. Usługa Service Bus, jeśli jednostki docelowej nie istnieje, zwraca wyjątek, po wyświetleniu monitu, aby utworzyć jednostki źródłowej.

Autoforwarding umożliwia skalowanie w poziomie poszczególne tematy. Limity usługi Service Bus [liczba subskrypcji na dany temat](service-bus-quotas.md) do 2000. Może obsłużyć dodatkowych subskrypcji, tworząc tematy drugiego poziomu. Nawet wtedy, gdy użytkownik nie są powiązane przez ograniczenie usługi Service Bus na liczbę subskrypcji, dodawanie drugiego poziomu tematy może zwiększyć ogólną przepływność tematu.

![Scenariusz z automatycznym przekazywaniem][0]

Autoforwarding umożliwia również oddzielenie nadawców wiadomości z odbiorników. Rozważmy na przykład system ERP, który składa się z trzech modułów: kolejność przetwarzania, Zarządzanie zapasami i zarządzanie relacjami z klientami. Każda z tych modułów generuje wiadomości, które są umieszczonych w kolejce do odpowiedniego tematu. Alice i Bob są przedstawicieli handlowych, które są zainteresowane wszystkie komunikaty, które odnoszą się do swoich klientów. Do otrzymywania tych wiadomości, Alice i Bob każdego Utwórz kolejkę osobistych i subskrypcji na wszystkich tematów ERP, które automatycznie przesyła wszystkie komunikaty do kolejki, ich.

![Scenariusz z automatycznym przekazywaniem][1]

Jeśli Alicja przechodzi na urlopu, swojej osobistej kolejki, a nie tematu ERP, się zapełni. W tym scenariuszu ponieważ przedstawiciel handlowy nie odebrał żadnych komunikatów żaden z tematów ERP i przeniesieniu jej nigdy nie dotrzeć do limitu przydziału.

> [!NOTE]
> Jeśli autoforwarding jest skonfigurowana, wartość AutoDeleteOnIdle w folderze docelowym automatycznie ustawiono maksymalnej wartości typu danych.
> Odbywa się, aby upewnić się, że jest zawsze do przesyłania dalej wiadomości do miejsca docelowego.

## <a name="autoforwarding-considerations"></a>Zagadnienia dotyczące Autoforwarding

Jeśli jednostki docelowej gromadzi zbyt wiele komunikatów i przekracza limit przydziału lub jednostki docelowej jest wyłączona, jednostki źródłowej dodaje komunikaty do jego [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) miejsce w miejscu docelowym (lub jednostki ponownie włączono). Te komunikaty w dalszym ciągu mieszka kolejki utraconych wiadomości, więc należy jawnie odbierać i przetwarzać je z kolejki utraconych wiadomości.

Gdy Łączenie łańcuchowe poszczególnych tematów w celu uzyskania złożonych tematów z wieloma subskrypcjami, zaleca się, że masz umiarkowana liczba subskrypcji na temat pierwszego poziomu i wiele subskrypcji na tematy drugiego poziomu. Na przykład tematu pierwszego poziomu z subskrypcjami 20, każde z nich łańcuchowa tematu second-level 200 subskrypcji umożliwia wyższą przepływność niż tematu pierwszego poziomu z subskrypcjami 200, każdy powiązany z 20 subskrypcje tematu drugiego poziomu.

Usługa Service Bus rozlicza jedną operację dla wszystkich wiadomości przesyłanych dalej. Na przykład wysyłania komunikatu do tematu z 20 subskrypcji, z których każdy z nich jest skonfigurowany tak, aby komunikaty autoforward do innej kolejki lub tematu jest rozliczane jako 21 operacje Jeśli wszystkie subskrypcje pierwszego poziomu otrzyma kopię wiadomości.

Aby utworzyć subskrypcję, która jest powiązany innej kolejki lub tematu, twórca subskrypcji musi mieć **Zarządzaj** uprawnień na komputerze źródłowym, jak i jednostki docelowej. Wysyłanie komunikatów do tematu źródła wymaga jedynie **wysyłania** uprawnień na temat źródła.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe informacje na temat autoforwarding zobacz następujące tematy odniesienia:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Aby dowiedzieć się więcej o ulepszeniach wydajności usługi Service Bus, zobacz 

* [Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu komunikatów usługi Service Bus](service-bus-performance-improvements.md)
* [Partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
