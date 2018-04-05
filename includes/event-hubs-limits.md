---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
W poniższej tabeli wymieniono przydziały i limity specyficzne dla [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje o cenach usługi Event Hubs, zobacz [cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limit | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- | --- |
| Liczba centrów zdarzeń na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania w celu utworzenia nowego Centrum zdarzeń zostaną odrzucone. |10 |
| Liczba partycji na Centrum zdarzeń |Jednostka |- |32 |
| Liczba grup odbiorców, na Centrum zdarzeń |Jednostka |- |20 |
| Liczba połączeń protokołu AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dotyczące dodatkowych połączeń zostaną odrzucone i wyjątek jest odbierany przez kod wywołujący. |5000 |
| Maksymalny rozmiar zdarzenia usługi Event Hubs|Jednostka |- |256 KB |
| Maksymalny rozmiar nazwy Centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba innych niż epoka odbiorcy dla każdej grupy odbiorców |Jednostka |- |5 |
| Maksymalny okres przechowywania danych zdarzenia |Jednostka |- |1-7 dni |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostki przepływności powoduje, że dane, aby zdławionych i generuje  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Możesz poprosić o większej liczby jednostek przepływności dla standardowej warstwy przy zgłoszenia [żądania obsługi](/azure/azure-supportability/how-to-create-azure-support-request). [Jednostki przepływności dodatkowe](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach po 20 na podstawie zakupu zatwierdzone. |20 |
| Liczba reguł autoryzacji na przestrzeń nazw |Przestrzeń nazw|Kolejne żądania tworzenia reguły autoryzacji zostaną odrzucone.|12 |
