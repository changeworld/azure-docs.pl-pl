---
title: Często zadawane pytania dotyczące wydajności aplikacji
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące problemów z dostępnością, wydajnością i aplikacjami w usłudze Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259866"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na platformie Azure

> [!NOTE]
> Niektóre z poniższych wytycznych mogą działać tylko w usługach Windows lub Linux App Services. Na przykład usługi Linux App Services są domyślnie uruchamiane w trybie 64-bitowym.
>

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z wydajnością aplikacji dla [funkcji aplikacji w sieci Web usługi Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Dlaczego moja aplikacja jest powolna?

Wiele czynników może przyczynić się do powolnej wydajności aplikacji. Aby uzyskać szczegółowe kroki rozwiązywania problemów, zobacz [Rozwiązywanie problemów z powolną wydajnością aplikacji sieci Web](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak rozwiązać problem ze scenariuszem wysokiego zużycia procesora?

W niektórych scenariuszach wysokiego zużycia procesora CPU aplikacja może naprawdę wymagać więcej zasobów obliczeniowych.W takim przypadku należy rozważyć skalowanie do wyższej warstwy usług, dzięki czemu aplikacja pobiera wszystkie zasoby, których potrzebuje. Innym razem wysokie zużycie procesora CPU może być spowodowane przez złą pętlę lub praktykę kodowania. Uzyskiwanie wglądu w to, co powoduje zwiększone zużycie procesora CPU jest procesem dwuczęściowym. Najpierw utwórz zrzut procesu, a następnie przeanalizuj zrzut procesu. Aby uzyskać więcej informacji, zobacz [Przechwytywanie i analizowanie pliku zrzutu pod kątem wysokiego zużycia procesora CPU dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak rozwiązać problem ze scenariuszem wysokiego zużycia pamięci?

W niektórych scenariuszach wysokiego zużycia pamięci aplikacja może naprawdę wymagać więcej zasobów obliczeniowych.W takim przypadku należy rozważyć skalowanie do wyższej warstwy usług, dzięki czemu aplikacja pobiera wszystkie zasoby, których potrzebuje. Innym razem błąd w kodzie może spowodować przeciek pamięci. Praktyka kodowania również może zwiększyć zużycie pamięci.Uzyskiwanie wglądu w to, co powoduje wysokie zużycie pamięci jest procesem dwuczęściowym. Najpierw utwórz zrzut procesu, a następnie przeanalizuj zrzut procesu. Diagnostyka awarii z Galerii rozszerzenia witryny azure można wydajnie wykonać oba te kroki. Aby uzyskać więcej informacji, zobacz [Przechwytywanie i analizowanie pliku zrzutu pod kątem przerywanej wysokiej pamięci dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak zautomatyzować aplikacje internetowe usługi App Service przy użyciu programu PowerShell?

Polecenia cmdlet programu PowerShell umożliwiają zarządzanie aplikacjami sieci Web usługi App Service i ich obsługę. W naszym wpisie w blogu [Automatyzuj aplikacje internetowe hostowane w usłudze Azure App Service przy użyciu programu PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)opisano, jak zautomatyzować wykonywanie poleceń cmdlet programu PowerShell opartych na usłudze Azure Resource Manager. Wpis w blogu zawiera również przykładowy kod dla różnych zadań zarządzania aplikacjami sieci Web. Aby uzyskać opisy i składnię wszystkich poleceń cmdlet aplikacji sieci Web usługi App Service, zobacz [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak wyświetlić dzienniki zdarzeń aplikacji internetowej?

Aby wyświetlić dzienniki zdarzeń aplikacji sieci Web:

1. Zaloguj się na swojej [stronie internetowej Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. W menu wybierz polecenie **Dydug konsoli** > **CMD**.
3. Wybierz folder **LogFiles.**
4. Aby wyświetlić dzienniki zdarzeń, wybierz ikonę ołówka obok **pliku eventlog.xml**.
5. Aby pobrać dzienniki, uruchom polecenie cmdlet programu PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak przechwycić zrzut pamięci w trybie użytkownika mojej aplikacji internetowej?

Aby przechwycić zrzut pamięci w trybie użytkownika aplikacji sieci web:

1. Zaloguj się na swojej [stronie internetowej Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz menu **Eksploratora procesów.**
3. Kliknij prawym przyciskiem myszy proces **w3wp.exe** lub proces WebJob.
4. Wybierz **opcję Pobierz zrzut** > **pamięci Pełny zrzut**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak wyświetlić informacje na poziomie procesu dla mojej aplikacji internetowej?

Dostępne są dwie opcje wyświetlania informacji na poziomie procesu dla aplikacji sieci web:

*   W witrynie Azure Portal:
    1. Otwórz **Eksploratora procesów** dla aplikacji sieci web.
    2. Aby wyświetlić szczegóły, wybierz proces **w3wp.exe.**
*   W konsoli Kudu:
    1. Zaloguj się na swojej [stronie internetowej Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Wybierz menu **Eksploratora procesów.**
    3. W przypadku procesu **w3wp.exe** wybierz opcję **Właściwości**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Gdy przeglądam aplikację, widzę "Błąd 403 - Ta aplikacja internetowa jest zatrzymana." Jak rozwiązać ten problem?

Może to spowodować trzy warunki:

* Aplikacja internetowa osiągnęła limit rozliczeń, a Twoja witryna została wyłączona.
* Aplikacja internetowa została zatrzymana w portalu.
* Aplikacja sieci web osiągnęła limit przydziału zasobów, który może mieć zastosowanie do planu usługi skalowania bezpłatnego lub udostępnionego.

Aby zobaczyć, co jest przyczyną błędu i rozwiązać problem, wykonaj kroki opisane w [aplikacji sieci Web: "Błąd 403 — ta aplikacja internetowa jest zatrzymana"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Gdzie mogę dowiedzieć się więcej o przydziałach i limitach dla różnych planów usługi App Service?

Aby uzyskać informacje o przydziałach i limitach, zobacz [Limity usługi app service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak skrócić czas odpowiedzi dla pierwszego żądania po czasie bezczynnym?

Domyślnie aplikacje sieci web są zwalniane, jeśli są bezczynne przez określony czas. W ten sposób system może oszczędzać zasoby. Wadą jest to, że odpowiedź na pierwsze żądanie po zwalnianiu aplikacji sieci web jest dłuższy, aby umożliwić aplikacji sieci web, aby załadować i rozpocząć wyświetlanie odpowiedzi. W planach usług podstawowych i standardowych można włączyć ustawienie **Zawsze włączone,** aby aplikacja była zawsze ładowana. Eliminuje to dłuższy czas ładowania po bezczynności aplikacji. Aby zmienić ustawienie **Zawsze włączone:**

1. W witrynie Azure portal przejdź do aplikacji sieci web.
2. Wybierz **konfigurację**
3. Wybierz **pozycję Ustawienia ogólne**.
4. W obszarze **Zawsze włączone**wybierz pozycję **Wł.**

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak włączyć śledzenie żądań nie powiodło się?

Aby włączyć śledzenie żądań nie powiodło się:

1. W witrynie Azure portal przejdź do aplikacji sieci web.
3. Wybierz wszystkie**dzienniki diagnostyki** **ustawień** > .
4. W przypadku **śledzenia żądań nie powiodło się**wybierz pozycję **On**.
5. Wybierz **pozycję Zapisz**.
6. W bloku aplikacji sieci Web wybierz pozycję **Narzędzia**.
7. Wybierz **pozycję Visual Studio Online**.
8. Jeśli ustawienie nie jest **włączone,** wybierz opcję **Włączone**.
9. Wybierz **pozycję Przejdź**.
10. Wybierz **web.config**.
11. W pliku system.webServer dodaj tę konfigurację (aby przechwycić określony adres URL):

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
12. Aby rozwiązać problemy z powolną wydajnością, dodaj tę konfigurację (jeśli żądanie przechwytywania trwa dłużej niż 30 sekund):
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
13. Aby pobrać ślady żądań, które nie powiodły się, przejdź do [witryny](https://portal.azure.com)sieci Web.
15. Wybierz **narzędzia** > **Kudu** > **Go**.
18. W menu wybierz polecenie **Dydug konsoli** > **CMD**.
19. Zaznacz folder **LogFiles,** a następnie wybierz folder o nazwie, która zaczyna się od **W3SVC**.
20. Aby wyświetlić plik XML, wybierz ikonę ołówka.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Widzę komunikat "Proces roboczy zażądał recyklingu z powodu limitu "Procent pamięci". Jak rozwiązać ten problem?

Maksymalna dostępna ilość pamięci dla procesu 32-bitowego (nawet w 64-bitowym systemie operacyjnym) wynosi 2 GB. Domyślnie proces roboczy jest ustawiony na 32-bitowy w usłudze App Service (w celu zapewnienia zgodności ze starszymi aplikacjami sieci web).

Należy rozważyć przełączenie do procesów 64-bitowych, aby można było korzystać z dodatkowej pamięci dostępnej w roli składnika Web Worker. Spowoduje to uruchomienie aplikacji sieci web, więc odpowiednio zaplanować.

Należy również zauważyć, że środowisko 64-bitowe wymaga planu usługi Basic lub Standard. Plany bezpłatne i udostępnione zawsze są uruchamiane w środowisku 32-bitowym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji sieci Web w usłudze App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Dlaczego mój limit czasu żądania po 230 sekundach?

Moduł równoważenia obciążenia platformy Azure ma domyślne ustawienie limitu czasu bezczynności na cztery minuty. Jest to zazwyczaj rozsądny termin odpowiedzi na żądanie internetowe. Jeśli aplikacja sieci Web wymaga przetwarzania w tle, zalecamy korzystanie z usługi Azure WebJobs. Aplikacja sieci Web platformy Azure może wywoływać webjobs i otrzymywać powiadomienia po zakończeniu przetwarzania w tle. Można wybrać jedną z wielu metod korzystania z funkcji WebJobs, w tym kolejek i wyzwalaczy.

WebJobs jest przeznaczony do przetwarzania w tle. W umiejce webjob można wykonać tyle przetwarzania w tle, ile chcesz. Aby uzyskać więcej informacji na temat aplikacji WebJobs, zobacz [Uruchamianie zadań w tle za pomocą aplikacji WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET podstawowe aplikacje, które są hostowane w usłudze App Service, czasami przestają odpowiadać. Jak rozwiązać ten problem?

Znany problem z wcześniejszą [wersją Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) może spowodować, że aplikacja ASP.NET Core 1.0, która jest hostowana w usłudze App Service, aby sporadycznie przestać odpowiadać. Może również pojawić się ten komunikat: "Określona aplikacja CGI napotkała błąd, a serwer zakończył proces."

Ten problem został rozwiązany w kestrel wersji 1.0.2. Ta wersja jest zawarta w aktualizacji ASP.NET Core 1.0.3. Aby rozwiązać ten problem, upewnij się, że zaktualizowałeś zależności aplikacji, aby używać Kestrel 1.0.2. Alternatywnie można użyć jednego z dwóch obejść opisanych w poście w blogu [ASP.NET problemy z powolnym perfem Core 1.0 w aplikacjach sieci Web usługi App Service.](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nie mogę znaleźć plików dziennika w strukturze plików mojej aplikacji internetowej. Jak je znaleźć?

Jeśli używasz funkcji Lokalnej pamięci podręcznej usługi App Service, dotyczy to struktury folderów LogFiles i Data dla wystąpienia usługi App Service. Gdy używana jest lokalna pamięć podręczna, podfoldery są tworzone w folderach LogFiles i Data magazynu. Podfoldery używają wzorca nazewnictwa "unikatowy identyfikator" + sygnatura czasowa. Każdy podfolder odpowiada wystąpieniu maszyny Wirtualnej, w którym aplikacja sieci web jest uruchomiona lub została uruchomiona.

Aby ustalić, czy używasz lokalnej pamięci podręcznej, sprawdź kartę **ustawienia aplikacji** usługi App Service. Jeśli używana jest lokalna pamięć `WEBSITE_LOCAL_CACHE_OPTION` podręczna, ustawienie aplikacji jest ustawione na `Always`.

Jeśli nie używasz lokalnej pamięci podręcznej i występuje ten problem, prześlij żądanie pomocy technicznej.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Widzę komunikat "Podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu." Jak rozwiązać ten problem?

Ten błąd zazwyczaj występuje, jeśli wychodzące połączenia TCP w wystąpieniu maszyny Wirtualnej są wyczerpane. W usłudze app service limity są wymuszane dla maksymalnej liczby połączeń wychodzących, które mogą być wykonane dla każdego wystąpienia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Limity liczbowe między wirtualnymi maszynami wirtualnymi](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ten błąd może również wystąpić, jeśli spróbujesz uzyskać dostęp do adresu lokalnego z aplikacji. Aby uzyskać więcej informacji, zobacz [Żądania adresów lokalnych](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Aby uzyskać więcej informacji na temat połączeń wychodzących w aplikacji sieci web, zobacz wpis w blogu dotyczący [połączeń wychodzących z witrynami sieci Web platformy Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Jak zdalnie debugować aplikację sieci Web usługi App Service za pomocą programu Visual Studio?

Aby uzyskać szczegółowy przewodnik, który pokazuje, jak debugować aplikację sieci web przy użyciu programu Visual Studio, zobacz [Zdalne debugowanie aplikacji sieci Web usługi App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
