---
title: Lepiej wykorzystać możliwości usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Application Insights, w tym miejscu po Podsumowanie funkcji, które można eksplorować.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5d46b446a71d75a20cc4771b651fbf107db31358
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958445"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej funkcji telemetrii z usługi Application Insights
Po utworzeniu [dodawane usługi Application Insights do kodu ASP.NET](app-insights-asp-net.md), istnieje kilka kwestii, które można zrobić, aby uzyskać jeszcze więcej funkcji telemetrii. 

| Akcja | Efekty|
|---|---|
|(Na serwerach usług IIS) [Instalowanie Monitora stanu](http://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje sieci web platformy azure) W Panelu sterowania platformy Azure dla aplikacji sieci web Otwórz blok usługi Application Insights.| [**Liczniki wydajności**](app-insights-performance-counters.md)<br/>[**Wyjątki** ](app-insights-asp-net-exceptions.md) — szczegółowe ślady stosu<br/>[**Zależności**](app-insights-asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci web](app-insights-javascript.md)|[Wydajność strony](app-insights-usage-overview.md), wyjątków przeglądarki, wydajności AJAX. Niestandardowej telemetrii po stronie klienta.|
|[Tworzenie testów sieci web dostępności](app-insights-monitor-web-app-availability.md)|Otrzymuj alerty, jeśli witryna staje się niedostępny|
|[Upewnij się, buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) jest generowany przez program MSBuild|[Twórz adnotacje w wykresy metryk](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Pisanie niestandardowych zdarzeń i metryk](app-insights-api-custom-events-metrics.md)|Liczba zdarzeń biznesowych i metryki, śledzić szczegółowym zestawieniem użycia i nie tylko.|
|[Profil witryny na żywo](https://aka.ms/AIProfilerPreview)|Funkcja szczegółowe chronometrażu z aplikacji sieci web na żywo|






