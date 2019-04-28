---
title: Dostępność i spójność — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Jak zapewnić maksymalną ilość dostępność i spójność z usługą Azure Event Hubs za pomocą partycji.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e5cad797b633d43bcc9ead657a60fca8aa6679bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822377"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostępność i spójność w usłudze Event Hubs

## <a name="overview"></a>Przegląd
Usługa Azure Event Hubs używa [partycjonowania modelu](event-hubs-features.md#partitions) do zwiększania dostępności i przetwarzanie równoległe w obrębie jednego Centrum zdarzeń. Na przykład jeśli Centrum zdarzeń ma cztery partycje, a jedna z tych partycji jest przenoszony z jednego serwera do drugiego w operacji równoważenia obciążenia, można nadal wysyłać i odbierać z trzech innych partycji. Ponadto istnienie więcej partycji umożliwia większą liczbę jednoczesnych czytników przetwarzania danych, poprawianie przepływności agregacji. Zrozumienia konsekwencji partycjonowanie i kolejność w rozproszonym systemie jest krytycznym aspektem projektowania rozwiązania.

Aby pomóc w wyjaśnieniu kompromisu między porządkowanie i dostępności, zobacz [kolejnego elementu teorii CAP](https://en.wikipedia.org/wiki/CAP_theorem), znanego również jako teorii Brewera firmy. Ta teorii w tym artykule omówiono wybór między spójnością, dostępnością i tolerancji partycji. Stwierdza, że systemów podzielona na partycje sieci jest zawsze zależnościami między spójności i dostępności.

Firmy Brewera kolejnego elementu teorii definiuje spójności i dostępności w następujący sposób:
* Partycje na uszkodzenia: możliwość dalszego przetwarzania danych, nawet w przypadku wystąpienia awarii partycji systemu przetwarzania danych.
* Dostępność: węzeł bez przechodzenia zwraca odpowiedź uzasadnione w rozsądnym czasie (przy użyciu nie błędów lub przekroczenia limitu czasu).
* Spójność: Odczyt gwarancję zwracania najnowszych zapisu dla danego klienta.

## <a name="partition-tolerance"></a>Tolerancja partycji
Usługa Event Hubs jest oparty na modelu danych podzielonych na partycje. Liczba partycji w Centrum zdarzeń można skonfigurować podczas instalacji, ale nie można później zmienić tę wartość. Ponieważ partycje musi być używany z usługą Event Hubs, należy podjąć decyzję o dostępności i spójności aplikacji.

## <a name="availability"></a>Dostępność
Najprostszym sposobem rozpoczęcia pracy z usługą Event Hubs jest Użyj zachowania domyślnego. Jeśli tworzysz nową **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** obiektu i użyj **[wysyłania](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metody, zdarzenia są automatycznie rozpraszane między partycji w Centrum zdarzeń. To zachowanie umożliwia największą ilość czasu.

Zastosowań, które wymagają maksymalny czas działania, aby uzyskać ten model jest preferowana.

## <a name="consistency"></a>Spójność
W niektórych scenariuszach uporządkowania zdarzeń może być istotne. Na przykład możesz używanego systemu zaplecza, aby przetworzyć polecenia aktualizacji przed polecenie delete. W tym wypadku można ustawić klucza partycji dla zdarzenia, lub użyj `PartitionSender` obiektu tylko do wysyłania zdarzeń do danej partycji. Daje to gwarancję, że gdy te zdarzenia są odczytywane z partycji, są odczytywane w kolejności.

W przypadku tej konfiguracji należy pamiętać o tym, że jeśli określonej partycji, do którego wysyłana jest niedostępny, nie możesz odbierać odpowiedź o błędzie. Jako punkt odniesienia Jeśli nie masz koligacje do pojedynczej partycji usługi Event Hubs wysyła zdarzenia do następna dostępna partycja.

Agregowanie zdarzeń jest jedno możliwe rozwiązanie, aby upewnić się, w kolejności, maksymalizując równocześnie również czas, w ramach Twojej aplikacji do przetwarzania zdarzeń. Najprostszym sposobem, aby osiągnąć ten cel jest sygnatura zdarzenia z właściwością numer sekwencji niestandardowych. Poniżej znajduje się kod przykładowy:

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

W tym przykładzie wysyła zdarzenia do jednej z dostępnych partycji w Centrum zdarzeń i ustawia odpowiedni numer sekwencji z aplikacji. To rozwiązanie wymaga stanu, które mają być przechowywane przez aplikację do przetwarzania, ale zapewnia Twojej nadawców punktu końcowego, który jest bardziej prawdopodobne, mają być dostępne.

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs usługi](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
