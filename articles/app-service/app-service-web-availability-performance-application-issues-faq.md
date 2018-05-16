---
title: Wydajność aplikacji — często zadawane pytania dotyczące aplikacji sieci web platformy Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące dostępności, wydajności i problemy z aplikacji w funkcji aplikacji sieci Web w usłudze Azure App Service.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c8eda1c4a65465a93b9c3f9caf23a6663073f26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Wydajność aplikacji — często zadawane pytania dotyczące aplikacji sieci Web na platformie Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczące problemów z wydajnością aplikacji dla [funkcja Web Apps usługi Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Dlaczego jest Moja aplikacja wolne?

Wiele czynników może przyczynić się do powolne działanie aplikacji. Aby uzyskać szczegółowe kroki rozwiązywania problemów, zobacz [wydajność aplikacji sieci web powolne rozwiązywanie](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak rozwiązywać problemy z scenariusza wysokie użycie procesora CPU

W niektórych scenariuszach wysokie użycie procesora CPU aplikacji naprawdę może wymagać więcej zasobów obliczeniowych. W takim przypadku należy wziąć pod uwagę skalowanie do wyższego poziomu usługi, więc aplikacja pobiera wszystkie zasoby, które są niezbędne. Innych przypadkach może być spowodowany wysokie użycie procesora CPU, zły pętli lub rozwiązaniem w zakresie kodowania. Uzyskiwanie wglądu w co jest przyczyną zwiększone użycie procesora CPU jest procesem dwóch części. Najpierw należy utworzyć zrzutu procesu, a następnie analizować zrzutu procesu. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie plik zrzutu wysokie użycie procesora CPU dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak rozwiązywać scenariusza wysokie zużycie pamięci?

W niektórych scenariuszach wysokie zużycie pamięci aplikacji naprawdę może wymagać więcej zasobów obliczeniowych. W takim przypadku należy wziąć pod uwagę skalowanie do wyższego poziomu usługi, więc aplikacja pobiera wszystkie zasoby, które są niezbędne. Innym razem usterki w kodzie może spowodować przeciek pamięci. Rozwiązaniem w zakresie kodowania także może zwiększyć wykorzystanie pamięci. Uzyskiwanie wglądu w co jest przyczyną pamięci wysokiej zużycie to proces składający się z dwóch części. Najpierw należy utworzyć zrzutu procesu, a następnie analizować zrzutu procesu. Diagnostyki awarii z galerii rozszerzeń witryny Azure można efektywnie obsługiwać obu tych czynności. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie pliku zrzutu pamięci wysokiej sporadyczne dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak zautomatyzować aplikacje sieci web usługi aplikacji przy użyciu programu PowerShell?

Można użyć poleceń cmdlet programu PowerShell do zarządzania i obsługa aplikacji sieci web usługi aplikacji. W naszym blogu [zautomatyzować aplikacje sieci web hostowanych w usłudze Azure App Service przy użyciu programu PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), opisano sposób użycia poleceń cmdlet usługi Azure Resource Manager na podstawie programu PowerShell do automatyzacji typowych zadań. Wpis w blogu ma również przykładowy kod dla różnych zadań zarządzania aplikacji sieci web. Opisy i składnia dla wszystkich poleceń cmdlet aplikacji sieci web usługi aplikacji, zobacz [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak wyświetlać dzienniki zdarzeń aplikacji sieci web?

Aby wyświetlić dzienniki zdarzeń aplikacji sieci web:

1. Zaloguj się do Twojego [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz z menu **konsoli debugowania** > **CMD**.
3. Wybierz **LogFiles** folderu.
4. Aby wyświetlić dzienniki zdarzeń, wybierz ikonę ołówka obok **eventlog.xml**.
5. Aby pobrać dzienniki, uruchom polecenie cmdlet programu PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak przechwytywania zrzutu pamięci trybu użytkownika mojej aplikacji sieci web?

Do przechwytywania zrzutu pamięci trybu użytkownika aplikacji sieci web:

1. Zaloguj się do Twojego [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz **Eksploratora procesów** menu.
3. Kliknij prawym przyciskiem myszy **w3wp.exe** procesu lub proces zadania WebJob.
4. Wybierz **Pobierz zrzut pamięci** > **pełne zrzutu**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak wyświetlić informacje o poziomie procesu dla mojej aplikacji sieci web?

Masz dwie opcje wyświetlania informacji o poziomie procesu dla aplikacji sieci web:

*   W witrynie Azure Portal:
    1. Otwórz **Eksploratora procesów** dla aplikacji sieci web.
    2. Aby wyświetlić szczegóły, wybierz **w3wp.exe** procesu.
*   W konsoli Kudu:
    1. Zaloguj się do Twojego [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Wybierz **Eksploratora procesów** menu.
    3. Aby uzyskać **w3wp.exe** procesu, wybierz opcję **właściwości**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Wskaż Moja aplikacja widzę "Błąd 403 - ta aplikacja sieci web została zatrzymana." Jak rozwiązać ten problem?

Trzy warunki mogą być przyczyną tego błędu:

* Aplikacja sieci web został osiągnięty limit rozliczeń i witryna została wyłączona.
* Aplikacja sieci web została zatrzymana w portalu.
* Aplikacja sieci web osiągnął limity przydziału zasobów mogą być stosowane do wolnego lub udostępnionego plan skalowania usługi.

Aby sprawdzić, co jest przyczyną błędu i rozwiązać ten problem, wykonaj kroki opisane w [aplikacji sieci Web: "Błąd 403 — ta aplikacja sieci web została zatrzymana"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Gdzie mogą dowiedzieć się więcej na temat przydziały i limity dla różnych planów usługi aplikacji?

Aby uzyskać informacje o przydziały i limity, zobacz [ogranicza usługi aplikacji](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak skrócić czas odpowiedzi na pierwsze żądanie po upływie czasu bezczynności?

Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynności na wybrany okres czasu. Dzięki temu system zachować zasoby. Wadą interfejsu jest dłużej, aby umożliwić aplikacji sieci web do ładowania i uruchomić jednoczesnej obsłudze odpowiedzi odpowiedzi na pierwsze żądanie po aplikacji sieci web zostanie zwolniona. W planie Basic i Standard usługi, można włączyć **zawsze na** ustawienie, aby zachować aplikacji zawsze ładowany. Eliminuje to dłuższy czas ładowania po aplikacji jest w stanie bezczynności. Aby zmienić **zawsze na** ustawienia:

1. W portalu Azure przejdź do aplikacji sieci web.
2. Wybierz **ustawienia aplikacji**.
3. Aby uzyskać **zawsze na**, wybierz pozycję **na**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak włączyć śledzenie niepomyślnych żądań

Aby włączyć śledzenie niepomyślnych żądań:

1. W portalu Azure przejdź do aplikacji sieci web.
3. Wybierz **wszystkie ustawienia** > **dzienników diagnostycznych**.
4. Aby uzyskać **śledzenia nieudanych żądań**, wybierz pozycję **na**.
5. Wybierz pozycję **Zapisz**.
6. W bloku aplikacja sieci web, wybierz **narzędzia**.
7. Wybierz **Visual Studio Online**.
8. Jeśli ustawienie jest **na**, wybierz pozycję **na**.
9. Wybierz **Przejdź**.
10. Wybierz **Web.config**.
11. W system.webServer Dodaj tę konfigurację (do przechwytywania na określony adres URL):

    ```
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
12. Powolna — Rozwiązywanie problemów z wydajnością, Dodaj tę konfigurację (jeśli przechwytywania żądania trwa dłużej niż 30 sekund):
    ```
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
13. Aby pobrać dane śledzenia nieudanych żądań, w [portal](https://portal.azure.com), przejdź do witryny sieci Web.
15. Wybierz **narzędzia** > **Kudu** > **Przejdź**.
18. Wybierz z menu **konsoli debugowania** > **CMD**.
19. Wybierz **LogFiles** folder, a następnie wybierz folder o nazwie, która rozpoczyna się od **W3SVC**.
20. Aby wyświetlić plik XML, wybierz ikonę ołówka.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Pojawił się komunikat "Proces roboczy zażądał odtworzenia z powodu limitu"Procent pamięci"." Jak rozwiązać ten problem?

Maksymalna dostępna ilość pamięci dla procesu 32-bitowego (nawet na 64-bitowym systemie operacyjnym) wynosi 2 GB. Domyślnie proces roboczy jest ustawiony do 32-bitowe w usłudze App Service (dla zachowania zgodności z aplikacjami sieci web w starszej wersji).

Należy rozważyć przełączanie do procesów 64-bitowych, dzięki czemu można korzystać dodatkowej pamięci w swojej roli procesu roboczego sieci Web. Powoduje ponowne uruchomienie aplikacji sieci web, więc planowanie odpowiednio.

Należy również zauważyć, że 64-bitowego środowiska wymaga usługi planu Basic lub Standard. Zwolnij i planów udostępnione są zawsze uruchamiane w środowisku 32-bitowym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji sieci web w usłudze App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>Dlaczego moja limit czasu żądania 240 sekund?

Moduł równoważenia obciążenia Azure ma domyślne ustawienie limitu czasu bezczynności czterech minut. Zwykle jest to termin uzasadnione odpowiedzi na żądania sieci web. Jeśli aplikacja sieci web wymaga przetwarzania w tle, zalecamy użycie zadań Webjob Azure. Aplikacja sieci web platformy Azure można wywołać zadania Webjob i powiadomienie po zakończeniu przetwarzania w tle. Można wybrać z wielu metod umożliwiających używanie zadań Webjob, łącznie z kolejki i wyzwalaczy.

Zadania Webjob jest przeznaczony do przetwarzania w tle. Możesz to zrobić tyle przetwarzania w tle w zadanie WebJob. Aby uzyskać więcej informacji na temat zadań Webjob, zobacz [wykonywać zadania w tle z zadań Webjob](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplikacji platformy ASP.NET Core, które są obsługiwane w usłudze App Service czasami przestać odpowiadać. Jak rozwiązać ten problem?

Znany problem dotyczący wcześniejszego [wersji Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) może spowodować aplikacji platformy ASP.NET Core 1.0, która znajduje się w usłudze App Service sporadycznie przestać odpowiadać. Można również zobaczyć ten komunikat: "podanej aplikacji CGI wystąpił błąd i serwer przedwcześnie zakończył proces."

Ten problem został rozwiązany w Kestrel wersji 1.0.2. Ta wersja nie jest dołączony do platformy ASP.NET Core 1.0.3 aktualizacji. Aby rozwiązać ten problem, upewnij się, że aktualizacja zależności aplikacji do użycia Kestrel 1.0.2. Alternatywnie można użyć jednej z dwóch rozwiązania, które zostały opisane w blogu [powolne wydajności platformy ASP.NET Core 1.0 problemy w aplikacji sieci web usługi aplikacji](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nie można znaleźć pliki dziennika w strukturze plików Moja aplikacja sieci web. Jak można je znaleźć?

Jeśli korzystasz z lokalnej pamięci podręcznej funkcji usługi App Service, problem dotyczy struktury folderów LogFiles i danych folderów dla swojego wystąpienia usługi aplikacji. W przypadku lokalnej pamięci podręcznej podfoldery są tworzone w magazynu LogFiles i foldery z danymi. Podfoldery Użyj nazewnictwa wzorzec "Unikatowy identyfikator" + sygnatury czasowej. Każdy podfolder odpowiada wystąpienia maszyny Wirtualnej, w którym aplikacja sieci web jest uruchomiona lub jest uruchomione.

Aby ustalić, czy używasz lokalnej pamięci podręcznej, sprawdź aplikację usługi **ustawienia aplikacji** kartę. Jeśli w lokalnej pamięci podręcznej jest używany, aplikacji, ustawianie `WEBSITE_LOCAL_CACHE_OPTION` ma ustawioną wartość `Always`.

Jeśli nie korzystają z lokalnej pamięci podręcznej i występuje ten problem, należy przesłać żądanie obsługi.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Pojawił się komunikat "nastąpiła próba uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu." Jak rozwiązać ten problem?

Ten błąd zazwyczaj występuje podczas wyczerpania wychodzących połączeń TCP w wystąpieniu maszyny Wirtualnej. W usłudze App Service limity są wymuszane maksymalnej liczby połączeń wychodzących, które można podjąć dla każdego wystąpienia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [limity wartości liczbowych maszyny Wirtualnej między](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ten błąd może również wystąpić przy próbie dostępu do adresu lokalnego w aplikacji. Aby uzyskać więcej informacji, zobacz [żądań lokalny adres](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Aby uzyskać więcej informacji na temat połączenia wychodzące w aplikacji sieci web, zobacz blogu o [wychodzące połączenia z witryn sieci Web Azure](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Jak użyć programu Visual Studio do zdalnego debugowania Moja aplikacja sieci web usługi aplikacji?

Aby uzyskać szczegółowy przewodnik pokazujący sposób debugowanie aplikacji sieci web za pomocą programu Visual Studio, zobacz [zdalne debugowanie aplikacji sieci web usługi aplikacji](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
