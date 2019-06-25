---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: fb57d8322cfd3f72862dc8edd1d2e231338a66de
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305315"
---
Istnieją pewne ograniczenia liczby metryk i zdarzeń w każdej aplikacji, czyli na klucz instrumentacji. Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Resource | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz większej ilości danych, możesz zwiększyć limit, w portalu do 1000 GB. Większe niż 1000 GB pojemności, Wyślij wiadomość e-mail do AIDataCap@microsoft.com.
| Ograniczanie przepływności | 32000 zdarzeń/s | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/azure-monitor/app/diagnostic-search.md), [analizy](../articles/azure-monitor/app/analytics.md) i [Eksploratora metryk](../articles/azure-monitor/app/metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/availability-multistep.md) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar zdarzenia | 64,000 |
| Długość nazwy właściwości i metryki | 150 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość ciągu wartości właściwości | 8192 | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Długość komunikatu śledzenia i wyjątku | 32,768  | Zobacz [wpisz schematów](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) przechowywanie danych | 5 dni |
| [Profiler](../articles/azure-monitor/app/profiler.md) danych wysyłanych dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/azure-monitor/app/pricing.md).