---
title: Dostępność i spójność Azure Event Hubs | Dokumentacja firmy Microsoft
description: Jak zapewnić maksymalną ilość dostępności i spójności z usługi Azure Event Hubs przy użyciu partycji.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: e119406292ca1d805f831bc65e3ae6e583147c6d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700693"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostępność i spójności w usłudze Event Hubs

## <a name="overview"></a>Przegląd
Usługa Azure Event Hubs używa [partycjonowania modelu](event-hubs-features.md#partitions) do zwiększenia dostępności i paralelizacja w obrębie jednego zdarzenia koncentratora. Na przykład jeśli Centrum zdarzeń ma cztery partycje i jedną z tych partycji jest przenoszone z jednego serwera do drugiego w operacji z równoważeniem obciążenia, użytkownik może nadal wysyłania i odbierania z trzech innych partycji. Ponadto posiadanie więcej partycji pozwala mieć więcej jednoczesnych czytników przetwarzania danych, poprawy z łącznej przepływności. Opis skutków partycjonowanie i kolejność w rozproszonym systemie jest krytyczne aspektów projektu rozwiązania.

Aby wyjaśnić równowagę między porządkowania i dostępności, zobacz [Newtona zakończenia](https://en.wikipedia.org/wiki/CAP_theorem), znanej również jako Newtona Brewera firmy. Ta Newtona omówiono wybór między spójności, dostępności i tolerancję partycji. Go informujący o systemów partycjonowanego sieci zawsze zależności między spójności i dostępności.

Newtona firmy Brewera definiuje spójności i dostępności w następujący sposób:
* Partycji tolerancji: możliwość dalszego przetwarzania danych, nawet jeśli wystąpi błąd partycji systemu przetwarzania danych.
* Dostępność: węzłem z systemem innym niż niepowodzenie zwraca odpowiedź uzasadnione w rozsądnym czasie (z nie błędów lub przekroczenia limitu czasu).
* Spójności: odczytu gwarancji zwrócić ostatniego zapisu dla danego klienta.

## <a name="partition-tolerance"></a>Tolerancja partycji
Centra zdarzeń jest oparty na modelu danych podzielonej na partycje. Liczba partycji w Centrum zdarzeń można skonfigurować podczas instalacji, ale nie można później zmienić tę wartość. Ponieważ partycji musi być używany z usługą Event Hubs, trzeba podjąć decyzję o dostępności i spójności aplikacji.

## <a name="availability"></a>Dostępność
Najprostszym sposobem, aby rozpocząć pracę z usługą Event Hubs jest zachowanie domyślne. Jeśli tworzysz nową **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** obiektu i użyj **[wysyłania](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metody zdarzeń są automatycznie dystrybuowane między partycji w Centrum zdarzeń. Takie zachowanie umożliwia największą ilość czasu.

Dla przypadków użycia, które wymagają maksymalny czas działania ten model jest preferowany.

## <a name="consistency"></a>Spójność
W niektórych scenariuszach uporządkowania zdarzeń może być istotne. Na przykład możesz systemie zaplecza, aby przetworzyć polecenia aktualizacji przed polecenia delete. W takim przypadku można ustawić klucza partycji dla zdarzenia, lub użyj `PartitionSender` obiektu tylko do wysyłania zdarzeń do danej partycji. Daje to gwarancję, że kiedy te zdarzenia są odczytywane z partycji, są one odczytu w kolejności.

W tej konfiguracji należy pamiętać, że jeśli określonej partycji, do którego są wysyłane jest niedostępny, zostanie wyświetlony błąd odpowiedzi. Jako punkt odniesienia Jeśli nie masz koligacji do jednej partycji, usługę Event Hubs wysyła zdarzenie do następnej dostępnej partycji.

Agregowania zdarzeń jest możliwe rozwiązanie zapewniające porządkowanie zwiększają również czasu, w ramach wydarzenia przetwarzania aplikacji. Najprostszym sposobem, w tym celu jest sygnatury zdarzenie z właściwością numer sekwencji niestandardowych. Poniżej znajduje się kod przykładowy:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

W tym przykładzie wysyła zdarzenie do jednej z dostępnych partycji w Centrum zdarzeń i ustawia jej numer sekwencji z aplikacji. To rozwiązanie wymaga stanu, które mają być przechowywane przez aplikację przetwarzania, ale zapewnia Twojej nadawców punktu końcowego, który jest mogą być dostępne.

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs usługi](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
