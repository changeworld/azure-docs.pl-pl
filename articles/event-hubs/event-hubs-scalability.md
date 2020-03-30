---
title: Skalowalność — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje na temat skalowania usługi Azure Event Hubs przy użyciu partycji i jednostek przepływności.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280952"
---
# <a name="scaling-with-event-hubs"></a>Skalowanie za pomocą centrów zdarzeń

Istnieją dwa czynniki, które wpływają na skalowanie za pomocą centrów zdarzeń.
*   Jednostki przepływności
*   Partycje

## <a name="throughput-units"></a>Jednostki przepływności

Pojemność przepływności usługi Event Hubs jest kontrolowana przez *jednostki przepływności*. Jednostki przepływności to zakupione wcześniej jednostki pojemności. Jedna przepustowość umożliwia:

* Ruch przychodzący: do 1 MB na sekundę lub 1000 zdarzeń na sekundę (w zależności od tego, co nastąpi wcześniej).
* Wyjście: do 2 MB na sekundę lub 4096 zdarzeń na sekundę.

Po przekroczeniu pojemności zakupionych jednostek przepływności ruch przychodzący jest ograniczany i jest zwracany wyjątek [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Ruch wychodzący nie generuje wyjątków ograniczania, ale też jest ograniczony do pojemności zakupionych jednostek przepływności. Jeśli wystąpią wyjątki szybkości publikowania lub oczekiwany będzie większy transfer danych wychodzących, należy sprawdzić liczbę jednostek przepływności zakupionych dla przestrzeni nazw. Jednostkami przepływności można zarządzać w bloku **Skala** przestrzeni nazw w witrynie [Azure Portal](https://portal.azure.com). Jednostki przepływności można również programowo zarządzać za pomocą [interfejsów API Centrum zdarzeń](event-hubs-api-overview.md).

Jednostki przepływności są kupowane w przedsprzedaży i są naliczane za godzinę. Po zakupieniu jednostki przepływności są rozliczane za co najmniej jedną godzinę. Do 20 jednostek przepływności można kupić dla obszaru nazw centrum zdarzeń i są współużytkowane we wszystkich centrach zdarzeń w tej przestrzeni nazw.

Funkcja **Automatycznego zawyżania** centrów zdarzeń jest automatycznie skalowana w górę, zwiększając liczbę jednostek przepływności, aby spełnić potrzeby użytkowania. Zwiększenie jednostek przepływności zapobiega scenariuszom ograniczania przepustowości, w których:

- Szybkość transferu danych przychodzących przekracza jednostki przepływności.
- Szybkość żądania transferu danych wychodzących przekracza ustawione jednostki przepływności.

Usługa Usługi Usługi Event Hubs zwiększa przepływność, gdy obciążenie zwiększa się powyżej minimalnego progu, bez żadnych żądań w przypadku błędów ServerBusy. 

Aby uzyskać więcej informacji na temat funkcji automatycznego napompowynia, zobacz [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Klucz partycji

Za pomocą [klucza partycji](event-hubs-programming-guide.md#partition-key) można mapować przychodzące dane zdarzeń na określone partycje na potrzeby organizacji danych. Klucz partycji to wartość podawana przez nadawcę przekazywana do centrum zdarzeń. Jest on przetwarzany przez statyczną funkcję tworzenia skrótu, za pomocą której tworzone jest przypisanie partycji. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego.

Wydawca zdarzeń ma informacje tylko o kluczu partycji, a nie partycji, na której publikowane są zdarzenia. To oddzielenie klucza od partycji powoduje, że nadawca nie musi wiedzieć zbyt dużo o przetwarzaniu podrzędnym. Unikatowa tożsamość urządzenia lub użytkownika stanowi dobry klucz partycji, ale inne atrybuty, takie jak lokalizacja geograficzna, mogą również zostać użyte do grupowania powiązanych zdarzeń w jedną partycję.


## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md)
- [Omówienie usługi Usługi Usługi Usługi Event Hubs](event-hubs-what-is-event-hubs.md)
