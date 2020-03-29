---
title: Automatyczne przekazywanie do obsługi wiadomości przez usługę Azure Service Bus
description: W tym artykule opisano sposób łańcucha kolejki usługi Azure Service Bus lub subskrypcji do innej kolejki lub tematu.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761053"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Łączenie elementów usługi Service Bus z automatycznym dopasowywaniem

Funkcja *automatycznego dopasowywania* usługi Service Bus umożliwia łańcuch kolejki lub subskrypcji do innej kolejki lub tematu, który jest częścią tego samego obszaru nazw. Gdy automatyczne włączanie jest włączone, usługa Service Bus automatycznie usuwa wiadomości, które są umieszczane w pierwszej kolejce lub subskrypcji (źródło) i umieszcza je w drugiej kolejce lub temacie (miejsce docelowe). Nadal jest możliwe bezpośrednie wysłanie wiadomości do jednostki docelowej.

## <a name="using-autoforwarding"></a>Korzystanie z automatycznego dostosowania

Funkcję autoforowania można włączyć, ustawiając właściwości [QueueDescription.ForwardTo][QueueDescription.ForwardTo] lub [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] w obiektach [QueueDescription][QueueDescription] lub [SubscriptionDescription][SubscriptionDescription] dla źródła, jak w poniższym przykładzie:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Jednostka docelowa musi istnieć w momencie utworzenia jednostki źródłowej. Jeśli encja docelowa nie istnieje, usługa Service Bus zwraca wyjątek, gdy zostanie poproszony o utworzenie encji źródłowej.

Za pomocą automatycznego dostosowania można skalować w poziomie poszczególnych tematów. Usługa Service Bus ogranicza [liczbę subskrypcji w danym temacie](service-bus-quotas.md) do 2000. Można pomieścić dodatkowe subskrypcje, tworząc tematy drugiego poziomu. Nawet jeśli nie są związane z ograniczeniem usługi Service Bus na liczbę subskrypcji, dodanie drugiego poziomu tematów może poprawić ogólną przepływność tematu.

![Scenariusz automatycznego przekazywania][0]

Można również użyć autoforwarding do oddzielenia nadawców wiadomości od odbiorników. Rozważmy na przykład system ERP, który składa się z trzech modułów: przetwarzania zamówień, zarządzania zapasami i zarządzania relacjami z klientami. Każdy z tych modułów generuje komunikaty, które są w kolejce do odpowiedniego tematu. Alice i Bob są przedstawicielami handlowymi, którzy są zainteresowani wszystkimi wiadomościami, które odnoszą się do ich klientów. Aby odbierać te wiadomości, Alicja i Robert tworzą kolejkę osobistą i subskrypcję w każdym z tematów ERP, które automatycznie prześlą wszystkie wiadomości do swojej kolejki.

![Scenariusz automatycznego przekazywania][1]

Jeśli Alice jedzie na wakacje, jej osobista kolejka, a nie temat ERP, wypełnia się. W tym scenariuszu, ponieważ przedstawiciel handlowy nie otrzymał żadnych wiadomości, żaden z tematów ERP nigdy nie osiągnie przydziału.

> [!NOTE]
> Podczas automatycznego włączania jest ustawienie, wartość AutoDeleteOnIdle **na źródło i miejsce docelowe** jest automatycznie ustawiana na maksymalną wartość typu danych.
> 
>   - Po stronie Źródło autoforowanie działa jako operacja odbierania. Tak więc źródło, które ma konfigurację autoforwarding, nigdy nie jest "bezczynne".
>   - Po stronie docelowej odbywa się to w celu zapewnienia, że zawsze istnieje miejsce docelowe do przekazania wiadomości do.

## <a name="autoforwarding-considerations"></a>Zagadnienia dotyczące automatycznego dostosowania

Jeśli jednostka docelowa gromadzi zbyt wiele wiadomości i przekracza przydział lub jednostka docelowa jest wyłączona, jednostka źródłowa dodaje wiadomości do [kolejki utraconych wiadomości,](service-bus-dead-letter-queues.md) dopóki nie ma miejsca w miejscu docelowym (lub jednostka jest ponownie włączona). Te wiadomości nadal żyją w kolejce utraconych wiadomości, więc należy jawnie odbierać i przetwarzać je z kolejki utraconych wiadomości.

Podczas tworzenia łańcucha razem poszczególnych tematów w celu uzyskania tematu złożonego z wielu subskrypcji, zaleca się, że masz umiarkowaną liczbę subskrypcji na temat pierwszego poziomu i wiele subskrypcji na temat drugiego poziomu. Na przykład temat pierwszego poziomu z 20 subskrypcji, z których każdy jest łańcuchem do tematu drugiego poziomu z 200 subskrypcji, pozwala na wyższą przepływność niż temat pierwszego poziomu z 200 subskrypcji, każdy łańcuch do drugiego poziomu temat z 20 subskrypcji.

Usługa Service Bus rozlicza jedną operację dla każdej przekazanej wiadomości. Na przykład wysyłanie wiadomości do tematu z 20 subskrypcji, z których każdy skonfigurowany do automatycznego dobierania wiadomości do innej kolejki lub tematu, jest rozliczane jako 21 operacji, jeśli wszystkie subskrypcje pierwszego poziomu otrzymują kopię wiadomości.

Aby utworzyć subskrypcję, która jest przywiązywać do innej kolejki lub tematu, twórca subskrypcji musi mieć uprawnienia **Zarządzania** zarówno w encji źródłowej, jak i docelowej. Wysyłanie wiadomości do tematu źródłowego wymaga tylko **uprawnień Wyślij** w temacie źródłowym.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat automatycznego dostosowania, zobacz następujące tematy referencyjne:

* [Do przodu][QueueDescription.ForwardTo]
* [Scriptość kolejki][QueueDescription]
* [Scriptość subskrypcji][SubscriptionDescription]

Aby dowiedzieć się więcej o ulepszeniach wydajności usługi Service Bus, zobacz 

* [Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu komunikatów usługi Service Bus](service-bus-performance-improvements.md)
* [Jednostki obsługi wiadomości na partycje][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
