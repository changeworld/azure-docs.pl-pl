---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407839"
---
Istnieją pewne ograniczenia liczby metryk i zdarzeń w każdej aplikacji, czyli na klucz instrumentacji. Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Zasób | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz większej ilości danych, możesz zwiększyć limit, w portalu do 1000 GB. Większe niż 1000 GB pojemności, Wyślij wiadomość e-mail do AIDataCap@microsoft.com.
| Ograniczanie przepływności | 32000 zdarzeń/s | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/azure-monitor/app/diagnostic-search.md), [analizy](../articles/azure-monitor/app/analytics.md) i [Eksploratora metryk](../articles/azure-monitor/app/metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar zdarzenia | 64,000 |
| Długość nazwy właściwości i metryki | 150 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość ciągu wartości właściwości | 8192 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość komunikatu śledzenia i wyjątku | 32,768  | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) przechowywanie danych | 5 dni |
| [Profiler](../articles/azure-monitor/app/profiler.md) danych wysyłanych dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/azure-monitor/app/pricing.md).