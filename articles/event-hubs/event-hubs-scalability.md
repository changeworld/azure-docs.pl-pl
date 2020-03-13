---
title: Skalowalność — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat skalowania Event Hubs platformy Azure przy użyciu partycji i jednostek przepływności.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280952"
---
# <a name="scaling-with-event-hubs"></a>Skalowanie za pomocą Event Hubs

Istnieją dwa czynniki wpływające na skalowanie w Event Hubs.
*   Jednostki przepływności
*   Partycje

## <a name="throughput-units"></a>Jednostki przepływności

Pojemność przepływności usługi Event Hubs jest kontrolowana przez *jednostki przepływności*. Jednostki przepływności to zakupione wcześniej jednostki pojemności. Pojedyncza przepływność pozwala:

* Transfer danych przychodzących: Maksymalnie 1 MB na sekundę lub 1000 zdarzeń na sekundę (w zależności od tego osiągnięty pierwszy).
* Transfer danych wychodzących: Maksymalnie 2 MB na sekundę lub 4096 zdarzeń na sekundę.

Po przekroczeniu pojemności zakupionych jednostek przepływności ruch przychodzący jest ograniczany i jest zwracany wyjątek [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Ruch wychodzący nie generuje wyjątków ograniczania, ale też jest ograniczony do pojemności zakupionych jednostek przepływności. Jeśli wystąpią wyjątki szybkości publikowania lub oczekiwany będzie większy transfer danych wychodzących, należy sprawdzić liczbę jednostek przepływności zakupionych dla przestrzeni nazw. Jednostkami przepływności można zarządzać w bloku **Skala** przestrzeni nazw w [Azure Portal](https://portal.azure.com). Jednostki przepływności można także zarządzać programowo przy użyciu [interfejsów api Event Hubs](event-hubs-api-overview.md).

Jednostki przepływności to zakupione wcześniej i jest rozliczana godzinowo. Po zakupieniu jednostki przepływności są rozliczane za co najmniej jedną godzinę. Przepływność maksymalnie 20 jednostek dla przestrzeni nazw usługi Event Hubs można kupić i są udostępniane we wszystkich centrach zdarzeń w tej przestrzeni nazw.

Funkcja **automatycznego** rozszerzania Event Hubs automatycznie skaluje się w górę przez zwiększenie liczby jednostek przepływności w celu spełnienia wymagań dotyczących użycia. Zwiększenie jednostek przepływności zapobiega scenariuszy, w którym ograniczania przepływności:

- Stawki transferu danych przychodzących danych przekracza zestaw jednostek przepływności.
- Liczby żądań danych wychodzących przekracza zestaw jednostek przepływności.

Usługa Event Hubs zwiększa przepływność, gdy rośnie obciążenie operacjami po osiągnięciu progu minimalne, bez żadnych żądań kończy się niepowodzeniem z błędami ServerBusy. 

Aby uzyskać więcej informacji na temat funkcji automatycznego rozszerzania, zobacz [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Klucz partycji

[Klucz partycji](event-hubs-programming-guide.md#partition-key) służy do mapowania danych zdarzeń przychodzących na określone partycje na potrzeby organizowania danych. Klucz partycji to wartość podawana przez nadawcę przekazywana do centrum zdarzeń. Jest on przetwarzany przez statyczną funkcję tworzenia skrótu, za pomocą której tworzone jest przypisanie partycji. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego.

Wydawca zdarzeń ma informacje tylko o kluczu partycji, a nie partycji, na której publikowane są zdarzenia. To oddzielenie klucza od partycji powoduje, że nadawca nie musi wiedzieć zbyt dużo o przetwarzaniu podrzędnym. Unikatowa tożsamość urządzenia lub użytkownika stanowi dobry klucz partycji, ale inne atrybuty, takie jak lokalizacja geograficzna, mogą również zostać użyte do grupowania powiązanych zdarzeń w jedną partycję.


## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md)
- [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
