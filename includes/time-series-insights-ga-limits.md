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
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013694"
---
Poniżej przedstawiono podsumowanie kluczowych limitów dostępności.

### <a name="sku-ingress-rates-and-capacities"></a>Stawki i pojemności jednostki SKU

Stawki za transfery danych w warstwie S1 i S2 zapewniają elastyczność podczas konfigurowania nowego środowiska Time Series Insights.

| Pojemność jednostki SKU S1 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 1 GB (1 000 000 zdarzeń) | 30 GB (30 000 000 zdarzeń) miesięcznie |
| 10 | 10 GB (10 000 000 zdarzeń) | 300 GB (zdarzenia 300 000 000) miesięcznie |

| Pojemność jednostki SKU S2 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 10 GB (10 000 000 zdarzeń) | 300 GB (zdarzenia 300 000 000) miesięcznie |
| 10 | 100 GB (zdarzenia 100 000 000) | 3 TB (3 000 000 000 zdarzeń) miesięcznie |

> [!NOTE]
> Pojemności są skalowane liniowie, więc jednostka SKU S1 z pojemnością 2 obsługuje 2 GB (2 000 000) zdarzeń na dzień i 60 GB (60 000 000 zdarzeń) miesięcznie.

Środowiska jednostki SKU S2 obsługują znacznie więcej zdarzeń miesięcznie i mają znacznie wyższą wydajność transferu danych przychodzących.

| JSZ  | Liczba zdarzeń miesięcznie  | Rozmiar zdarzenia miesięcznie  | Liczba zdarzeń na minutę | Rozmiar zdarzenia na minutę  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 000 000    |   300 GB   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Limity właściwości

Limity właściwości GA zależą od wybranego środowiska jednostki SKU. Podane właściwości zdarzenia mają odpowiednie kolumny JSON, CSV i wykresu, które mogą być wyświetlane w [eksploratorze Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| JSZ | Właściwości maksymalne |
| --- | --- |
| S1 | Właściwości 600 (kolumny) |
| S2 | Właściwości 800 (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie. 

* Dowiedz się, jak [dodać Źródło centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Skonfiguruj [Źródło Centrum IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limity interfejsu API

Limity interfejsu API REST dla Time Series Insights ogólnej dostępności są określone w [dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).