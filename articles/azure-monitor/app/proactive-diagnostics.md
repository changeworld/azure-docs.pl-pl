---
title: Inteligentne wykrywanie na platformie Azure Application Insights | Microsoft Docs
description: Application Insights przeprowadza automatyczne głębokiej analizy danych telemetrycznych aplikacji i ostrzega o potencjalnych problemach.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/07/2019
ms.openlocfilehash: ae9af5721e53277f6c939840721ca50ea67ca51e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818816"
---
# <a name="smart-detection-in-application-insights"></a>Inteligentne wykrywanie w Application Insights
 Inteligentne wykrywanie automatycznie ostrzega o potencjalnych problemach z wydajnością i anomaliach niepowodzeń w aplikacji sieci Web. Wykonuje proaktywnie analizę danych telemetrycznych wysyłanych przez aplikację do [Application Insights](../../azure-monitor/app/app-insights-overview.md). W przypadku nagłego wzrostu współczynnika błędów lub nietypowych wzorców wydajności klienta lub serwera zostanie wyświetlony alert. Ta funkcja nie wymaga konfiguracji. Działa, gdy aplikacja wysyła wystarczającą ilość danych telemetrycznych.

Możesz uzyskać dostęp do wykrycia wystawionego przez Inteligentne wykrywanie zarówno z otrzymywanych wiadomości e-mail, jak i z bloku inteligentnego wykrywania.

## <a name="review-your-smart-detections"></a>Przejrzyj Inteligentne wykrywanie
Wykrywanie wykrywania można przeprowadzić na dwa sposoby:

* **Otrzymasz wiadomość e-mail** od Application Insights. Oto typowy przykład:
  
    ![Alert e-mail](./media/proactive-diagnostics/03.png)
  
    Kliknij przycisk Big (duży), aby otworzyć więcej szczegółów w portalu.
* **Blok inteligentnego wykrywania** w Application Insights. Wybierz pozycję **Inteligentne wykrywanie** w menu **Zbadaj** , aby wyświetlić listę ostatnich wykryć.

![Wyświetlanie ostatnich wykryć](./media/proactive-diagnostics/04.png)

Wybierz wykrywanie, aby wyświetlić jego szczegóły.

## <a name="what-problems-are-detected"></a>Jakie problemy są wykrywane?
Inteligentne wykrywanie wykrywa różne problemy, takie jak:

* [Anomalie wykrywania inteligentnego](../../azure-monitor/app/proactive-failure-diagnostics.md). Używamy uczenia maszynowego, aby ustawić oczekiwaną liczbę żądań zakończonych niepowodzeniem dla aplikacji, skorelowanie z obciążeniem i innymi czynnikami. Jeśli współczynnik błędów wykracza poza oczekiwaną kopertę, wyślemy alert.
* [Wykrywanie inteligentne — anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md). Otrzymuję powiadomienia, jeśli czas odpowiedzi operacji lub czasu trwania zależności jest wolniejszy w porównaniu do historycznej linii bazowej lub w przypadku zidentyfikowania nietypowego wzorca w czasie odpowiedzi lub w czasie ładowania strony.   
* Ogólne degradacje i problemy, takie jak [Trace obniżeniu](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [przeciek pamięci](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [nietypowy wzrost ilości](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) i [antywzorce zabezpieczeń](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Linki pomocy w poszczególnych powiadomieniach przeprowadzą Cię do odpowiednich artykułów).

## <a name="smart-detection-email-notifications"></a>Powiadomienia e-mail dotyczące wykrywania inteligentnego

Wszystkie reguły inteligentnego wykrywania, z wyjątkiem reguł oznaczonych jako _wersja zapoznawcza_, są domyślnie skonfigurowane do wysyłania powiadomień e-mail po znalezieniu wykrycia.

Konfigurowanie powiadomień e-mail dla konkretnej reguły wykrywania inteligentnego można wykonać, otwierając blok **Ustawienia** inteligentnego wykrywania i wybierając regułę, co spowoduje otwarcie bloku **Edytuj regułę** .

Alternatywnie można zmienić konfigurację przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, [Zobacz temat zarządzanie Application Insights regułami wykrywania automatycznego przy użyciu szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) .

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają inspekcję danych telemetrycznych z aplikacji:

* [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Inteligentne wykrywanie jest całkowicie automatyczne. Ale być może chcesz skonfigurować więcej alertów?

* [Ręcznie skonfigurowane alerty metryk](../../azure-monitor/app/alerts.md)
* [Testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md) 

