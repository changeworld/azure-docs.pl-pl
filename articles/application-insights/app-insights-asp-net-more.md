---
title: Lepiej wykorzystać usługę Azure Application Insights | Dokumentacja firmy Microsoft
description: Po pierwsze kroki z usługą Application Insights, poniżej przedstawiono podsumowanie funkcji, które można eksplorować.
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
ms.openlocfilehash: 2f03083367de4e818bdc953ab76c28ff687f0a48
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294340"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej danych telemetrycznych z usługi Application Insights
Po utworzeniu [dodać usługi Application Insights w kodzie ASP.NET](app-insights-asp-net.md), jest kilka rzeczy, które można wykonać, aby uzyskać większą telemetrii. 

| Akcja | Efekty|
|---|---|
|(Serwery IIS) [Zainstaluj Monitor stanu](http://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze z serwerem.<br/>(Aplikacje sieci web platformy azure) W Panelu sterowania systemu Azure dla aplikacji sieci web Otwórz blok usługi Application Insights.| [**Liczniki wydajności**](app-insights-performance-counters.md)<br/>[**Wyjątki** ](app-insights-asp-net-exceptions.md) — szczegółowe dane śledzenia stosu<br/>[**Zależności**](app-insights-asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci web](app-insights-javascript.md)|[Strona wydajności](app-insights-web-track-usage.md), wyjątki przeglądarki, wydajność AJAX. Telemetria niestandardowa po stronie klienta.|
|[Tworzenie testów sieci web dostępności](app-insights-monitor-web-app-availability.md)|Alerty, jeśli witryna jest niedostępny|
|[Upewnij się, buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) jest generowany przez program MSBuild|[Adnotacje w wykresach metryki kompilacji](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Pisanie niestandardowych zdarzeń i metryk](app-insights-api-custom-events-metrics.md)|Liczba zdarzeń biznesowych i metryki, śledzić szczegółowe dane użycia i inne.|
|[Profil witryny na żywo](https://aka.ms/AIProfilerPreview)|Czasy szczegółowe funkcji z aplikacji sieci web na żywo|






