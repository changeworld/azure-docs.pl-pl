---
title: Rozwiązywanie problemów z spadku wydajności — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci aplikacja wolno Rozwiązywanie problemów z wydajnością w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: wydajności aplikacji sieci Web, wolno aplikacji app powolne
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2d17991854f13f889c4e8c3a8c6f18e933655546
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128453"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Aplikacja wolno Rozwiązywanie problemów z wydajnością w usłudze Azure App Service
Ten artykuł pomoże Ci rozwiązywania problemów z wydajnością aplikacji powolne [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Podczas przeglądania aplikacji, ładowania stron powoli, a czasem również limit czasu.

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowane przez problemy z poziomu aplikacji, takich jak:

* żądania sieci, trwa zbyt długo
* zapytania kodu lub bazy danych aplikacji jest nieefektywne
* aplikacji przy użyciu wysokie wykorzystanie pamięci/Procesora
* aplikacja uległa awarii z powodu wyjątku

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Rozwiązywanie problemów z można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Sprawdź i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Rozwiązać problem](#mitigate)

[Usługa App Service](overview.md) daje różne opcje w każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sprawdź i monitorowanie zachowania aplikacji
#### <a name="track-service-health"></a>Śledzenie kondycji usługi
Microsoft Azure publicizes każdorazowo, gdy występuje spadek przerw i wydajności usługi. Kondycja usługi można śledzić na [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzenie kondycji usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorowanie aplikacji
Ta opcja umożliwia dowiedzieć się, jeśli masz problemy aplikacji. W bloku aplikacji kliknij **żądania i błędy** kafelka. **Metryki** blok zawiera wszystkie metryki, które można dodać.

Niektóre metryki, które warto monitorować aplikację

* Średni zestaw roboczy pamięci
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![Monitorowanie wydajności aplikacji](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorowanie stanu punktu końcowego sieci web
Jeśli korzystasz z aplikacji **standardowa** warstwy cenowej, usługa App Service umożliwia monitorowanie dwa punkty końcowe z trzech lokalizacjach geograficznych.

Monitorowanie punktu końcowego konfiguruje testy sieci web z rozproszone geograficznie lokalizacje, które testują czas odpowiedzi i czas pracy z adresami URL sieci web. Test wykonuje operację GET protokołu HTTP na adres URL sieci web, aby określić czas odpowiedzi i czas pracy w każdej lokalizacji. Każdej skonfigurowanej lokalizacji uruchamia test co pięć minut.

Czas pracy jest monitorowana przy użyciu kody odpowiedzi HTTP, a czas odpowiedzi jest mierzony w milisekundach. Test monitorowania kończy się niepowodzeniem, jeśli kod odpowiedzi HTTP jest mniejszy niż 400 lub odpowiedzi trwa dłużej niż 30 sekund. Punkt końcowy jest uważana za dostępną, jeśli jego monitorowania testy z określonych lokalizacji.

Aby ustawić go tak, zobacz [monitorować aplikacje w usłudze Azure App Service](web-sites-monitor.md).

Zobacz też [witryn sieci Web Azure przechowywanie w górę oraz monitorowanie punktu końcowego — za pomocą Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) dla wideo na temat monitorowania punktu końcowego.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorowanie wydajności aplikacji za pomocą rozszerzeń
Można również monitorować wydajność aplikacji za pomocą *rozszerzenie witryny*.

Każda aplikacja usługi App Service zawiera punkt końcowy rozszerzonego zarządzania, który umożliwia korzystanie z zaawansowanego zestawu narzędzi wdrożonych jako rozszerzenia witryny. Rozszerzenia obejmują: 

- Edytorów kodów źródłowych, takich jak [DevOps platformy Azure](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Narzędzia do zarządzania dla połączonych zasobów, takich jak bazy danych MySQL jest podłączony do aplikacji.

[Usługa Azure Application Insights](https://azure.microsoft.com/services/application-insights/) jest rozszerzenie witryny, która jest również dostępna monitorowania wydajności. Aby korzystać z usługi Application Insights, możesz ponownie skompiluj kod, za pomocą zestawu SDK. Można także zainstalować rozszerzenia, które zapewnia dostęp do dodatkowych danych. Zestaw SDK pozwala napisać kod, aby monitorować użycie i wydajność Twojej aplikacji bardziej szczegółowo. Aby uzyskać więcej informacji, zobacz [monitorowania wydajności w aplikacjach sieci web](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
Usługa App Service oferuje funkcje diagnostyczne dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Informacje jest dzielony na diagnostyki serwera sieci web i diagnostyki aplikacji.

#### <a name="enable-web-server-diagnostics"></a>Włączanie diagnostyki serwera sieci web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

* **Szczegółowe rejestrowanie błędów** — szczegółowe informacje o błędzie dla kodów stanu HTTP, które wskazują błędu (kod stanu 400 lub nowszej). Może zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu.
* **Nie powiodło się żądanie śledzenia** — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śledzenia komponenty używani do przetwarzania żądania i czasu trwania w poszczególnych składnikach. Może to być przydatne, jeśli chcesz zwiększyć wydajność aplikacji lub określić, co powoduje określonego błędu HTTP.
* **Rejestrowanie serwera w sieci Web** — informacje o transakcji HTTP za pomocą rozszerzonym formacie W3C dziennika plików. Jest to przydatne podczas określania ogólnego metryki aplikacji, takie jak liczba żądań obsłużonych lub ile żądań pochodzących z określonego adresu IP.

#### <a name="enable-application-diagnostics"></a>Włączanie diagnostyki aplikacji
Dostępnych jest kilka opcji, aby zbierać dane dotyczące wydajności aplikacji usługi App Service, profil aplikacji na żywo w programie Visual Studio lub modyfikować kodu aplikacji, aby rejestrować dodatkowe informacje i dane śledzenia. Możesz wybrać opcji, w zależności od ilości dostępu do aplikacji i zaobserwowanie z monitorowania narzędzi.

##### <a name="use-application-insights-profiler"></a>Użyj Application Insights Profiler
Można włączyć Application Insights Profiler rozpocząć, przechwytywanie śladów wydajności szczegółowe. Możesz uzyskać dostęp do przechwycone ślady pięć dni temu niezbędne, aby badać problemy wystąpiły w przeszłości. Użytkownik wybierz tę opcję, tak długo, jak długo mają dostęp do aplikacji, zasób usługi Application Insights w witrynie Azure portal.

Application Insights Profiler zawiera dane statystyczne dotyczące czas odpowiedzi dla każdego wywołania sieci web i ślady, wskazujący, który wiersz kodu spowodowane powolnych odpowiedziach. Czasami aplikacja usługi App Service jest powolne, ponieważ niektórych kodu nie jest zapisywana w wydajny sposób. Przykłady obejmują sekwencyjnego kodu, który mogą być uruchamiane w rywalizacji o blokadę równoległe niepożądane i nieoczekiwane bazy danych. Usunięcie tych wąskich gardeł w kodzie zwiększa wydajność aplikacji, ale są trudne do wykrycia bez konfigurowania rozbudowane ślady i dzienniki. Ślady zebrane przez program Application Insights Profiler ułatwia identyfikowanie wierszy kodu, który spowalnia aplikacji i stawić czoła temu wyzwaniu dla aplikacji usługi App Service.

 Aby uzyskać więcej informacji, zobacz [profilowanie aplikacji na żywo w usłudze Azure App Service z usługą Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Użyj zdalnego profilowania
W usłudze Azure App Service, aplikacje sieci web, aplikacje interfejsu API zaplecza aplikacji mobilnych i zadania Webjob można zdalnie profilować. Wybierz tę opcję, jeśli masz dostęp do zasobów aplikacji i wiesz, jak prowadzące do odtworzenia problemu lub osobom znającym dokładny czas interwału się dzieje problemów z wydajnością.

Zdalne Profilowanie jest przydatne, jeśli użycie procesora CPU procesu jest duże i proces działa wolniej niż oczekiwano lub opóźnienia żądania HTTP są większe niż zwykle, można zdalnie profilować proces i uzyskać stosy wywołań próbkowania procesora CPU do analizowania procesu działanie i kod ścieżek krytycznych.

Aby uzyskać więcej informacji, zobacz [obsłudze zdalnego profilowania w usłudze Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Konfiguruj dane śledzenia diagnostycznego ręcznie
W przypadku dostępu do kodu źródłowego aplikacji sieci web diagnostyki aplikacji umożliwia przechwytywanie informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać `System.Diagnostics.Trace` klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Jednakże należy zmienić kodu i ponownego wdrażania aplikacji. Ta metoda jest zalecane, jeśli Twoja aplikacja działa w środowisku testowym.

Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania aplikacji na potrzeby rejestrowania, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji w usłudze Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Używanie narzędzia do diagnostyki
Usługa App Service udostępnia inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją, bez konieczności konfiguracji. Jeśli napotkasz problemy z aplikacją, narzędzia do diagnostyki wskaże na czym polega problem poświęcany na właściwe informacje, aby łatwiej i szybciej rozwiązać problem.

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do swojej aplikacji usługi App Service lub App Service Environment w [witryny Azure portal](https://portal.azure.com). Na lewym pasku nawigacyjnym kliknij **diagnozowanie i rozwiązywanie problemów**.

#### <a name="use-the-kudu-debug-console"></a>Użyj konsoli debugowania aparatu Kudu
Usługa App Service jest powiązana z konsoli debugowania, używanego do debugowania, eksplorowanie, przekazywanie plików, a także JSON punkty końcowe, w celu uzyskania informacji dotyczących środowiska. Ta konsola jest nazywany *konsoli Kudu* lub *pulpitu nawigacyjnego funkcji SCM* dla aplikacji.

Dostęp do tego pulpitu nawigacyjnego, przechodząc do łącza **https://&lt;Twoja nazwa aplikacji >.scm.azurewebsites.net/** .

Niektóre rzeczy, które zapewnia aparat Kudu, to:

* ustawienia środowiska dla aplikacji
* strumień dziennika
* diagnostyczne zrzutu
* Debuguj konsoli, w którym można uruchomić polecenia cmdlet programu Powershell i podstawowe polecenia systemu DOS.

Inną przydatną cechą Kudu jest, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć narzędzia Kudu i zrzuty narzędzie SysInternals Procdump, aby utworzyć pamięci. Te zrzuty pamięci są migawki procesu i często może pomóc rozwiązać bardziej skomplikowanych problemów z aplikacją.

Aby uzyskać więcej informacji na temat funkcji dostępnych w Kudu, zobacz [narzędzi DevOps platformy Azure należy wiedzieć o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Rozwiązać problem
#### <a name="scale-the-app"></a>Skalowanie aplikacji
W usłudze Azure App Service Aby zwiększyć wydajność i przepływność, można dostosować skali, w którym aplikacja jest uruchamiana. Skalowanie w górę aplikacji obejmuje dwie akcje powiązane: zmiana planu usługi App Service na wyższą warstwę cenową i konfigurowanie niektórych ustawień, po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji w usłudze Azure App Service](web-sites-scale.md).

Ponadto można uruchomić aplikację na więcej niż jedno wystąpienie. Skalowanie w poziomie nie tylko zapewnia więcej możliwości przetwarzania, ale również zapewnia pewien stopień odporności na uszkodzenia. Jeśli proces ulegnie awarii w jednym wystąpieniu, inne wystąpienia w dalszym ciągu obsługiwać żądań.

Możesz ustawić skalowanie ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Używają funkcji AutoHeal
Funkcji AutoHeal odtwarzania procesu roboczego dla aplikacji na podstawie ustawień wybranych (np. zmiany w konfiguracji, żądań, limity opartego na pamięci lub czas potrzebny do wykonania na żądanie). W większości przypadków, odtwarzania procesu jest najszybszym sposobem na odzyskanie problem. Chociaż można zawsze ponownie uruchomić aplikację z bezpośrednio z poziomu witryny Azure portal, funkcji AutoHeal zrobi to automatycznie za Ciebie. To wszystko, co należy zrobić, Dodaj niektórych wyzwalaczy w głównym pliku web.config aplikacji. Te ustawienia będzie działać w taki sam sposób, nawet jeśli aplikacja nie jest aplikacji platformy .NET.

Aby uzyskać więcej informacji, zobacz [Samonaprawianie witryn sieci Web Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uruchom ponownie aplikację
Ponowne uruchamianie często jest to najprostszy sposób, aby odzyskać sprawność po jednorazowych problemach. Na [witryny Azure portal](https://portal.azure.com/), w bloku aplikacji masz opcji, aby zatrzymać lub uruchomić ponownie aplikację.

 ![Uruchom ponownie aplikację, aby rozwiązać problemy z wydajnością](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Można również zarządzać swojej aplikacji za pomocą programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).
