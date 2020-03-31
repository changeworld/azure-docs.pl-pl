---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123140"
---
### <a name="general-availability-and-preview-comparison"></a>Ogólna dostępność i porównanie wersji zapoznawczej

W poniższej tabeli podsumowano kilka kluczowych różnic między ogólną dostępnością usługi Azure Time Series Insights (GA) a wystąpieniami w wersji zapoznawczej.

| | Ogólna dostępność | Wersja zapoznawcza |
| --- | --- | ---|
| Obywatel pierwszej klasy | Zorientowane na zdarzenia | Czas-zorientowany na szeregi |
| Rozumowanie semantyczne | Niski poziom (dane referencyjne) | Wysoki poziom (modele) |
| Kontekstowanie danych | Poziom niezwiązanych z urządzeniem | Poziom urządzenia i urządzenia niezwiązanego z urządzeniem |
| Pamięć masowa logiki obliczeniowej | Nie | Przechowywane w zmiennych typu część modelu |
| Przechowywanie i kontrola dostępu | Nie | Włączona za pośrednictwem modelu |
| Agregacje/pobieranie próbek | Nie | Ważony zdarzenie i ważony czas |
| Rekonstrukcja sygnału | Nie | Interpolacji |
| Produkcja pochodnych szeregów czasowych | Nie | Tak, łączy się i łączy |
| Elastyczność językowa | Nieskomisowalne | Można skomponować |
| Język wyrażeń | Ciąg predykatu | Wyrażenia szeregów czasowych (ciągi predykacji, wartości, wyrażenia i funkcje) |

### <a name="property-limits"></a>Limity nieruchomości

Limity właściwości usługi Time Series Insights wzrosły do 1000 z maksymalnego limitu 800 w ga. Podane właściwości zdarzeń mają odpowiednie kolumny JSON, CSV i wykresu, które można wyświetlić w [eksploratorze podglądu usługi Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maksymalna liczba właściwości |
| --- | --- |
| Podgląd PAYG | 1000 właściwości (kolumny) |
| GA S1 | 600 właściwości (kolumny) |
| GA S2 | 800 właściwości (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie. 

* Dowiedz się, jak [dodać źródło centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurowanie [źródła centrum IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Domyślnie [środowiska w wersji zapoznawczej obsługują szybkość transferu ruchu przychodzącego](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) do **1 megabajta na sekundę (MB/s) na środowisko.** Klienci mogą skalować swoje środowiska w wersji zapoznawczej do **16 MB/s przepływności,** jeśli to konieczne. Istnieje również limit na partycję **0,5 MB/s.** 

### <a name="api-limits"></a>Limity API

Limity interfejsu API REST dla usługi Time Series Insights Preview są określone w [dokumentacji referencyjnej interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
