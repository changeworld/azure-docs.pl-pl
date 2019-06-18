---
title: Wydajność aplikacji — często zadawane pytania — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępności, wydajności i problemów z aplikacjami w funkcji Web Apps w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: f455985d2a7d05f45100d4a88b43c688fe1a7767
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65955755"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Wydajność aplikacji — często zadawane pytania dla aplikacji sieci Web na platformie Azure

> [!NOTE]
> Niektóre z poniższych wytycznych może działać wyłącznie względem Windows lub Linux App Services. Na przykład Linux App Services są domyślnie uruchamiane w trybie 64-bitowym.
>

W tym artykule znajdują się odpowiedzi na często zadawane pytania (FAQ) dotyczących problemów z wydajnością aplikacji [funkcji Web Apps w usłudze Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Dlaczego jest Moja aplikacja wolno?

Wiele czynników może przyczynić się do obniżyć wydajność aplikacji. Aby uzyskać szczegółowe kroki rozwiązywania problemów, zobacz [wydajność aplikacji sieci web powolne Rozwiązywanie problemów](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak rozwiązywać problemy z wysoką scenariusz użycia procesora CPU

W niektórych scenariuszach wysokie użycie procesora CPU aplikacji naprawdę może wymagać większej ilości zasobów obliczeniowych. W takiej sytuacji należy rozważyć skalowanie do wyższego poziomu usługi, dzięki czemu aplikacja pobiera wszystkie zasoby, których potrzebuje. W innych sytuacjach może być spowodowany wysokie użycie procesora CPU, przez pętlę zły lub rozwiązaniem w zakresie kodowania. Uzyskiwanie szczegółowych informacji co jest przyczyną zwiększone użycie procesora CPU to proces składający się z dwóch części. Najpierw utwórz zrzut procesu, a następnie analizować zrzut procesu. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie pliku zrzutu dla wysokiego użycia procesora CPU dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak rozwiązywać scenariusz wysokie zużycie pamięci?

W niektórych scenariuszach wysokie zużycie pamięci aplikacji naprawdę może wymagać większej ilości zasobów obliczeniowych. W takiej sytuacji należy rozważyć skalowanie do wyższego poziomu usługi, dzięki czemu aplikacja pobiera wszystkie zasoby, których potrzebuje. W innych sytuacjach usterki w kodzie mogą spowodować przeciek pamięci. Rozwiązaniem w zakresie kodowania może również zwiększyć użycie pamięci. Uzyskiwanie szczegółowych informacji co jest przyczyną dużą ilość pamięci, użycie jest proces dwuetapowy. Najpierw utwórz zrzut procesu, a następnie analizować zrzut procesu. Diagnostyki awarii przy użyciu galerii rozszerzeń witryny Azure mogą efektywnie oba te kroki. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie pliku zrzutu dla sporadyczne dużą ilość pamięci dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak zautomatyzować aplikacje internetowe usługi App Service przy użyciu programu PowerShell?

Można użyć poleceń cmdlet programu PowerShell do zarządzania i obsługi aplikacji sieci web usługi App Service. Z naszego wpisu w blogu [automatyzacja aplikacji sieci web hostowanych w usłudze Azure App Service przy użyciu programu PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), opisujemy sposób użycia polecenia cmdlet oparte na usłudze Azure Resource Manager programu PowerShell do automatyzacji typowych zadań. Wpis w blogu ma również przykładowy kod dla różnych zadań zarządzania aplikacji sieci web. Opisy i składnia dla wszystkich poleceń cmdlet aplikacji internetowych usługi App Service, zobacz [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak wyświetlić dzienniki zdarzeń aplikacji sieci web?

Aby wyświetlić dzienniki zdarzeń aplikacji sieci web:

1. Zaloguj się do Twojej [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz z menu **konsoli debugowania** > **CMD**.
3. Wybierz **LogFiles** folderu.
4. Aby wyświetlić dzienniki zdarzeń, wybierz ikonę ołówka obok **eventlog.xml**.
5. Aby pobrać dzienniki, uruchom polecenie cmdlet programu PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak przechwycić zrzut pamięci trybu użytkownika w mojej aplikacji sieci web?

Aby przechwycić zrzut pamięci trybu użytkownika w aplikacji sieci web:

1. Zaloguj się do Twojej [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz **Eksplorator procesów** menu.
3. Kliknij prawym przyciskiem myszy **w3wp.exe** procesu lub proces zadania WebJob.
4. Wybierz **Pobierz zrzut pamięci** > **pełny zrzut**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak wyświetlić informacje o poziomie procesu dla mojej aplikacji sieci web?

Masz dwie opcje do wyświetlania informacji na poziomie procesu dla aplikacji sieci web:

*   W witrynie Azure Portal:
    1. Otwórz **Eksplorator procesów** dla aplikacji sieci web.
    2. Aby wyświetlić szczegóły, wybierz **w3wp.exe** procesu.
*   W konsoli Kudu:
    1. Zaloguj się do Twojej [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Wybierz **Eksplorator procesów** menu.
    3. Aby uzyskać **w3wp.exe** procesu, wybierz opcję **właściwości**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Wskaż mojej aplikacji widzę "Błąd 403 — ta aplikacja sieci web została zatrzymana." Jak rozwiązać ten problem?

Trzy warunki mogą być przyczyną tego błędu:

* Aplikacja sieci web osiągnęła limit rozliczeniowy i witryna została wyłączona.
* Aplikacja sieci web została zatrzymana w portalu.
* Aplikacja sieci web osiągnął limit przydziału zasobów, które mogą być stosowane do bezpłatnej lub udostępnione skalowania planu usług.

Aby zobaczyć, co jest przyczyną błędu i aby rozwiązać ten problem, wykonaj kroki opisane w [aplikacji sieci Web: "Błąd 403 — ta aplikacja sieci web została zatrzymana"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Gdzie można dowiedzieć się więcej na temat limitów przydziału i limity dla różnych planów usługi App Service?

Aby uzyskać informacji na temat limitów przydziału i limity, zobacz [limity usługi App Service](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak skrócić czas odpowiedzi na pierwsze żądanie po upływie czasu bezczynności?

Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynne na pewien okres czasu. Dzięki temu system zachować zasoby. Minusem jest dłuższy, aby umożliwić aplikacji sieci web załadować i uruchomić jednoczesnej obsłudze odpowiedzi odpowiedzi na pierwsze żądanie po aplikacji sieci web jest zwalniana. W planach usługi podstawowa i standardowa, można włączyć **Always On** ustawienie, aby zachować aplikacji zawsze ładowany. Pozwala to wyeliminować dłuższy czas ładowania po aplikacji jest w stanie bezczynności. Aby zmienić **Always On** ustawienia:

1. W witrynie Azure portal przejdź do aplikacji sieci web.
2. Wybierz **ustawienia aplikacji**.
3. Aby uzyskać **Always On**, wybierz opcję **na**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak włączyć śledzenie niepomyślnych żądań

Aby włączyć śledzenie żądań zakończonych niepowodzeniem:

1. W witrynie Azure portal przejdź do aplikacji sieci web.
3. Wybierz **wszystkie ustawienia** > **dzienniki diagnostyczne**.
4. Aby uzyskać **Śledzenie żądań zakończonych niepowodzeniem**, wybierz opcję **na**.
5. Wybierz pozycję **Zapisz**.
6. W bloku aplikacji sieci web, wybierz **narzędzia**.
7. Wybierz **Visual Studio Online**.
8. Jeśli ustawienie nie jest **na**, wybierz opcję **na**.
9. Wybierz **Przejdź**.
10. Wybierz **Web.config**.
11. W system.webServer Dodaj tę konfigurację (w celu przechwytywania na określony adres URL):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Aby rozwiązywać problemy niska wydajność, należy dodać tę konfigurację (jeśli jest to żądanie przechwytywania trwa dłużej niż 30 sekund):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Aby pobrać śladów nieudanych żądań w [portal](https://portal.azure.com), przejdź do witryny sieci Web.
15. Wybierz **narzędzia** > **Kudu** > **Przejdź**.
18. Wybierz z menu **konsoli debugowania** > **CMD**.
19. Wybierz **LogFiles** folder, a następnie wybierz folder o nazwie, która rozpoczyna się od **W3SVC**.
20. Aby wyświetlić plik XML, wybierz ikonę ołówka.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Pojawił się komunikat "Proces roboczy zażądał odtwarzania z powodu limitu"Procent pamięci"." Jak rozwiązać ten problem?

Maksymalną dostępna ilość pamięci dla procesu 32-bitowych (nawet w 64-bitowym systemie operacyjnym) wynosi 2 GB. Domyślnie proces roboczy jest ustawiony do 32-bitowych w usłudze App Service (na potrzeby utrzymywania zgodności z aplikacji sieci web w starszej wersji).

Rozważ przełączenie do procesów 64-bitowych, dzięki czemu można wykorzystać dodatkowe dostępnej pamięci w swojej roli procesu roboczego w sieci Web. Spowoduje to wyzwolenie ponownego uruchomienia aplikacji sieci web, więc należy odpowiednio zaplanować.

Należy również zauważyć, że 64-bitowego środowiska wymaga planu usługi Basic lub Standard. Bezpłatna i współdzielona plany są zawsze uruchamiane w 32-bitowego środowiska.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji sieci web w usłudze App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Dlaczego mój limit czasu żądania 230 sekund?

Usługa Azure Load Balancer ma domyślne ustawienie limitu czasu bezczynności czterech minut. Zwykle jest to uzasadnione odpowiedzi limit czasu dla żądania sieci web. Jeśli Twoja aplikacja sieci web wymaga przetwarzania w tle, zaleca się przy użyciu usługi Azure WebJobs. Aplikacji internetowej platformy Azure można wywołać zadania Webjob i otrzymywać powiadomienia po zakończeniu przetwarzania w tle. Możesz wybrać wiele metod za pomocą zadań Webjob, w tym kolejek i wyzwalacze.

Zadania Webjob jest przeznaczony do przetwarzania w tle. Możesz tworzyć, jak najwięcej przetwarzania w tle w zadanie WebJob. Aby uzyskać więcej informacji na temat zadań Webjob, zobacz [uruchamianie zadań w tle za pomocą zadań Webjob](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplikacje platformy ASP.NET Core, które są hostowane w usłudze App Service, czasami przestać odpowiadać. Jak rozwiązać ten problem?

Znany problem z wcześniejszej [wersji Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) może spowodować aplikację ASP.NET Core 1.0, która jest hostowana w usłudze App Service sporadycznie przestanie odpowiadać. Również może zostać wyświetlony ten komunikat: "Określona aplikacja CGI napotkał błąd i serwer przerwał działanie procesu".

Ten problem został rozwiązany w wersji 1.0.2 Kestrel. Ta wersja jest uwzględniony w aktualizacji platformy ASP.NET Core 1.0.3. Aby rozwiązać ten problem, upewnij się, że aktualizacja zależności aplikacji do użycia Kestrel 1.0.2. Alternatywnie można użyć dwóch obejścia, które są opisane w wpis w blogu [powolne wydajności programu ASP.NET Core 1.0 problemy w usłudze App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nie mogę znaleźć pliki dziennika w strukturze pliku Moja aplikacja sieci web. Jak można je znaleźć?

Jeśli używasz funkcji lokalnej pamięci podręcznej App Service, wpływają na strukturze folderów, folderów LogFiles i dane dla swojego wystąpienia usługi App Service. W przypadku lokalnej pamięci podręcznej podfoldery są tworzone w magazyn LogFiles i foldery z danymi. Podfoldery Użyj nazw wzorzec "Unikatowy identyfikator" + sygnaturę czasową. Każdy podfolder odnosi się do wystąpienia maszyny Wirtualnej, w którym działa aplikacja sieci web, lub zostało uruchomione.

Aby ustalić, czy używasz lokalnej pamięci podręcznej, zapoznaj się z usługi App Service **ustawienia aplikacji** kartę. Jeśli lokalna pamięć podręczna jest używana, ustawienie aplikacji `WEBSITE_LOCAL_CACHE_OPTION` ustawiono `Always`.

Jeśli nie używasz lokalnej pamięci podręcznej i występuje ten problem, należy przesłać żądanie pomocy technicznej.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Pojawił się komunikat "próbowano uzyskać dostęp do gniazda w sposób zabroniony przez jego uprawnienia dostępu." Jak rozwiązać ten problem?

Ten błąd zazwyczaj występuje w przypadku wyczerpania połączenia wychodzące TCP na wystąpieniu maszyny Wirtualnej. W usłudze App Service limity są wymuszane dla maksymalnej liczby połączeń wychodzących, które mogą być wykonane dla każdego wystąpienia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [limity wartości liczbowych maszyny Wirtualnej między](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ponadto ten błąd może wystąpić, Jeśli spróbujesz uzyskać dostęp do lokalnego adresu z aplikacji. Aby uzyskać więcej informacji, zobacz [żądań z adresu lokalnego](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Aby uzyskać więcej informacji na temat połączeń wychodzących w aplikacji sieci web, zobacz wpis w blogu dotyczący [wychodzące połączenia w usłudze Azure websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Jak używać programu Visual Studio na zdalne debugowanie aplikacji sieci web usługi App Service?

Aby uzyskać szczegółowy przewodnik pokazujący sposób debugowania aplikacji sieci web przy użyciu programu Visual Studio, zobacz [zdalne debugowanie aplikacji sieci web usługi App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
