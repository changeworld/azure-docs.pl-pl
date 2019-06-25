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
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276784"
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

Partycje pozwalają skalowaniu dla Twojego przetwarzania transmisji dla klientów. Ze względu na partycjonowanego modelu odbiorców oferująca usługi Event Hubs z partycjami użytkownik może skalowalnego w poziomie podczas przetwarzania zdarzeń jednocześnie. Centrum zdarzeń mogą mieć maksymalnie 32 partycjami.

Zalecane jest równoważenie jednostek przepływności 1:1 i partycji w celu osiągnięcia optymalnej skali. Jedna partycja ma gwarantowaną ruchu przychodzącego i wychodzącego więcej niż jedną jednostkę przepływności. Może być możliwe do uzyskania większej przepływności na partycji, wydajności nie jest gwarantowana. Jest to, dlaczego firma Microsoft zaleca, że liczba partycji w Centrum zdarzeń jest większa lub równa liczbie jednostek przepływności.

Biorąc pod uwagę łącznej przepływności, w którym planujesz wymagające, wiesz, że liczba jednostek przepływności, które są wymagane i minimalna liczba partycji, ale jak wiele partycji, należy mieć? Wybierz liczbę partycji, oparte na równoległością, który chcesz osiągnąć, jak również w zakresie przyszłych przepływności. Nie ma opłat za liczbę partycji, posiadanych w ramach Centrum zdarzeń.

Aby uzyskać szczegółowe informacje o cenach za korzystanie z usługi Event Hubs, zobacz [Usługa Event Hubs — cennik](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md)
- [Omówienie usługi Event Hubs usługi](event-hubs-what-is-event-hubs.md)
