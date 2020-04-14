---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263351"
---
Poniżej podsumowano limity kluczy w ogólnej dostępności.

### <a name="sku-ingress-rates-and-capacities"></a>Stawki i zdolności wnikaniu jednostek SKU

Szybkość i przepustowość jednostek SKU s1 i S2 zapewniają elastyczność podczas konfigurowania nowego środowiska usługi Time Series Insights. Pojemność jednostki SKU wskazuje dzienny współczynnik transferu ruchu przychodzącego na podstawie liczby przechowywanych zdarzeń lub bajtów, w zależności od tego, co nastąpi wcześniej. Należy zauważyć, że ruch przychodzący jest mierzony *na minutę,* a **ograniczanie** jest stosowane przy użyciu algorytmu zasobnika tokenu. Ruch przychodzący jest mierzony w blokach 1 KB. Na przykład rzeczywiste zdarzenie o rozmiarze 0,8 KB będzie mierzone jako jedno zdarzenie, a zdarzenie o rozmiarze 2,6 KB jest liczone jako trzy zdarzenia.

| Pojemność jednostki SKU S1 | Wskaźnik transferu wnikacza | Maksymalna pojemność pamięci masowej
| --- | --- | --- |
| 1 | 1 GB (1 milion zdarzeń) dziennie | 30 GB (30 milionów zdarzeń) miesięcznie |
| 10 | 10 GB (10 milionów zdarzeń) dziennie | 300 GB (300 milionów zdarzeń) miesięcznie |

| Pojemność jednostki SKU S2 | Wskaźnik transferu wnikacza | Maksymalna pojemność pamięci masowej
| --- | --- | --- |
| 1 | 10 GB (10 milionów zdarzeń) dziennie | 300 GB (300 milionów zdarzeń) miesięcznie |
| 10 | 100 GB (100 milionów wydarzeń) dziennie | 3 TB (3 miliardy zdarzeń) miesięcznie |

> [!NOTE]
> Możliwości są skalowane liniowo, więc jednostka SKU S1 o pojemności 2 obsługuje 2 GB (2 miliony) zdarzeń dziennie i 60 GB (60 milionów zdarzeń) miesięcznie.

Środowiska SKU S2 obsługują znacznie więcej zdarzeń miesięcznie i mają znacznie wyższą pojemność transferu przychodzącego.

| SKU  | Liczba zdarzeń na miesiąc  | Liczba zdarzeń na minutę | Rozmiar zdarzenia na minutę  |
|---------|---------|---------|---------|---------|
| S1     |   30 milionów   |  720    |  720 KB   |
 |S2     |   300 milionów   | 7,200   | 7200 KB  |

### <a name="property-limits"></a>Limity nieruchomości

Limity właściwości GA zależą od wybranego środowiska jednostki SKU. Podane właściwości zdarzeń mają odpowiednie kolumny JSON, CSV i wykresu, które można wyświetlać w [Eksploratorze wglądu w szeregi czasowe.](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)

| SKU | Maksymalna liczba właściwości |
| --- | --- |
| S1 | 600 właściwości (kolumny) |
| S2 | 800 właściwości (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie. 

* Dowiedz się, jak [dodać źródło centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurowanie [źródła centrum IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limity API

Limity interfejsu API REST dla ogólnej dostępności usługi Time Series Insights są określone w [dokumentacji referencyjnej interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).