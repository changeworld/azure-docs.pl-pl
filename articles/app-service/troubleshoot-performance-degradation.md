---
title: Rozwiązywanie problemów z pogorszeniem wydajności
description: Dowiedz się, jak rozwiązywać problemy z powolną wydajnością aplikacji w usłudze Azure App Service, w tym monitorowanie zachowania aplikacji, zbieranie danych i łagodzenie problemu.
tags: top-support-issue
keywords: Wydajność aplikacji internetowej, powolna aplikacja, aplikacja powolna
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688314"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Rozwiązywanie problemów z powolną wydajnością aplikacji w usłudze Azure App Service
Ten artykuł ułatwia rozwiązywanie problemów z powolną wydajnością aplikacji w [usłudze Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję Uzyskaj pomoc **techniczną**.

## <a name="symptom"></a>Objaw
Podczas przeglądania aplikacji strony ładują się powoli, a czasami limit czasu.

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowany przez problemy z poziomem aplikacji, takie jak:

* żądania sieciowe zajmują dużo czasu
* Kod aplikacji lub kwerendy bazy danych są nieefektywne
* aplikacja wykorzystująca wysoką pamięć/procesor
* awaria aplikacji z powodu wyjątku

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Rozwiązywanie problemów można podzielić na trzy różne zadania, w kolejności:

1. [Obserwowanie i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Złagodzenie problemu](#mitigate)

[Usługa App Service](overview.md) oferuje różne opcje na każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Obserwować i monitorować zachowanie aplikacji
#### <a name="track-service-health"></a>Śledź kondycję usługi
Platforma Microsoft Azure nagłaśnia za każdym razem, gdy występuje przerwa w świadczeniu usług lub spadek wydajności. Kondycję usługi można śledzić w [witrynie Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Śledzenie kondycji usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorowanie aplikacji
Ta opcja umożliwia dowiedzieć się, czy aplikacja ma jakiekolwiek problemy. W bloku aplikacji kliknij kafelek **Żądania i błędy.** **Metryka** bloku pokazuje wszystkie metryki, które można dodać.

Niektóre z danych, które możesz monitorować dla aplikacji, to

* Zestaw pracy średniej pamięci
* Średni czas reakcji
* Czas procesora
* Zestaw roboczy pamięci
* Żądania

![monitorowanie wydajności aplikacji](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorowanie stanu punktu końcowego sieci Web
Jeśli używasz aplikacji w **standardowej** warstwie cenowej, usługa App Service umożliwia monitorowanie dwóch punktów końcowych z trzech lokalizacji geograficznych.

Monitorowanie punktów końcowych konfiguruje testy sieci web z lokalizacji rozproszonych geograficznie, które testują czas odpowiedzi i czas pracy adresów URL sieci Web. Test wykonuje operację HTTP GET w internetowym adresie URL, aby określić czas odpowiedzi i czas pracy bez przestojów z każdej lokalizacji. Każda skonfigurowana lokalizacja uruchamia test co pięć minut.

Czas pracy bez przestojów jest monitorowany przy użyciu kodów odpowiedzi HTTP, a czas odpowiedzi jest mierzony w milisekundach. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest większy lub równy 400 lub jeśli odpowiedź trwa dłużej niż 30 sekund. Punkt końcowy jest uważany za dostępny, jeśli jego testy monitorowania zakończyć się pomyślnie ze wszystkich określonych lokalizacji.

Aby ją skonfigurować, zobacz [Monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md).

Zobacz [także: Utrzymywanie witryn sieci Web platformy Azure w górę oraz monitorowanie punktów końcowych — ze Stefanem Schackowem,](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) aby uzyskać film wideo dotyczący monitorowania punktów końcowych.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorowanie wydajności aplikacji przy użyciu rozszerzeń
Wydajność aplikacji można również monitorować za pomocą *rozszerzenia witryny*.

Każda aplikacja usługi App Service zapewnia rozszerzalny punkt końcowy zarządzania, który umożliwia użycie zaawansowanego zestawu narzędzi wdrożonych jako rozszerzenia lokacji. Rozszerzenia obejmują: 

- Edytory kodu źródłowego, takie jak [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Narzędzia do zarządzania połączonymi zasobami, takimi jak baza danych MySQL połączona z aplikacją.

[Usługa Azure Application Insights](https://azure.microsoft.com/services/application-insights/) to rozszerzenie lokacji monitorowania wydajności, które jest również dostępne. Aby użyć usługi Application Insights, można odbudować kod za pomocą sdk. Można również zainstalować rozszerzenie, które zapewnia dostęp do dodatkowych danych. SDK umożliwia pisanie kodu, aby bardziej szczegółowo monitorować użycie i wydajność aplikacji. Aby uzyskać więcej informacji, zobacz [Monitorowanie wydajności w aplikacjach sieci Web](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
Usługa App Service udostępnia funkcje diagnostyczne do rejestrowania informacji z serwera sieci web i aplikacji sieci web. Informacje są podzielone na diagnostykę serwera sieci web i diagnostykę aplikacji.

#### <a name="enable-web-server-diagnostics"></a>Włączanie diagnostyki serwera sieci web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

* **Szczegółowe rejestrowanie błędów** — szczegółowe informacje o błędach dla kodów stanu HTTP, które wskazują błąd (kod stanu 400 lub wyższy). Może to zawierać informacje, które mogą pomóc w określeniu, dlaczego serwer zwrócił kod błędu.
* **Śledzenie żądań nie powiodło się** — szczegółowe informacje na temat nieudanych żądań, w tym śledzenia składników usług IIS używanych do przetwarzania żądania i czasu poświęconego każdemu składnikowi. Może to być przydatne, jeśli próbujesz poprawić wydajność aplikacji lub wyizolować, co jest przyczyną określonego błędu HTTP.
* **Rejestrowanie serwera sieci Web** — informacje o transakcjach HTTP przy użyciu rozszerzonego formatu pliku dziennika W3C. Jest to przydatne podczas określania ogólnych metryk aplikacji, takich jak liczba obsługiwanych żądań lub liczba żądań z określonego adresu IP.

#### <a name="enable-application-diagnostics"></a>Włącz diagnostykę aplikacji
Istnieje kilka opcji, aby zbierać dane o wydajności aplikacji z usługi App Service, profil aplikacji na żywo z programu Visual Studio lub zmodyfikować kod aplikacji, aby zarejestrować więcej informacji i śladów. Możesz wybrać opcje w zależności od tego, ile masz dostępu do aplikacji i co zaobserwowano z narzędzi do monitorowania.

##### <a name="use-application-insights-profiler"></a>Korzystanie z profilera usługi Application Insights
Można włączyć profiler usługi Application Insights, aby rozpocząć przechwytywanie szczegółowych śladów wydajności. Możesz uzyskać dostęp do śladów przechwyconych do pięciu dni temu, gdy trzeba zbadać problemy, które wystąpiły w przeszłości. Możesz wybrać tę opcję, o ile masz dostęp do zasobu usługi Application Insights aplikacji w witrynie Azure portal.

Profiler usługi Application Insights zawiera statystyki dotyczące czasu odpowiedzi dla każdego wywołania sieci web i śledzenia, który wskazuje, który wiersz kodu spowodował powolne odpowiedzi. Czasami aplikacja usługi App Service jest powolny, ponieważ niektóre kod nie jest zapisywany w sposób wydajny. Przykłady obejmują sekwencyjny kod, który może być uruchamiany równolegle i niepożądane rywalizacji blokady bazy danych. Usunięcie tych wąskich gardeł w kodzie zwiększa wydajność aplikacji, ale są one trudne do wykrycia bez konfigurowania skomplikowanych śladów i dzienników. Ślady zebrane przez profiler usługi Application Insights pomaga zidentyfikować wiersze kodu, który spowalnia aplikację i przezwyciężyć to wyzwanie dla aplikacji usługi App Service.

 Aby uzyskać więcej informacji, zobacz [Profilowanie aplikacji na żywo w usłudze Azure App Service za pomocą usługi Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Korzystanie z profilowania zdalnego
W usłudze Azure App Service aplikacje sieci Web, aplikacje interfejsu API, tylne końce urządzeń przenośnych i aplikacje WebJobs mogą być profilowane zdalnie. Wybierz tę opcję, jeśli masz dostęp do zasobu aplikacji i wiesz, jak odtworzyć problem lub jeśli znasz dokładny przedział czasu, w którym występuje problem z wydajnością.

Zdalne profilowanie jest przydatne, jeśli użycie procesora CPU w procesie jest wysokie, a proces działa wolniej niż oczekiwano lub opóźnienie żądań HTTP są wyższe niż normalnie, można zdalnie profilować proces i uzyskać stosy wywołań próbkowania procesora CPU do analizy procesu aktywności i kodować gorące ścieżki.

Aby uzyskać więcej informacji, zobacz [Pomoc techniczna w zakresie profilowania zdalnego w usłudze Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ręczne konfigurowanie śladów diagnostycznych
Jeśli masz dostęp do kodu źródłowego aplikacji sieci web, diagnostyka aplikacji umożliwia przechwytywanie informacji produkowanych przez aplikację sieci web. ASP.NET aplikacje mogą używać `System.Diagnostics.Trace` klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Jednak należy zmienić kod i ponownie wdrożyć aplikację. Ta metoda jest zalecana, jeśli aplikacja jest uruchomiona w środowisku testowym.

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania aplikacji do rejestrowania, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service.](troubleshoot-diagnostic-logs.md)

#### <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego
Usługa App Service zapewnia inteligentne i interaktywne środowisko, które ułatwia rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy napotkasz problemy z aplikacją, narzędzie diagnostyczne wskaże, co jest nie tak, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybciej rozwiązać problem.

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do aplikacji usługi App Service lub środowiska usługi app service w [witrynie Azure portal](https://portal.azure.com). W lewej nawigacji kliknij przycisk **Diagnozuj i rozwiązuj problemy**.

#### <a name="use-the-kudu-debug-console"></a>Korzystanie z konsoli debugowania Kudu
Usługa App Service jest wyposażona w konsolę debugowania, której można używać do debugowania, eksplorowania, przekazywania plików, a także punktów końcowych JSON w celu uzyskania informacji o środowisku. Ta konsola nosi nazwę *Konsoli Kudu* lub *pulpitu nawigacyjnego SCM* dla twojej aplikacji.

Dostęp do tego pulpitu nawigacyjnego można uzyskać, przechodząc do łącza **https://&lt;nazwa aplikacji>.scm.azurewebsites.net/**.

Niektóre z rzeczy, które zapewnia Kudu to:

* ustawienia środowiska dla aplikacji
* strumień dziennika
* zrzut diagnostyczny
* konsola debugowania, w której można uruchamiać polecenia cmdlet programu Powershell i podstawowe polecenia DOS.

Inną przydatną cechą Kudu jest to, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć Kudu i narzędzia SysInternals Procdump do tworzenia zrzutów pamięci. Te zrzuty pamięci są migawkami procesu i często mogą pomóc w rozwiązywaniu bardziej skomplikowanych problemów z aplikacją.

Aby uzyskać więcej informacji na temat funkcji dostępnych w programie Kudu, zobacz [narzędzia programu Azure DevOps, o których powinieneś wiedzieć.](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Złagodzenie problemu
#### <a name="scale-the-app"></a>Skalowanie aplikacji
W usłudze Azure App Service, aby zwiększyć wydajność i przepływność, można dostosować skalę, w której są uruchomione aplikacji. Skalowanie aplikacji obejmuje dwie powiązane akcje: zmiana planu usługi App Service na wyższą warstwę cenową i skonfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [Skalowanie aplikacji w usłudze Azure App Service](manage-scale-up.md).

Ponadto można uruchomić aplikację na więcej niż jedno wystąpienie. Skalowanie w poziomie nie tylko zapewnia większe możliwości przetwarzania, ale także zapewnia pewną ilość odporności na uszkodzenia. Jeśli proces ustępuje w dół na jedno wystąpienie, inne wystąpienia nadal obsługiwać żądania.

Skalowanie można ustawić tak, aby było ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Korzystanie z funkcji AutoHeal
AutoHeal odtwarza proces roboczy aplikacji na podstawie ustawień, które wybierzesz (takich jak zmiany konfiguracji, żądania, limity oparte na pamięci lub czas potrzebny do wykonania żądania). W większości przypadków recykling jest najszybszym sposobem na rozwiązanie problemu. Chociaż zawsze można ponownie uruchomić aplikację bezpośrednio w witrynie Azure portal, AutoHeal robi to automatycznie za Ciebie. Wszystko, co musisz zrobić, to dodać kilka wyzwalaczy w web.config root dla aplikacji. Te ustawienia będą działać w ten sam sposób, nawet jeśli aplikacja nie jest aplikacją .NET.

Aby uzyskać więcej informacji, zobacz [Automatyczne koryguje witryny sieci Web platformy Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uruchom ponownie aplikację
Ponowne uruchamianie jest często najprostszym sposobem odzyskania danych po jednorazowych problemach. W [witrynie Azure portal](https://portal.azure.com/)— w bloku aplikacji dostępne są opcje zatrzymania lub ponownego uruchomienia aplikacji.

 ![ponowne uruchamianie aplikacji w celu rozwiązania problemów z wydajnością](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Możesz również zarządzać aplikacją za pomocą programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).
