---
title: Inteligentne wykrywania w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Usługa Application Insights odbywa się szczegółowa analiza automatycznego dotyczących telemetrii aplikacji i ostrzega o potencjalnych problemach.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 38a7c5be98440a717eb54092bf7ce0d412619017
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection-in-application-insights"></a>Inteligentne wykrywanie w usłudze Application Insights
 Inteligentne wykrywanie automatycznie ostrzega o potencjalnych problemów z wydajnością w aplikacji sieci web. Wykonuje aktywnego analizy telemetrii, wysyłanej przez aplikację do [usługi Application Insights](app-insights-overview.md). Jeśli istnieje nagły wzrost awariami lub nietypowe wzorce wydajności serwera lub klienta, zostanie wyświetlony alert. Ta funkcja wymaga żadnej konfiguracji. Działa on tak, jeśli aplikacja wyśle za mało danych telemetrii.

Alerty o wykryciu inteligentne są dostępne zarówno z wiadomości e-mail, który pojawi się, jak i z bloku inteligentne wykrywania.

## <a name="review-your-smart-detections"></a>Zapoznaj się z zagrożeń wykrywanych przez usługę inteligentnego
Może odnajdować wykryć na dwa sposoby:

* **Otrzymasz wiadomość e-mail** z usługi Application Insights. Oto typowy przykład:
  
    ![Alerty e-mail](./media/app-insights-proactive-diagnostics/03.png)
  
    Kliknij przycisk duży, aby otworzyć bardziej szczegółowo w portalu.
* **Na kafelku inteligentne wykrywanie** na Omówienie aplikacji bloku pokazuje liczbę ostatnich alertów. Kliknij Kafelek, aby wyświetlić listę ostatnich alertów.

![Wyświetl ostatnie wykryć](./media/app-insights-proactive-diagnostics/04.png)

Wybierz alert, aby wyświetlić jego szczegóły.

## <a name="what-problems-are-detected"></a>Jakie problemy zostaną wykryte?
Istnieją trzy typy wykrywania:

* [Inteligentne wykrywania — błąd anomalii](app-insights-proactive-failure-diagnostics.md). Używamy oczekiwana liczba nieudanych żądań dla aplikacji, ustaw uczenia maszynowego dopasowywanie obciążenia i innych czynników. Jeśli współczynnik awaryjności odbędzie się poza oczekiwanym koperty, możemy wysyłać alert.
* [Inteligentne wykrywania - anomalii wydajności](app-insights-proactive-performance-diagnostics.md). Otrzymywać powiadomień, jeśli odpowiedź czas trwania operacji lub zależności jest spowolnieniem w porównaniu do linii bazowej historyczne lub nazywamy nietypowych wzorca w czasie odpowiedzi lub czas ładowania strony.   
* [Inteligentne wykrywania — problemy z usługą w chmurze Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Uzyskiwanie alertów, jeśli aplikacja jest hostowana w usług Azure Cloud Services i wystąpienia roli ma uruchamianiem, często odtwarzania lub awarie środowiska wykonawczego.

(Łącza pomocy Każde powiadomienie prowadzą do odpowiednich artykułów.)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
Te narzędzia diagnostyczne pomóc sprawdzić dane telemetryczne z aplikacji:

* [Eksplorator metryk](app-insights-metrics-explorer.md)
* [Eksplorator wyszukiwania](app-insights-diagnostic-search.md)
* [Analiza - język zaawansowanych zapytań](app-insights-analytics-tour.md)

Wykrywanie inteligentne jest całkowicie automatyczne. A może chcesz skonfigurować niektóre alerty więcej?

* [Ręcznie skonfigurowanej metryki alertów](app-insights-alerts.md)
* [Dostępność testy sieci web](app-insights-monitor-web-app-availability.md) 

