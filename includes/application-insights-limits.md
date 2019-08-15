---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 7bf9c338315f5013be815625b06d633567a801a0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912448"
---
Istnieją pewne ograniczenia dotyczące liczby metryk i zdarzeń dla aplikacji, czyli według klucza Instrumentacji. Ograniczenia zależą od wybranego [planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

| Resource | Limit domyślny | Uwaga
| --- | --- | --- |
| Łączna ilość danych na dzień | 100 GB | Ilość danych możesz zmniejszyć, ustawiając limit. Jeśli potrzebujesz więcej danych, możesz zwiększyć limit w portalu, do 1 000 GB. W przypadku pojemności większej niż 1 000 GB Wyślij wiadomość e-mail AIDataCap@microsoft.comna adres.
| Ograniczanie przepływności | 32 000 zdarzeń/sekundę | Limit jest mierzony przez minutę.
| Przechowywanie danych | 90 dni | Ten zasób jest przeznaczony na potrzeby [wyszukiwania](../articles/azure-monitor/app/diagnostic-search.md), [analizy](../articles/azure-monitor/app/analytics.md) i [Eksploratora metryk](../articles/azure-monitor/app/metrics-explorer.md).
| Przechowywanie szczegółowych wyników [wieloetapowego testu dostępności](../articles/azure-monitor/app/availability-multistep.md) | 90 dni | Ten zasób zapewnia szczegółowe wyniki każdego kroku.
| Maksymalny rozmiar elementu telemetrii | 64 kB |
| Maksymalna liczba elementów telemetrii na partię | 64 K |
| Długość nazwy właściwości i metryki | 150 | Zobacz [schematy typów](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Długość ciągu wartości właściwości | 8192  | Zobacz [schematy typów](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Długość komunikatu śledzenia i wyjątku | 32 768  | Zobacz [schematy typów](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Liczba [testów dostępności](../articles/azure-monitor/app/monitor-web-app-availability.md) na aplikację | 100 |
| Przechowywanie danych [profilera](../articles/azure-monitor/app/profiler.md) | 5 dni |
| Dane [profilera](../articles/azure-monitor/app/profiler.md) wysłane dziennie | 10 GB |

Aby uzyskać informacje, zobacz [Informacje o zarządzaniu cenami i limitami przydziału dla usługi Application Insights](../articles/azure-monitor/app/pricing.md).