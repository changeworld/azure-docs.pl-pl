---
title: Automatyczne przekazywanie jednostki do obsługi komunikatów usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Jak tworzyć łańcuchy kolejki usługi Service Bus lub subskrypcję do innej kolejki lub tematu.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: be23d919b0c96d6c9b96ee328d1b18ad978a9dcc
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2018
ms.locfileid: "29558096"
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Tworzenie łańcuchów jednostek usługi Service Bus z automatyczne przekazywanie

Magistrala usług *automatyczne przekazywanie* funkcja umożliwia łańcucha kolejki lub subskrypcję do innej kolejki lub temat, który jest częścią tego samego obszaru nazw. Gdy włączone jest automatyczne przekazywanie, usługi Service Bus automatycznie usuwa wiadomości, które są umieszczane w pierwszym kolejki lub subskrypcji (źródło) i umieszcza je w drugim kolejka lub temat (docelowy). Należy pamiętać, że jest nadal możliwe wysłać wiadomość bezpośrednio do jednostki docelowej. Ponadto nie jest możliwe łańcucha podkolejki, takich jak kolejki utraconych wiadomości, do innej kolejki lub tematu.

## <a name="using-auto-forwarding"></a>Przy użyciu automatycznego przekazywania

Aby umożliwić automatyczne przekazywanie przez ustawienie [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] lub [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] właściwości [QueueDescription] [ QueueDescription] lub [SubscriptionDescription] [ SubscriptionDescription] obiektów dla źródła, jak w programie Poniższy przykład:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Obiekt docelowy musi istnieć w momencie utworzenia jednostki źródłowej. Jeśli obiekt docelowy nie istnieje, Usługa Service Bus zwraca wyjątek po otrzymaniu monitu, aby utworzyć jednostki źródłowej.

Automatyczne przekazywanie umożliwia skalowanie poszczególnych tematu. Limity usługi Service Bus [liczba subskrypcji danego tematu](service-bus-quotas.md) do 2000. Może obsłużyć dodatkowe subskrypcji, tworząc tematy drugiego poziomu. Nawet jeśli użytkownik nie są powiązane przez ograniczenia usługi Service Bus na liczbę subskrypcji, dodawanie drugiego poziomu tematy może zwiększyć ogólną przepustowość części tematu.

![Automatyczne przekazywanie scenariusza][0]

Umożliwia także automatyczne przekazywanie rozdzielenie nadawcy wiadomości z odbiorcy. Rozważmy na przykład system ERP, który składa się z trzech modułów: kolejność przetwarzania, Zarządzanie zapasami i zarządzania relacjami z klientami. Każdy z tych modułów generuje wiadomości, które są umieszczonych w kolejce do odpowiedniego tematu. Alicja i Robert są przedstawicieli handlowych, którzy chcą wszystkie komunikaty, które dotyczą klientów. Do otrzymywania tych wiadomości, Alicja, jak i Robert Utwórz kolejkę osobistych i subskrypcji na wszystkich tematów ERP, które automatycznie przekazywać komunikaty do ich kolejki.

![Automatyczne przekazywanie scenariusza][1]

Jeśli Alicja odbędzie się na urlopie, swoje osobiste kolejki, a nie w temacie ERP, wypełnia. W tym scenariuszu ponieważ przedstawiciel handlowy nie odebrał żadnych wiadomości Brak tematów ERP kiedykolwiek łączność przydziału.

## <a name="auto-forwarding-considerations"></a>Zagadnienia dotyczące automatyczne przekazywanie

Jeśli obiekt docelowy akumuluje zbyt wiele komunikatów i przekracza limit przydziału lub jednostki docelowej jest wyłączona, jednostki źródłowej dodaje komunikaty do jego [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) miejsce w miejscu docelowym (lub jednostki ponownie jest włączona). Te komunikaty w dalszym ciągu na żywo w kolejce wiadomości utraconych, musisz jawnie odbierania i ich przetwarzania z kolejki utraconych wiadomości.

Podczas łączenia ze sobą poszczególne tematy, aby uzyskać złożone temat o wielu subskrypcji, zaleca się, że masz umiarkowanej liczbie subskrypcje tematu pierwszego stopnia i subskrypcje wiele tematów drugiego poziomu. Na przykład tematu pierwszego stopnia, z subskrypcją 20, każde z nich umożliwia temat drugiego poziomu z subskrypcją 200, umożliwia wyższej przepustowości niż tematu pierwszego stopnia, z subskrypcją 200, każdy powiązany łańcuchem zależności z 20 subskrypcje tematu drugiego poziomu.

Usługa Service Bus rachunków jedna operacja dla każdej przekazany dalej komunikat. Na przykład wysyłania komunikatu do tematu z subskrypcją 20, każde z nich jest skonfigurowany do automatycznego przekazywania wiadomości do innej kolejki lub tematu, jest on rozliczany jako operacje 21 wszystkie subskrypcje pierwszego stopnia otrzymania kopia wiadomości.

Aby utworzyć subskrypcję, który jest powiązany łańcuchem zależności innego kolejka lub temat, twórca subskrypcji musi być **Zarządzaj** uprawnienia na serwerze źródłowym, jak i jednostki docelowej. Wysyłanie komunikatów do tematu źródła wymaga tylko **wysyłania** uprawnienia na temat źródła.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe informacje o przesyłaniu dalej automatycznego zobacz następujące tematy odwołania:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Aby dowiedzieć się więcej na temat usprawnień wydajności usługi Service Bus, zobacz 

* [Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu usługi magistrali komunikatów](service-bus-performance-improvements.md)
* [Partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
