---
title: Skalowalność — usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Zawiera informacje na temat skalowania usługi Azure Event Hubs.
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
ms.openlocfilehash: c46b333f2cc304cc12ddf78670b60940c7bc0db3
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827660"
---
# <a name="scaling-with-event-hubs"></a>Skalowanie za pomocą usługi Event Hubs

Istnieją dwa czynniki, które mają wpływ na skalowanie za pomocą usługi Event Hubs.
*   Jednostki przepływności
*   Partycje

## <a name="throughput-units"></a>Jednostki przepływności

Pojemność przepływności usługi Event Hubs jest kontrolowana przez *jednostki przepływności*. Jednostki przepływności to zakupione wcześniej jednostki pojemności. Przepływność pojedynczego zapewnia następujące możliwości:

* Transfer danych przychodzących: Do 1 MB na sekundę lub 1000 zdarzeń na sekundę (w zależności od tego osiągnięty pierwszy).
* Transfer danych wychodzących: Do 2 MB na sekundę lub 4096 zdarzeń na sekundę.

Po przekroczeniu pojemności zakupionych jednostek przepływności ruch przychodzący jest ograniczany i jest zwracany wyjątek [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Ruch wychodzący nie generuje wyjątków ograniczania, ale też jest ograniczony do pojemności zakupionych jednostek przepływności. Jeśli wystąpią wyjątki szybkości publikowania lub oczekiwany będzie większy transfer danych wychodzących, należy sprawdzić liczbę jednostek przepływności zakupionych dla przestrzeni nazw. Jednostkami przepływności można zarządzać na **skalowania** bloku przestrzeni nazw w [witryny Azure portal](https://portal.azure.com). Można również zarządzać programowo przy użyciu wszystkich jednostek przepływności [Event Hubs API](event-hubs-api-overview.md).

Jednostki przepływności to zakupione wcześniej i jest rozliczana godzinowo. Po zakupieniu jednostki przepływności są rozliczane za co najmniej jedną godzinę. Przepływność maksymalnie 20 jednostek dla przestrzeni nazw usługi Event Hubs można kupić i są udostępniane we wszystkich centrach zdarzeń w tej przestrzeni nazw.

**Automatyczne rozszerzanie** funkcji usługi Event Hubs automatycznie jest skalowany w górę, zwiększając liczbę jednostek przepływności, aby zaspokoić potrzeby użycia wymagań. Zwiększenie jednostek przepływności zapobiega scenariuszy, w którym ograniczania przepływności:

- Stawki transferu danych przychodzących danych przekracza zestaw jednostek przepływności.
- Liczby żądań danych wychodzących przekracza zestaw jednostek przepływności.

Usługa Event Hubs zwiększa przepływność, gdy rośnie obciążenie operacjami po osiągnięciu progu minimalne, bez żadnych żądań kończy się niepowodzeniem z błędami ServerBusy. 

Aby uzyskać więcej informacji na temat automatyczne rozszerzanie funkcji, zobacz [automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Klucz partycji

[Klucz partycji](event-hubs-programming-guide.md#partition-key) służy do mapowania danych zdarzeń przychodzących na określone partycje na potrzeby organizowania danych. Klucz partycji to wartość podawana przez nadawcę przekazywana do centrum zdarzeń. Jest on przetwarzany przez statyczną funkcję tworzenia skrótu, za pomocą której tworzone jest przypisanie partycji. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego.

Wydawca zdarzeń ma informacje tylko o kluczu partycji, a nie partycji, na której publikowane są zdarzenia. To oddzielenie klucza od partycji powoduje, że nadawca nie musi wiedzieć zbyt dużo o przetwarzaniu podrzędnym. Unikatowa tożsamość urządzenia lub użytkownika stanowi dobry klucz partycji, ale inne atrybuty, takie jak lokalizacja geograficzna, mogą również zostać użyte do grupowania powiązanych zdarzeń w jedną partycję.


## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md)
- [Omówienie usługi Event Hubs usługi](event-hubs-what-is-event-hubs.md)
