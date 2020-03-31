---
title: Więcej informacji o usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Po rozpoczęciu pracy z aplikacją Insights, oto podsumowanie funkcji, które można zbadać.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666028"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej danych telemetrycznych z usługi Application Insights
Po [dodaniu usługi Application Insights do kodu ASP.NET](../../azure-monitor/app/asp-net.md)istnieje kilka czynności, które można zrobić, aby uzyskać jeszcze więcej danych telemetrycznych. 

| Akcja | Efekty|
|---|---|
|(Serwery usług IIS) [Zainstaluj Monitor stanu](https://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje internetowe platformy Azure) W panelu sterowania platformy Azure dla aplikacji sieci web otwórz narzędzie Application Insights.| [**Liczniki wydajności**](../../azure-monitor/app/performance-counters.md)<br/>[**Wyjątki**](asp-net-exceptions.md) — szczegółowe ślady stosu<br/>[**Zależności**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Dodawanie fragmentu kodu JavaScript do stron internetowych](../../azure-monitor/app/javascript.md)|[Wydajność strony,](../../azure-monitor/app/usage-overview.md)wyjątki przeglądarki, wydajność AJAX. Niestandardowe dane telemetryczne po stronie klienta.|
|[Tworzenie testów sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)|Otrzymuj alerty, jeśli witryna stanie się niedostępna|
|[Upewnij się, że buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) jest generowany przez MSBuild|[Tworzenie adnotacji na wykresach metryk](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Pisanie niestandardowych zdarzeń i metryk](../../azure-monitor/app/api-custom-events-metrics.md)|Zlicz zdarzenia biznesowe i dane, śledź szczegółowe użycie i nie tylko.|
|[Profiluj swoją witrynę na żywo](https://aka.ms/AIProfilerPreview)|Szczegółowe chronometrażu funkcji z aplikacji sieci web na żywo|






