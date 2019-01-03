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
ms.openlocfilehash: 0dbc0834d5f5b87a39ae33d296af847cf4e368e3
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977467"
---
Istnieją pewne ograniczenia liczby metryk i zdarzeń na aplikację (czyli na klucz instrumentacji). Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Zasób | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz większej ilości danych, możesz zwiększyć limit, w portalu do 1000 GB. Dla większych niż 1000 GB pojemności, Wyślij wiadomość e-mail do AIDataCap@microsoft.com.
| Ograniczanie przepływności | Zdarzenia 32 KB/s | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/azure-monitor/app/diagnostic-search.md), [analizy](../articles/azure-monitor/app/analytics.md) i [Eksploratora metryk](../articles/application-insights/app-insights-metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar zdarzenia | 64 K | 
| Długość nazwy właściwości i metryki | 150 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość ciągu wartości właściwości | 8192 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość komunikatu śledzenia i wyjątku | 10 K | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| [Profiler](../articles/application-insights/app-insights-profiler.md) przechowywanie danych | 5 dni |
| [Profiler](../articles/application-insights/app-insights-profiler.md) danych wysyłanych dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/application-insights/app-insights-pricing.md).