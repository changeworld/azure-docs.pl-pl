---
title: Blok ustawień usługi Azure Application Insights Profiler | Dokumentacja firmy Microsoft
description: Zobacz stan profiler i uruchom profilowanie sesji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 81608dd7281ceddce7e0701535ad99e1c9e44315
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142430"
---
# <a name="configure-application-insights-profiler"></a>Konfigurowanie narzędzia Application Insights Profiler

## <a name="profiler-settings-page"></a>Strona Ustawienia Profiler

Na stronie ustawień profilera można otworzyć ze strony wydajność szczegółowych informacji w aplikacji, naciskając klawisz **Profiler** przycisku.

![Konfigurowanie wpis w okienku profilera][configure-profiler-entry]

Strona Konfigurowanie Application Insights Profiler zawiera cztery funkcje: 
1. **Profil teraz** — kliknięcie tego przycisku spowoduje, że sesje profilowania można uruchomić dla wszystkich aplikacji, które są połączone z tym wystąpieniem usługi Application Insights
1. **Połączone aplikacje** — lista aplikacji, wysyłając profiler do tego zasobu usługi Application Insights
1. **Sesje w toku** — po naciśnięciu klawisza **profilu teraz**, w tym miejscu będzie wyświetlany stan sesji)
1. **Ostatnie sesje profilowania** — zawiera informacje o ostatnich sesji profilowania.

![Profiler na żądanie][profiler-on-demand]

## <a name="app-service-environments-ase"></a>Środowiska App Service Environment (ASE)
W zależności od sposobu skonfigurowania środowiska ASE może być blokowany wywołanie, aby sprawdzić stan agenta. Ta strona Załóżmy, że agent nie jest uruchomiona, gdy w rzeczywistości jest. Zadania webjob można sprawdzić mieć pewność, że w swojej aplikacji. Ale jeśli wszystkie ustawienia aplikacji są prawidłowo zainstalowano rozszerzenie witryny usługi App Insights w swojej aplikacji, program profilujący zostanie uruchomiona i najnowsze sesje profilowania na liście powinien zostać wyświetlony, jeśli istnieje odpowiedni ruch do aplikacji.

## <a id="profileondemand"></a> Ręcznie wyzwalać Profiler

Profiler może być uruchamiane ręcznie, za pomocą kliknięcia jednego przycisku. Załóżmy, że uruchomieniu testu wydajności sieci web. Konieczne będzie śladów, które pomagają zrozumieć, jak działa aplikacja sieci web pod obciążeniem. Masz kontrolę nad kiedy są przechwytywane dane śledzenia ma podstawowe znaczenie, ponieważ wiadomo, gdy zostanie uruchomiony test obciążeniowy, ale interwał próbkowania losowe może jej przeoczyć.
Poniższe kroki pokazują, jak działa w tym scenariuszu:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Opcjonalnie) Krok 1: Generowanie ruch do aplikacji sieci web przez uruchomienie testu wydajności sieci web

Jeśli Twoja aplikacja sieci web ma już ruchu przychodzącego lub po prostu chcesz ręcznie wygenerować ruchu, Pomiń tę sekcję i przejdź do kroku 2.

Przejdź do portalu usługi Application Insights **Konfiguruj > testowania Wydajnościowego**. Kliknij nowy przycisk, aby rozpocząć nowy test wydajności.

![Utwórz nowy test wydajności][create-performance-test]

W **nowego testu wydajności** okienko, skonfiguruj docelowy adres URL testu. Zaakceptuj wszystkie ustawienia domyślne i uruchom test obciążenia.

![Konfigurowanie testu obciążeniowego][configure-performance-test]

Zobaczysz, że nowy test znajduje się w kolejce najpierw następuje stan "w toku".

![test obciążeniowy jest przesłane i umieszczonych w kolejce][load-test-queued]

![test obciążenia jest uruchomiony w toku][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Krok 2. uruchamianie profilera na żądanie

Gdy uruchomiony jest test obciążeniowy, firma Microsoft można uruchomić program profilujący do przechwytywania śladów, w usłudze web app, gdy odbiera obciążenia.
Przejdź do okienka Profiler skonfigurować:


### <a name="step-3-view-traces"></a>Krok 3: Wyświetlanie śladów

Po zakończeniu program profilujący, wykonaj instrukcje dotyczące powiadomień, aby przejść do strony i widoku ślady wydajności.

## <a name="troubleshooting-on-demand-profiler"></a>Rozwiązywanie problemów z profilera na żądanie

Czasami może zostać wyświetlony komunikat o błędzie limitu czasu Profiler po sesji na żądanie:

![Błąd upływu limitu czasu Profiler][profiler-timeout]

Istnieją dwie możliwe przyczyny Dlaczego zostanie wyświetlony ten błąd:

1. Sesji profilera na żądanie zakończyło się pomyślnie, ale usługi Application Insights trwała dłużej przetwarzania zebranych danych. Jeśli dane nie zostało zakończone, przetwarzanych w ciągu 15 minut, w portalu zostanie wyświetlony komunikat limitu czasu. Jednak po krótkiej chwili Profiler ślady pojawią się. W takim przypadku po prostu zignorować komunikat o błędzie. Aktywnie pracujemy nad poprawką.

1. Aplikacja sieci web ma starszą wersję agenta Profiler, który nie ma funkcji na żądanie. Jeśli profil usługi Application Insights został włączony wcześniej, jest szansa, że musisz zaktualizować swoje agenta Profiler, aby rozpocząć korzystanie z funkcji na żądanie.
  
Wykonaj następujące kroki, aby zaznaczyć i zainstalować najnowszą Profiler:

1. Przejdź do ustawień aplikacji usługi App i sprawdzić, jeśli są ustawione następujące ustawienia:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Zastąp klucz Instrumentacji właściwe dla usługi Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0, jeśli te ustawienia nie są ustawione, przejdź do okienka włączenie usługi Application Insights, aby zainstalować najnowsze rozszerzenie witryny.

1. Przejdź do okienka usługi Application Insights w portalu usługi App Services.

    ![Włącz usługę Application Insights w portalu usługi App Services][enable-app-insights]

1. Jeśli zostanie wyświetlony przycisk "Aktualizuj" na następującej stronie, kliknij go, aby zaktualizować rozszerzenie witryny usługi Application Insights, który zainstaluje najnowszą wersję agenta Profiler.

    ![Aktualizuj rozszerzenie witryny][update-site-extension]

1. Następnie kliknij przycisk **zmienić** zapewnić Profiler jest włączona i wybierz **OK** Aby zapisać zmiany.

    ![Zmiany i Zapisz usługi app insights][change-and-save-appinsights]

1. Wróć do **ustawienia aplikacji** kartę dla usługi App Service można dokładnie sprawdzić następujące elementy ustawienia aplikacji są ustawione:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Zastąp klucz Instrumentacji właściwe dla usługi application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Ustawienia aplikacji dla programu profilującego][app-settings-for-profiler]

1. Opcjonalnie sprawdź wersję rozszerzenia i upewnienie się, że żadna aktualizacja jest dostępna.

    ![Sprawdzanie aktualizacji rozszerzenia][check-for-extension-update]

## <a name="next-steps"></a>Następne kroki
[Włącz Profiler i wyświetlanie śladów](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png