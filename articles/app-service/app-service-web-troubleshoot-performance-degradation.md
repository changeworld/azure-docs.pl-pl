---
title: Powolne działanie aplikacji sieci web w usłudze App Service | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga powolne web app Rozwiązywanie problemów z wydajnością w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: wydajność aplikacji sieci Web, powolne aplikacji app powolne
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 6b71aa004095a94bea84623fd2b5dbdfc1f81af0
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29461846"
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Powolne web app Rozwiązywanie problemów z wydajnością w usłudze Azure App Service
W tym artykule pomocy w rozwiązywaniu problemów z wydajnością aplikacji sieci web wolne w [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz polecenie **Get Support**.

## <a name="symptom"></a>Objaw
Podczas przeglądania aplikacji sieci web, obciążenia stron powoli i czasami limitu czasu.

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowane przez problemy z poziomu aplikacji, takich jak:

* długi czas trwania żądania sieciowe
* zapytania kod lub bazy danych aplikacji jest nieefektywne
* aplikacji przy użyciu pamięci wysokiej/procesora CPU
* Aplikacja awarii z powodu wyjątku

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Rozwiązywanie problemów, można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Sprawdź i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Ograniczenia problem](#mitigate)

[Aplikacje sieci Web usługi aplikacji](/services/app-service/web/) zapewnia różne opcje w każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sprawdź i monitorowanie zachowania aplikacji
#### <a name="track-service-health"></a>Śledź usługę kondycji
Microsoft Azure publicizes każdym razem, gdy istnieje degradacji przerw i wydajności usługi. Kondycja usługi można śledzić na [portalu Azure](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzić kondycja usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorowanie aplikacji sieci web
Ta opcja pozwala dowiedzieć się, jeśli masz problemy aplikacji. W bloku aplikacja sieci web, kliknij przycisk **żądań i błędów** kafelka. **Metryka** bloku pokazuje wszystkie metryki można dodać.

Niektóre metryki, które mogą zostać do monitorowania aplikacji sieci web

* Średni zestaw roboczy pamięci
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![Monitorowanie wydajności aplikacji sieci web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji sieci Web w usłudze aplikacji Azure](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitoruje stan punktu końcowego sieci web
Jeśli używasz aplikacji sieci web **standardowe** warstwy cenowej, aplikacje sieci Web umożliwia monitorowanie dwa punkty końcowe z trzech lokalizacji geograficznej.

Monitorowania punktów końcowych konfiguruje testów sieci web z rozproszona geograficznie lokalizacje testujące czas reakcji i przestoje adresów URL sieci web. Test wykonuje operację GET protokołu HTTP, adres URL sieci web, aby określić czas reakcji i przestoje w każdej lokalizacji. Każdej skonfigurowanej lokalizacji uruchamia test co pięć minut.

Czas działania jest monitorowana przy użyciu kodów odpowiedzi HTTP, a czas odpowiedzi jest mierzony w milisekundach. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest większa niż lub równa 400 lub odpowiedzi trwa dłużej niż 30 sekund. Punkt końcowy jest traktowany jako dostępne w przypadku powodzenia testów monitorowania w określonych lokalizacjach.

Aby go skonfigurować, zobacz [monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md).

Zobacz też [utrzymywanie Azure Web Sites zapasową oraz monitorowania punktów końcowych — z Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) Film przedstawiający monitorowania punktów końcowych.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorowanie wydajności aplikacji przy użyciu rozszerzenia
Można również monitorować wydajność aplikacji za pomocą *lokacji rozszerzenia*.

Każdej aplikacji sieci web usługi App Service udostępnia punkt końcowy rozszerzonego zarządzania, który pozwala na stosowanie zaawansowany zestaw narzędzi wdrożony jako rozszerzenia lokacji. Rozszerzenia obejmują: 

- Edytory kodu źródłowego, takich jak [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Narzędzia do zarządzania dla połączonych zasobów, takich jak bazy danych MySQL podłączone do aplikacji sieci web.

[Azure Application Insights](/services/application-insights/) jest rozszerzeniem lokacji, które jest również dostępna do monitorowania wydajności. Aby korzystać z usługi Application Insights, należy odtworzyć kodu za pomocą zestawu SDK. Można także zainstalować rozszerzenia, która zapewnia dostęp do dodatkowych danych. Zestaw SDK umożliwia napisz kod umożliwiający monitorowanie użycia i wydajności aplikacji bardziej szczegółowo. Aby uzyskać więcej informacji, zobacz [monitorować wydajność w aplikacji sieci web](../application-insights/app-insights-web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
Środowisko aplikacji sieci Web udostępnia funkcję diagnostyki dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Informacje są podzielone na diagnostyki serwera sieci web i diagnostyki aplikacji.

#### <a name="enable-web-server-diagnostics"></a>Włącz diagnostykę serwera sieci web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

* **Szczegółowe rejestrowanie błędów** — szczegółowe informacje o błędzie kodów stanu HTTP, które wskazania błędu (kod stanu 400 lub nowszej). Może zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu.
* **Nie powiodło się żądanie śledzenia** — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śladu używane do przetwarzania żądania oraz godzinę w poszczególnych składników składników usług IIS. Może to być przydatne, jeśli chcesz zwiększyć wydajność aplikacji sieci web lub określić, co powoduje określonego błędu HTTP.
* **Rejestrowanie serwera w sieci Web** — informacje o transakcjach HTTP przy użyciu rozszerzonym formacie W3C dziennika pliku. Jest to przydatne podczas określania ogólnego metryki aplikacji sieci web, takich jak liczba żądań obsłużonych lub liczbę żądań pochodzą z określonego adresu IP.

#### <a name="enable-application-diagnostics"></a>Włącz diagnostykę aplikacji
Dostępnych jest kilka opcji do zbierania danych dotyczących wydajności aplikacji z aplikacji sieci Web, profil aplikacji na żywo w programie Visual Studio lub zmodyfikować kod aplikacji do logowania się dodatkowe informacje i dane śledzenia. Można wybrać opcje oparte na narzędzi ile dostępu do aplikacji i obserwowanych z monitorowania.

##### <a name="use-application-insights-profiler"></a>Użyj Application Insights profilera
Można włączyć aplikacji profilera Insights można uruchomić przechwytywanie śladów szczegółowe wydajności. Można uzyskać dostęp do przechwycone dane śledzenia pięć dni temu potrzebne do badania problemów przypada w przeszłości. Tę opcję można tak długo, jak długo mają dostęp do zasobu usługi Application Insights aplikacji sieci web w portalu Azure.

Application Insights Profiler zawiera dane statystyczne dotyczące czas odpowiedzi dla każdego wywołania sieci web i śladów, wskazującą, w którym wierszem kodu spowodował powolne odpowiedzi. Czasami aplikację usługi aplikacji działa wolno, ponieważ niektóre kodu nie jest zapisywany w wydajności sposób. Przykładami sekwencyjnych kod, który można uruchomić w rywalizacji blokad równoległych i niepożądane bazy danych. Usunięcie tych wąskich gardeł w kodzie zwiększa wydajność aplikacji, ale są trudne do wykrycia bez konfigurowania dzienników i rozbudowane dane śledzenia. Ślady zebrane przez Profiler Insights aplikacji pomaga w identyfikacji wierszy kodu, który spowalnia aplikacji i rozwiązać ten problem w przypadku aplikacji usługi aplikacji.

 Aby uzyskać więcej informacji, zobacz [profilowania aplikacji sieci web platformy Azure na żywo za pomocą usługi Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Użyj profilowanie zdalne
W usłudze Azure App Service aplikacje sieci Web, aplikacje interfejsu API i zadania Webjob może być zdalnie profilowane. Wybierz tę opcję, jeśli masz dostęp do zasobów aplikacji sieci web i wiesz, jak do odtworzenia problemu lub jeśli znasz dokładnego interwał sytuacji problem z wydajnością.

Profilowanie zdalne jest przydatne, jeśli jest wysokie użycie procesora CPU przez proces i proces działa wolniej niż oczekiwano lub opóźnienia żądania HTTP są większe niż jest to normalne, można zdalnie profilu procesu i uzyskać stosy wywołań próbkowania procesora CPU do analizowania procesu ścieżki działania i kod dostępu.

Aby uzyskać więcej informacji, zobacz [profilowanie zdalne pomocy technicznej w usłudze Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ręcznie skonfigurować dane śledzenia diagnostycznego
Jeśli masz dostęp do kodu źródłowego aplikacji sieci web diagnostyki aplikacji umożliwia przechwytywanie informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać `System.Diagnostics.Trace` klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Jednak należy zmienić kod i wdrożenie aplikacji. Ta metoda jest zalecane, jeśli aplikacja działa w środowisku testowym.

Aby uzyskać szczegółowe instrukcje na temat konfigurowania aplikacji w celu rejestrowania, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci web w usłudze Azure App Service](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Użyj portalu pomocy technicznej usługi Azure App Service
Aplikacje sieci Web umożliwia rozwiązanie problemów dotyczących aplikacji sieci web, analizując HTTP dzienniki, dzienniki zdarzeń, zrzuty procesu i inne. Można uzyskać dostępu do tych informacji za pomocą portalu pomocy technicznej w **http://&lt;Twojego Nazwa aplikacji >.scm.azurewebsites.net/Support**

Portalu pomocy technicznej usługi Azure App Service udostępnia trzy oddzielne karty do obsługi trzy kroki typowego scenariusza rozwiązywania problemów:

1. Sprawdź bieżące zachowanie
2. Analizowanie zbierania informacji diagnostycznych i uruchamiając analizatorów wbudowane
3. Ograniczenie

Problem jest wykonywane od razu, kliknij przycisk **Analizuj** > **diagnostyki** > **diagnozowanie teraz** utworzenie sesji diagnostycznej, do którego Służy do zbierania dzienników HTTP, dzienniki Podglądu zdarzeń zrzuty pamięci, dzienniki błędów PHP i raportu procesu PHP.

Gdy dane są zbierane, w portalu pomocy technicznej uruchamia analizę danych i udostępnia raport HTML.

W przypadku, gdy chcesz pobrać dane, domyślnie, powinny być przechowywane w folderze D:\home\data\DaaS.

Aby uzyskać więcej informacji w portalu pomocy technicznej usługi Azure App Service, zobacz [nowe aktualizacje do obsługi rozszerzenia lokacji dla witryn sieci Web Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Użyj konsoli debugowania aparatu Kudu
Aplikacje sieci Web jest dostarczany z konsoli debugowania, która służy do debugowania, eksploracji, przekazywania plików, a także pobieranie informacji na temat środowiska punktów końcowych JSON. Ta konsola jest nazywany *konsoli Kudu* lub *pulpitu nawigacyjnego SCM* dla aplikacji sieci web.

Dostęp do tego pulpitu nawigacyjnego, przechodząc do łącza **https://&lt;Twojego Nazwa aplikacji >.scm.azurewebsites.net/**.

Niektóre czynności, które program Kudu udostępnia są:

* ustawienia środowiska aplikacji
* strumień dziennika
* diagnostycznych zrzutu
* Konsola, w którym można uruchomić poleceń cmdlet programu Powershell i podstawowe polecenia systemu DOS debugowania.

Inny przydatną cechą Kudu jest, że w przypadku, gdy aplikacja jest zgłaszanie wyjątków pierwszej szansy, można użyć Kudu i zrzuty narzędzie SysInternals Procdump utworzyć pamięci. Te zrzuty pamięci są migawki procesu i często ułatwiają rozwiązywanie problemów z bardziej skomplikowanych problemów z aplikacją sieci web.

Aby uzyskać więcej informacji o funkcjach dostępnych w Kudu, zobacz [narzędzia Azure witryn sieci Web Team Services, musisz wiedzieć o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Ograniczenia problem
#### <a name="scale-the-web-app"></a>Skalowanie aplikacji sieci web
W usłudze Azure App Service Aby zwiększyć wydajność i przepływność, można dostosować skali, w którym są uruchomione aplikacji. Skalowanie w górę aplikacji sieci web obejmuje dwie akcje powiązane: zmiana planu usługi aplikacji na wyższej warstwy cenowej i konfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji sieci web w usłudze Azure App Service](web-sites-scale.md).

Ponadto można wybrać do uruchamiania aplikacji na więcej niż jedno wystąpienie. Skalowanie w poziomie nie tylko zapewnia więcej możliwości przetwarzania, ale umożliwia także niektóre ilość odporność na uszkodzenia. Jeśli proces przestanie działać w jednym wystąpieniu, inne wystąpienia nadal obsługiwać żądań.

Można skonfigurować jako ręczne lub automatyczne skalowanie.

#### <a name="use-autoheal"></a>Funkcja AutoHeal użycia
Funkcja AutoHeal odtwarzania procesu roboczego dla aplikacji na podstawie ustawień wybranego (na przykład zmiany konfiguracji żądania, limity pamięci lub czas potrzebny na wykonanie żądania). W większości przypadków, odtworzenia procesu jest najszybszym sposobem odzyskanie problem. Chociaż zawsze można ponownie uruchomić aplikacji sieci web z bezpośrednio z poziomu portalu Azure, funkcja AutoHeal zrobi to automatycznie za użytkownika. To wszystko, co należy zrobić, Dodaj niektóre wyzwalacze w głównym pliku web.config dla aplikacji sieci web. Te ustawienia będzie działać w ten sam sposób, nawet jeśli aplikacja nie jest aplikacji .net.

Aby uzyskać więcej informacji, zobacz [automatyczne naprawianie Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Uruchom ponownie aplikację sieci web
Ponowne uruchomienie jest często Najprostszym sposobem, aby rozwiązać jednorazowo występujące problemy. Na [portalu Azure](https://portal.azure.com/), w bloku aplikacja sieci web ma opcji, aby zatrzymać lub uruchomić ponownie aplikację.

 ![ponowne uruchomienie aplikacji sieci web, aby rozwiązać problemy z wydajnością](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Można również zarządzać aplikacji sieci web przy użyciu programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).
