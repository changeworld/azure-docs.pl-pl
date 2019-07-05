---
title: Inteligentne wykrywanie w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Usługa Application Insights wykonuje automatyczne szczegółowej analizy telemetrii aplikacji i ostrzega o potencjalnych problemach.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465895"
---
# <a name="smart-detection-in-application-insights"></a>Wykrywanie inteligentne w usłudze Application Insights
 Wykrywanie inteligentne automatycznie ostrzega o potencjalnych problemów z wydajnością i anomalie w aplikacji sieci web. Wykonuje aktywnego analizy telemetrii, że aplikacja wysyła do [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Jeśli istnieje nagły wzrost częstotliwości awarii lub nietypowe wzorce wydajności klienta lub serwera, zostanie wyświetlony alert. Ta funkcja wymaga żadna konfiguracja. Działa on tak, jeśli aplikacja wysyła taką ilość telemetrii.

Można uzyskać dostęp do wykrywania wystawiony przez wykrywania inteligentnego, zarówno z wiadomości e-mail, które otrzymujesz, jak i w bloku wykrywania inteligentnego.

## <a name="review-your-smart-detections"></a>Zapoznaj się z rozwiązaniami do wykrywania inteligentnego
Możesz odkryć wykrywania na dwa sposoby:

* **Otrzymasz wiadomość e-mail** z usługi Application Insights. Oto typowy przykład:
  
    ![Alert e-mail](./media/proactive-diagnostics/03.png)
  
    Kliknij przycisk duże, aby otworzyć bardziej szczegółowo w portalu.
* **W bloku wykrywania inteligentnego** w usłudze Application Insights. Wybierz **wykrywania inteligentnego** w obszarze **zbadaj** menu, aby wyświetlić listę zagrożeń wykrywanych przez ostatnie.

![Wyświetl ostatnie wykrywania](./media/proactive-diagnostics/04.png)

Wybierz opcję wykrywania, aby wyświetlić jego szczegóły.

## <a name="what-problems-are-detected"></a>Jakie problemy są wykrywane?
Wykrywanie inteligentne wykrywa i powiadamia o różnych problemów, takich jak:

* [Wykrywanie inteligentne — anomalie](../../azure-monitor/app/proactive-failure-diagnostics.md). Firma Microsoft korzysta z uczenia maszynowego można ustawić oczekiwana liczba nieudanych żądań dla aplikacji, korelacji z obciążeniem i innych czynników. Współczynnik błędów przekroczy oczekiwanego koperty, wysyłamy alertu.
* [Inteligentne wykrywanie - anomalie wydajność](../../azure-monitor/app/proactive-performance-diagnostics.md). Otrzymuj powiadomienia, jeśli czas reakcji operacji lub zależności czasu trwania spowalnia w porównaniu do historycznych linii bazowych lub nazywamy nietypowego wzorca w czasie odpowiedzi lub czas ładowania strony.   
* Ogólne spadku wydajności i zagadnienia, takie jak [śledzenia znacznemu obniżeniu](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [przeciek pamięci](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [nieprawidłowa wzrost ilości wyjątków](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) i [zabezpieczeń niezalecane wzorce dotyczące](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Każde powiadomienie łącza pomocy prowadzą do odpowiednich artykułów.)

## <a name="smart-detection-email-notifications"></a>Powiadomienia e-mail w usłudze inteligentnego wykrywania

Wszystkie reguły wykrywania inteligentnego, z wyjątkiem zasady oznaczone jako _Podgląd_, są skonfigurowane domyślnie do wysyłania powiadomień e-mail, gdy zostaną odnalezione wykrywania.

Konfigurowanie powiadomień e-mail o określonej reguły wykrywania inteligentnego może odbywać się przez otwarcie wykrywania inteligentnego **ustawienia** bloku i wybierając pozycję reguły, co spowoduje otwarcie **edycji reguły** bloku.

Alternatywnie można zmienić konfiguracji za pomocą szablonów usługi Azure Resource Manager. [Zobacz reguły wykrywania inteligentnego zarządzania usługi Application Insights przy użyciu szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) Aby uzyskać więcej informacji.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych telemetrycznych z Twojej aplikacji:

* [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Wykrywanie inteligentne jest całkowicie automatyczny. A może chcesz skonfigurować niektóre alerty więcej?

* [Ręcznie skonfigurowane alertów dotyczących metryk](../../azure-monitor/app/alerts.md)
* [Testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md) 

