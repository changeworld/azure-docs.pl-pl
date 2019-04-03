---
title: Używanie okienka ustawień usługi Azure Application Insights Profiler | Dokumentacja firmy Microsoft
description: Zobacz stan Profiler i uruchom profilowanie sesji
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884163"
---
# <a name="configure-application-insights-profiler"></a>Konfigurowanie narzędzia Application Insights Profiler

## <a name="profiler-settings-pane"></a>Okienko ustawień Profiler

Aby otworzyć okienko ustawienia usługi Azure Application Insights Profiler, przejdź do okienka Application Insights wydajności, a następnie wybierz **Profiler** przycisku.

![Konfigurowanie okienka Profiler][configure-profiler-entry]

**Skonfigurować Application Insights Profiler** okienko zawiera cztery funkcje: 
* **Profil teraz**: Rozpoczyna się profilowanie sesji dla wszystkich aplikacji, które są połączone z tym wystąpieniem usługi Application Insights.
* **Połączone aplikacje**: Wyświetla listę aplikacji, które wysyłają profilowania danych do tego zasobu usługi Application Insights.
* **Sesje w toku**: Wyświetla stan sesji po wybraniu **profilu teraz**. 
* **Ostatnie sesje profilowania**: Wyświetla informacje dotyczące ostatnich sesji profilowania.

![Profiler na żądanie][profiler-on-demand]

## <a name="app-service-environment"></a>Środowisko usługi App Service
W zależności od sposobu skonfigurowania środowiska Azure App Service Environment wywołanie, aby sprawdzić stan agenta może zostać zablokowany. Okienka może wyświetlić komunikat, który nie jest uruchomiony agent, nawet wtedy, gdy jest uruchomiona. Aby upewnić się, że jest, sprawdź zadanie webjob w swojej aplikacji. Jeśli wszystkie wartości ustawienia aplikacji są poprawne, a rozszerzenie witryny usługi Application Insights jest zainstalowany w swojej aplikacji, Profiler jest uruchomiona. Jeśli aplikacja odbiera ruch wystarczająco dużo, najnowsze sesje profilowania powinien zostać wyświetlony na liście.

## <a id="profileondemand"></a> Ręcznie wyzwalać Profiler

### <a name="minimum-requirements"></a>Minimalne wymagania 
Użytkownik może ręcznie wyzwolić sesji profilera wymagają co najmniej "" dostęp do zapisu w ich roli, składnika usługi Application Insights. W większości przypadków można uzyskać dostęp automatycznie i jest wymagane żadne dodatkowe czynności. Jeśli występują problemy, roli zakres subskrypcji, aby dodać będzie "Application Insights składnik rolę"współautor. [Zobacz więcej informacji na temat kontroli dostępu roli z usługą Azure Monitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control).

Profiler można uruchomić ręcznie za pomocą jednego kliknięcia. Załóżmy, że uruchomieniu testu wydajności sieci web. Potrzebna będzie śladów, które pomagają zrozumieć, jak działa aplikacja sieci web pod obciążeniem. Masz kontrolę nad kiedy są przechwytywane dane śledzenia jest niezwykle istotne, ponieważ wiadomo, kiedy zostanie uruchomiony test obciążeniowy. Ale interwał próbkowania losowe może jej przeoczyć.

W kolejnych sekcjach przedstawiono, jak działa w tym scenariuszu:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1: (Opcjonalnie) Generowanie ruch do aplikacji sieci web przez uruchomienie testu wydajności sieci web

Jeśli Twoja aplikacja sieci web ma już ruchu przychodzącego lub po prostu chcesz ręcznie wygenerować ruchu, Pomiń tę sekcję i przejdź do kroku 2.

1. W portalu usługi Application Insights wybierz **Konfiguruj** > **testowania Wydajnościowego**. 

1. Aby uruchomić nowy test wydajności, zaznacz **New** przycisku.

   ![Utwórz nowy test wydajności][create-performance-test]

1. W **nowego testu wydajności** okienko, skonfiguruj docelowy adres URL testu. Zaakceptuj wszystkie ustawienia domyślne, a następnie wybierz pozycję **Uruchom test** uruchomienia testu obciążenia.

    ![Konfigurowanie testu obciążeniowego][configure-performance-test]

    Nowy test znajduje się w kolejce najpierw następuje stan *w toku*.

    ![test obciążeniowy jest przesłane i umieszczonych w kolejce][load-test-queued]

    ![test obciążenia jest uruchomiony w toku][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2: Rozpocznij sesję na żądanie Profiler

1. Gdy uruchomiony jest test obciążeniowy, należy uruchomić Profiler do przechwytywania śladów, w usłudze web app, gdy odbiera obciążenia.

1. Przejdź do **skonfigurować Profiler** okienka.


### <a name="step-3-view-traces"></a>Krok 3: Wyświetlanie śladów

Po zakończeniu Profiler wykonaj instrukcje dotyczące powiadomień, aby przejść do okienka i widoku ślady wydajności.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Rozwiązywanie problemów z sesji na żądanie Profiler

Po sesji na żądanie można otrzymać komunikat o błędzie Profiler limit czasu:

![Błąd upływu limitu czasu Profiler][profiler-timeout]

Ten błąd może występować dla jednej z następujących powodów:

* Na żądanie sesji Profiler zakończyło się pomyślnie, ale usługi Application Insights trwała dłużej, niż oczekiwano przetwarzania zebranych danych.  

  Jeśli dane nie są przetwarzane w ciągu 15 minut, portalu jest wyświetlany komunikat o limicie czasu. Po krótkiej chwili jednak Profiler ślady pojawią się. Jeśli zostanie wyświetlony komunikat o błędzie, należy go zignorować teraz. Aktywnie pracujemy nad poprawką.

* Aplikacja sieci web ma starszą wersję agenta Profiler, który nie ma funkcji na żądanie.  

  Application Insights Profiler został włączony wcześniej, może być konieczne aktualizowanie Twojego agenta Profiler, aby rozpocząć korzystanie z funkcji na żądanie.
  
Przejdź do usług aplikacji **ustawienia aplikacji** okienku i wyszukaj następujące ustawienia:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Zamień na klucz Instrumentacji właściwe dla usługi Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Jeśli powyższe wartości nie są ustawione, należy zainstalować najnowsze rozszerzenia lokacji, wykonując następujące czynności:

1. Przejdź do **usługi Application Insights** okienku w portalu usługi App Services.

    ![Włącz usługę Application Insights z poziomu portalu usług aplikacji][enable-app-insights]

1. Jeśli **usługi Application Insights** okienko wyświetla **aktualizacji** przycisku, wybierz ją, aby zaktualizować rozszerzenie witryny usługi Application Insights, który zainstaluje najnowszą wersję agenta Profiler.

    ![Aktualizuj rozszerzenie witryny][update-site-extension]

1. Aby upewnić się, że Profiler jest włączone, wybierz pozycję **zmiany**, a następnie wybierz pozycję **OK** Aby zapisać zmiany.

    ![Zmiany i Zapisz usługi app insights][change-and-save-appinsights]

1. Wróć do **ustawienia aplikacji** okienko usługi aplikacji upewnić się, że są ustawione następujące wartości:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Zamień na klucz Instrumentacji właściwe dla usługi application insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Ustawienia aplikacji dla Profiler][app-settings-for-profiler]

1. Opcjonalnie można zaznaczyć **rozszerzenia**, a następnie sprawdź wersję rozszerzenia i określić, czy dostępna jest aktualizacja.

    ![Sprawdzanie aktualizacji rozszerzenia][check-for-extension-update]

## <a name="next-steps"></a>Kolejne kroki
[Włącz Profiler i przeglądać ślady](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
