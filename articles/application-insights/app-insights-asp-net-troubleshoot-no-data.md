---
title: Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
description: Nie można wyświetlać dane w usłudze Azure Application Insights? Spróbuj w tym miejscu.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: cdfc3ceb1ce94c4653a710658c1df991d9ca2c30
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294449"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
## <a name="some-of-my-telemetry-is-missing"></a>Brakuje części Moje telemetrii
*W usłudze Application Insights I zobaczyć tylko część zdarzenia generowane przez Moja aplikacja.*

* Stale wyświetlenie tego samego ułamek, jest prawdopodobnie z powodu adaptacyjną [próbkowania](app-insights-sampling.md). Można to potwierdzić, otwórz wyszukiwania (za pomocą bloku — omówienie) i przyjrzyj się wystąpienia na żądanie lub inne zdarzenia. W dolnej części sekcji właściwości kliknij przycisk "..." można uzyskać właściwości pełne szczegóły. Jeśli żądanie Count > 1, a następnie jest używany w operacji pobierania próbek. 
* W przeciwnym razie jest to możliwe, że w przypadku naciśnięcie [limit szybkości danych](app-insights-pricing.md#limits-summary) dla Twojego planu cenowego. Ograniczenia te są stosowane na minutę.

## <a name="no-data-from-my-server"></a>Żadne dane z serwerem
*Moja aplikacja został zainstalowany na serwerze sieci web, a nie widzisz wszystkie dane telemetryczne z niego. Na komputerze deweloperów Ci poszło OK.*

* Prawdopodobnie problem zapory. [Ustaw wyjątki zapory dla usługi Application Insights wysłać dane](app-insights-ip-addresses.md).
* Serwer usług IIS może brakować niektórych wymagań wstępnych: .NET Extensibility 4.5 i ASP.NET 4.5.

*I [zainstalowany Monitor stanu](app-insights-monitor-performance-live-website-now.md) na serwerze sieci web w celu monitorowania istniejących aplikacji. Nie ma żadnych wyników.*

* Zobacz [Rozwiązywanie problemów z Monitora stanu](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Brak opcji "Dodaj usługę Application Insights" w programie Visual Studio
*Gdy I kliknij prawym przyciskiem myszy istniejący projekt w Eksploratorze rozwiązań, nie ma żadnych opcji usługi Application Insights.*

* Nie wszystkie typy .NET projektu są obsługiwane przez narzędzia. Projekty sieci Web i WCF są obsługiwane. Dla innych typów projektów, takich jak aplikacje pulpitu lub usługi, nadal możesz [ręcznie Dodaj zestaw SDK usługi Application Insights do projektu](app-insights-windows-desktop.md).
* Upewnij się, że masz [programu Visual Studio 2013 Update 3 lub nowszym](http://go.microsoft.com/fwlink/?LinkId=397827). Pochodzi on wstępnie zainstalowane z narzędzia Developer Analytics, które zapewniają zestaw SDK usługi Application Insights.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **Developer Analytics Tools** jest zainstalowany i włączony. Jeśli tak, kliknij przycisk **aktualizacje** czy jest aktualizacja.
* Otwórz okno dialogowe Nowy projekt i wybierz aplikację sieci Web programu ASP.NET. Jeśli widzisz opcję usługi Application Insights są zainstalowane narzędzia. Jeśli nie, spróbuj odinstalować i ponowne zainstalowanie narzędzia Application Insights Tools.

## <a name="q02"></a>Dodawanie usługi Application Insights nie powiodło się
*Podczas próby dodania usługi Application Insights do istniejącego projektu, I wyświetlony komunikat o błędzie.*

Prawdopodobne przyczyny:

* Komunikacja z portalem usługi Application Insights nie powiodła się; lub
* Istnieje problem z Twoim kontem platformy Azure;
* Masz tylko [dostęp do odczytu do subskrypcji lub grupy, w którym chcesz utworzyć nowy zasób](app-insights-resources-roles-access-control.md).

Poprawka:

* Sprawdź podane poświadczenia logowania dla konta prawo platformy Azure. 
* W przeglądarce, sprawdź, czy masz dostęp do [portalu Azure](https://portal.azure.com). Otwórz ustawienia i czy ma żadnych ograniczeń.
* [Dodawanie usługi Application Insights do istniejącego projektu](app-insights-asp-net.md): W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie "Dodaj usługę Application Insights".
* Jeśli nadal nie działa, wykonaj [procedury ręcznego](app-insights-windows-services.md) Dodaj zasób w portalu, a następnie dodać zestawu SDK do projektu. 

## <a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucza Instrumentacji nie może być pusta"
Prawdopodobnie wystąpił problem podczas zostały instalacji usługi Application Insights lub być może adapter rejestrowania.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **usługi Application Insights > Konfiguruj usługę Application Insights**. Okno dialogowe, które umożliwiające logowanie do platformy Azure, zostanie wyświetlony i Utwórz zasobu usługi Application Insights lub ponownego użycia istniejącej.

## <a name="NuGetBuild"></a> "Pakietów NuGet brakuje" na serwerze kompilacji
*Wszystko, co tworzy OK I czy debugowanie na moim komputerze programowanie, ale pojawia się błąd NuGet na serwerze kompilacji.*

Zobacz [przywracania pakietów NuGet](http://docs.nuget.org/Consume/Package-Restore) i [automatyczne przywracanie pakietów](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Brak polecenia menu można otworzyć usługi Application Insights z programu Visual Studio
*I kliknij prawym przyciskiem myszy mój projekt w Eksploratorze rozwiązań, nie ma żadnych poleceń usługi Application Insights, lub nie widzę polecenia Otwórz usługę Application Insights.*

Prawdopodobne przyczyny:

* Jeśli ręcznie utworzyć zasobu usługi Application Insights, czy projekt jest typu, który nie jest obsługiwany przez narzędzia Application Insights.
* Narzędzia Developer Analytics są wyłączone w Visual Studio. 
* Visual Studio jest starsza niż 2013 Update 3.

Poprawka:

* Upewnij się, że używanej wersji programu Visual Studio 2013 z aktualizacją 3 lub nowszym.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **Developer Analytics tools** jest zainstalowany i włączony. Jeśli tak, kliknij przycisk **aktualizacje** czy jest aktualizacja.
* Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań. Jeśli widzisz polecenia **usługi Application Insights > Konfiguruj usługę Application Insights**, należy połączyć Twój projekt z zasobów w usłudze Application Insights.

W przeciwnym razie wartość danego typu projektu bezpośrednio nie jest obsługiwane przez narzędzia Application Insights. Aby wyświetlić telemetrii, zaloguj się do [portalu Azure](https://portal.azure.com), wybierz usługę Application Insights na lewym pasku nawigacyjnym i wybierz aplikację.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Odmowa dostępu" Otwieranie usługi Application Insights z programu Visual Studio
*Polecenia menu "Otwórz usługę Application Insights" powoduje przejście do portalu Azure, ale występuje błąd "odmowa dostępu".*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Logowanie Microsoft ostatnio używana w domyślnej przeglądarce nie ma dostępu do [zasobu, który został utworzony podczas dodawania usługi Application Insights do tej aplikacji](app-insights-asp-net.md). Istnieją dwie prawdopodobne przyczyny: 

* Masz więcej niż jedno konto Microsoft — może być służbowy i osobiste konto Microsoft? Logowanie, ostatnio używana w domyślnej przeglądarce był przeznaczony dla konta innego niż ten, który ma dostęp do [dodać usługi Application Insights do projektu](app-insights-asp-net.md). 
  
  * Poprawka: Kliknij swoją nazwę na prawej stronie okna przeglądarki z góry i Wyloguj. Następnie zaloguj się przy użyciu konta, które ma dostęp. Następnie na pasku nawigacyjnym po lewej stronie kliknij usługi Application Insights i wybierz aplikację.
* Ktoś dodać usługi Application Insights do projektu, a ich nie zapewniają [dostępu do grupy zasobów](app-insights-resources-roles-access-control.md) , w której został utworzony. 
  
  * Poprawka: Jeśli używane konto organizacyjne, mogą dodawać należy do zespołu; lub ich można przyznać indywidualnej operacji dostępu do grupy zasobów.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Zasobów" nie można odnaleźć na otwarcie usługi Application Insights z programu Visual Studio
*Polecenia menu "Otwórz usługę Application Insights" powoduje przejście do portalu Azure, ale występuje błąd "nie można odnaleźć zasobów".*

Prawdopodobne przyczyny:

* Usunięto zasób usługi Application Insights dla aplikacji; lub
* Klucz Instrumentacji został ustawiony lub zmienione w ApplicationInsights.config edytując je bezpośrednio, bez aktualizowania pliku projektu. 

Klucz Instrumentacji w formantach ApplicationInsights.config wysyłania danych telemetrycznych. Wiersz w pliku projektu kontrolki, którego zasobu jest otwarty, korzystając z polecenia programu Visual Studio. 

Poprawka:

* W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz usługi Application Insights, Konfiguruj usługę Application Insights. W oknie dialogowym albo można do wysyłania danych telemetrycznych do istniejącego zasobu lub Utwórz nową. Lub:
* Otwórz bezpośrednio zasób. Zaloguj się do [portalu Azure](https://portal.azure.com)kliknij usługi Application Insights na pasku nawigacyjnym po lewej stronie, a następnie wybierz aplikację.

## <a name="where-do-i-find-my-telemetry"></a>Gdzie znaleźć Moje telemetrii?
*Aby podpisanej [portalu Microsoft Azure](https://portal.azure.com), i wyświetlane Azure główny pulpit nawigacyjny. Dlatego gdzie można znaleźć dane usługi Application Insights?*

* Na pasku nawigacyjnym po lewej stronie kliknij opcję usługi Application Insights, a następnie nazwę aplikacji. Jeśli masz żadnych projektów, musisz [dodać lub skonfigurować usługi Application Insights do projektu sieci web](app-insights-asp-net.md).
  
    Brak zobaczysz Niektóre wykresy podsumowania. Możesz kliknąć za pośrednictwem je, aby wyświetlić więcej szczegółów.
* W programie Visual Studio podczas debugowania kodu aplikacji, kliknij przycisk Application Insights.

## <a name="q03"></a> Brak danych serwera (lub żadne dane na wszystkich)
*I Moja aplikacja uruchomiono, a potem usługa Application Insights w Microsoft Azure, ale wszystkich schematów Pokaż "Dowiedz się, jak zbierać..." lub "Nie skonfigurowano."* Lub, *tylko dane widoku strony i użytkownika, ale żadne dane z serwera.*

* Uruchom aplikację w trybie debugowania w programie Visual Studio (F5). Za pomocą aplikacji tak, aby wygenerować niektórych telemetrii. Sprawdź, czy można przejrzeć zdarzenia rejestrowane w oknie danych wyjściowych programu Visual Studio. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* Otwórz w portalu usługi Application Insights [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md). Dane zazwyczaj są wyświetlane tutaj najpierw.
* Kliknij przycisk Odśwież. Odświeża sam okresowo bloku, lecz możesz także zrobić to ręcznie. Interwał odświeżania jest dłużej większych przedziałów czasu.
* Sprawdź, czy klucze Instrumentacji pasują do siebie. W głównym bloku aplikacji w portalu usługi Application Insights w **Essentials** listy rozwijanej, obejrzyj **klucza Instrumentacji**. Następnie, w projekcie w programie Visual Studio Otwórz ApplicationInsights.config i Znajdź `<instrumentationkey>`. Sprawdź, czy dwa klucze są takie same. Jeśli nie:
  
  * W portalu kliknij przycisk Application Insights i poszukaj zasób aplikacji z kluczem prawo; lub
  * W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz usługi Application Insights, konfigurowanie. Zresetuj aplikację, aby wysłać dane telemetryczne do prawej zasobu.
  * Nie można odnaleźć pasującego klucze, sprawdź, czy w programie Visual Studio, jak używasz tych samych poświadczeń logowania do portalu.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* W [główny pulpit nawigacyjny Microsoft Azure](https://portal.azure.com), obejrzyj mapy usługi kondycji. W przypadku niektórych alertów oznaczeń, poczekaj, aż one zwrócono do OK, a następnie zamknij i otwórz ponownie z bloku aplikacji usługi Application Insights.
* Należy także sprawdzić [naszym blogu stan](http://blogs.msdn.com/b/applicationinsights-status/).
* Czy pisania kodu dla [po stronie serwera SDK](app-insights-api-custom-events-metrics.md) które może zmieniać klucza Instrumentacji w `TelemetryClient` wystąpień lub `TelemetryContext`? Lub czy zapisu [filtru lub próbkowania konfiguracji](app-insights-api-filtering-sampling.md) może on filtrować się zbyt dużo?
* W przypadku modyfikacji ApplicationInsights.config dokładnie sprawdź konfigurację [TelemetryInitializers i TelemetryProcessors](app-insights-api-filtering-sampling.md). Typ o nazwie niepoprawnie lub parametr może spowodować zestaw SDK, aby wysyłać żadnych danych.

## <a name="q04"></a>Brak danych dotyczących użycia wyświetleń strony, przeglądarek,
*Czas ładowania strony widoku lub w blokach przeglądarki lub użycia są widoczne dane na czas odpowiedzi serwera i żądań serwera, ale żadne dane.*

Dane pochodzą ze skryptów na stronach sieci web. 

* Jeśli dodano usługę Application Insights do istniejącego projektu sieci web, [należy ręcznie dodać skryptów](app-insights-javascript.md).
* Upewnij się, że program Internet Explorer lokacji nie są wyświetlane w trybie zgodności.
* Użyj funkcji debugowania w przeglądarce (F12 w niektórych przeglądarkach, następnie wybierz sieć), aby sprawdzić, czy dane są wysyłane do `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Brak danych zależności lub wyjątku
Zobacz [dane telemetryczne zależności](app-insights-asp-net-dependencies.md) i [dane telemetryczne dotyczące wyjątków](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Brak danych wydajności
Dane dotyczące wydajności (procesora CPU, szybkość We/Wy i tak dalej) jest dostępna dla [usług sieci web Java](app-insights-java-collectd.md), [aplikacji klasycznych systemu Windows](app-insights-windows-desktop.md), [sieci web IIS na aplikacje i usługi Jeśli Zainstaluj monitor stanu](app-insights-monitor-performance-live-website-now.md), i [Usług w chmurze azure](app-insights-azure.md). można znaleźć go w obszarze Ustawienia serwerów.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Żadne dane (serwer), ponieważ po opublikowaniu aplikacji serwerem
* Sprawdź faktycznie skopiowane przez firmę Microsoft. Biblioteki DLL ApplicationInsights do serwera, razem z Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* W zaporze, może być konieczne [Otwórz Niektóre porty TCP](app-insights-ip-addresses.md).
* Jeśli musisz użyć serwera proxy, aby wysłać spoza sieci firmowej, należy ustawić [defaultProxy —](https://msdn.microsoft.com/library/aa903360.aspx) w pliku Web.config
* Windows Server 2008: Upewnij się, że zostały zainstalowane następujące aktualizacje: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Używany do wyświetlania danych, ale została zatrzymana
* Sprawdź [blogu stan](http://blogs.msdn.com/b/applicationinsights-status/).
* Czy osiągnięto limitu przydziału miesięcznego punktów danych? Otwórz ustawienia/przydziału i cennik, aby dowiedzieć się. Jeśli tak, możesz uaktualnić swój plan lub zapłacić za dodatkowe pojemności. Zobacz [cennik schemat](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych I czy oczekiwana
Jeśli aplikacja wysyła dużą ilość danych i używasz SDK Insights aplikacji dla platformy ASP.NET w wersji 2.0.0-beta3 lub nowszego, [adaptacyjną próbkowania](app-insights-sampling.md) funkcja może działać i wysłać określonego odsetka telemetrii. 

Można ją wyłączyć, ale nie jest to zalecane. Próbkowanie zaprojektowano tak, aby poprawnie powiązane dane telemetryczne są przesyłane w celach diagnostycznych. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Niewłaściwe dane geograficzne w danych telemetrycznych użytkownika
Miasto, region i kraju wymiary są uzyskiwane z adresów IP, a nie zawsze są dokładne.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek „nie można odnaleźć metody” podczas uruchamiania w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Wersja 4.6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj wersję 4.6 w każdej roli](../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="still-not-working"></a>Nadal nie działa...
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

