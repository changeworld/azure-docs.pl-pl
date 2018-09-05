---
title: Automatyczne przekazywanie jednostki obsługi komunikatów usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Jak połączyć w łańcuch kolejki usługi Service Bus lub subskrypcji do innej kolejki lub tematu.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: spelluru
ms.openlocfilehash: 563fa6f38bb5baffb9a4ae86f944b7597d325d30
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698999"
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Tworzenie łańcuchów jednostek usługi Service Bus z automatycznym przekazywaniem

Usługa Service Bus *automatycznym przekazywaniem* pozwala połączyć w łańcuch kolejki lub subskrypcji do innej kolejki lub tematu, który jest częścią tej samej przestrzeni nazw. Włączenie automatycznego przekazywania usługi Service Bus automatycznie usuwa wiadomości, które są umieszczane w pierwszej kolejki lub subskrypcji (źródło) i umieszcza je w drugim kolejki lub tematu (docelowy). Należy pamiętać, że jest nadal możliwe bezpośrednio wysyłać wiadomości do jednostki docelowej. Ponadto nie jest możliwe połączyć w łańcuch kolejki podrzędnej, takiego jak kolejka utraconych wiadomości, innej kolejki lub tematu.

## <a name="using-auto-forwarding"></a>Korzystanie z automatycznym przekazywaniem

Aby umożliwić automatyczne przekazywanie przez ustawienie [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] lub [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] właściwości [QueueDescription] [ QueueDescription] lub [SubscriptionDescription] [ SubscriptionDescription] obiektów dla źródła, podobnie jak w Poniższy przykład:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Jednostki docelowej musi istnieć w momencie utworzenia jednostki źródłowej. Usługa Service Bus, jeśli jednostki docelowej nie istnieje, zwraca wyjątek, po wyświetleniu monitu, aby utworzyć jednostki źródłowej.

Automatyczne przekazywanie umożliwia skalowanie w poziomie poszczególne tematy. Limity usługi Service Bus [liczba subskrypcji na dany temat](service-bus-quotas.md) do 2000. Może obsłużyć dodatkowych subskrypcji, tworząc tematy drugiego poziomu. Nawet wtedy, gdy użytkownik nie są powiązane przez ograniczenie usługi Service Bus na liczbę subskrypcji, dodawanie drugiego poziomu tematy może zwiększyć ogólną przepływność tematu.

![Scenariusz z automatycznym przekazywaniem][0]

Automatyczne przekazywanie umożliwia również oddzielenie nadawców wiadomości z odbiorników. Rozważmy na przykład system ERP, który składa się z trzech modułów: kolejność przetwarzania, Zarządzanie zapasami i zarządzanie relacjami z klientami. Każda z tych modułów generuje wiadomości, które są umieszczonych w kolejce do odpowiedniego tematu. Alice i Bob są przedstawicieli handlowych, które są zainteresowane wszystkie komunikaty, które odnoszą się do swoich klientów. Do otrzymywania tych wiadomości, Alice i Bob każdego Utwórz kolejkę osobistych i subskrypcji na wszystkich tematów ERP, które automatycznie przesyła wszystkie komunikaty do kolejki, ich.

![Scenariusz z automatycznym przekazywaniem][1]

Jeśli Alicja przechodzi na urlopu, swojej osobistej kolejki, a nie tematu ERP, się zapełni. W tym scenariuszu ponieważ przedstawiciel handlowy nie odebrał żadnych komunikatów żaden z tematów ERP i przeniesieniu jej nigdy nie dotrzeć do limitu przydziału.

## <a name="auto-forwarding-considerations"></a>Zagadnienia dotyczące automatycznym przekazywaniem

Jeśli jednostki docelowej gromadzi zbyt wiele komunikatów i przekracza limit przydziału lub jednostki docelowej jest wyłączona, jednostki źródłowej dodaje komunikaty do jego [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) miejsce w miejscu docelowym (lub jednostki ponownie włączono). Te komunikaty w dalszym ciągu mieszka kolejki utraconych wiadomości, więc należy jawnie odbierać i przetwarzać je z kolejki utraconych wiadomości.

Gdy Łączenie łańcuchowe poszczególnych tematów w celu uzyskania złożonych tematów z wieloma subskrypcjami, zaleca się, że masz umiarkowana liczba subskrypcji na temat pierwszego poziomu i wiele subskrypcji na tematy drugiego poziomu. Na przykład tematu pierwszego poziomu z subskrypcjami 20, każde z nich łańcuchowa tematu second-level 200 subskrypcji umożliwia wyższą przepływność niż tematu pierwszego poziomu z subskrypcjami 200, każdy powiązany z 20 subskrypcje tematu drugiego poziomu.

Usługa Service Bus rozlicza jedną operację dla wszystkich wiadomości przesyłanych dalej. Na przykład wysyłania komunikatu do tematu z 20 subskrypcji, z których każdy z nich są skonfigurowane do automatycznego przekazywania dalej wiadomości do innej kolejki lub tematu jest rozliczane jako 21 operacje Jeśli wszystkie subskrypcje pierwszego poziomu otrzyma kopię wiadomości.

Aby utworzyć subskrypcję, która jest powiązany innej kolejki lub tematu, twórca subskrypcji musi mieć **Zarządzaj** uprawnień na komputerze źródłowym, jak i jednostki docelowej. Wysyłanie komunikatów do tematu źródła wymaga jedynie **wysyłania** uprawnień na temat źródła.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe informacje o automatycznym przekazywaniem zobacz następujące tematy odniesienia:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Aby dowiedzieć się więcej o ulepszeniach wydajności usługi Service Bus, zobacz 

* [Najlepsze rozwiązania zwiększające wydajność przy użyciu komunikatów usługi Service Bus](service-bus-performance-improvements.md)
* [Partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
