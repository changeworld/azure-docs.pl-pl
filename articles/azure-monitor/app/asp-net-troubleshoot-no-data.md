---
title: Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
description: Nie widzisz danych w usłudze Azure Application Insights? Wypróbuj je tutaj.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: cf818756f583974a8a9b53a9a0cce31dd93d042b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299303"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
## <a name="some-of-my-telemetry-is-missing"></a>Brakuje części moich danych telemetrycznych
*W usłudze Application Insights I widoczne tylko ułamek zdarzeń, które są generowane przez aplikację.*

* Jeśli ciągle widzisz ten sam ułamek, prawdopodobnie z powodu adaptacyjne [próbkowania](../../azure-monitor/app/sampling.md). Aby to sprawdzić, należy otworzyć wyszukiwanie (z poziomu bloku Przegląd) i spójrz na wystąpienia na żądanie lub inne zdarzenie. W dolnej części sekcji właściwości kliknij przycisk "...", aby uzyskać szczegółowe informacje z pełną właściwość. Jeśli żądanie liczby > 1, a następnie próbkowania jest używany w operacji.
* W przeciwnym razie jest to możliwe, że występują [limit szybkości danych](../../azure-monitor/app/pricing.md#limits-summary) dla swój plan cenowy. Limity te są stosowane na minutę.

*Losowo występują utraty danych.*

* Sprawdź, jeśli występują utratę danych na [kanału danych Telemetrycznych](telemetry-channels.md#does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost)

* Sprawdź, czy wszelkich znanych problemach w kanale danych Telemetrycznych [repozytorium Github](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Występują utraty danych w aplikacji konsoli lub aplikacji sieci Web, gdy aplikacja jest zatrzymana.*

* Zestaw SDK kanału przechowuje dane telemetryczne w buforze i wysyła je w partiach. Jeśli dana aplikacja jest zamykana, konieczne może być jawnie wywołać [Flush()](api-custom-events-metrics.md#flushing-data). Zachowanie `Flush()` jest zależny od rzeczywistego [kanału](telemetry-channels.md#built-in-telemetrychannels) używane.

## <a name="no-data-from-my-server"></a>Żadne dane z serwera
*Po zainstalowaniu aplikacji na serwerze sieci web, a nie widzisz żadnych danych telemetrycznych z niego. Poszło OK na moim komputerze deweloperskim.*

* Prawdopodobnie problem z zaporą. [Ustaw wyjątki zapory dla usługi Application Insights do przesyłania danych](../../azure-monitor/app/ip-addresses.md).
* Serwer usług IIS może brakować niektórych wymagań wstępnych: .NET Extensibility 4.5 i ASP.NET 4.5.

*Czy mogę [zainstalować Monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md) na serwerze sieci web, aby monitorować istniejące aplikacje. Nie widzę żadnych wyników.*

* Zobacz [Rozwiązywanie problemów z Monitora stanu](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Nie opcji "Dodaj usługę Application Insights" w programie Visual Studio
*Czy mogę kliknij prawym przyciskiem myszy istniejący projekt w Eksploratorze rozwiązań, nie widzę żadnych opcji usługi Application Insights.*

* Nie wszystkie typy projektu .NET są obsługiwane przez narzędzia. Projekty sieci Web i usługi WCF są obsługiwane. Dla innych typów projektów, takich jak aplikacje klasycznych lub usługi, nadal możesz [ręcznie dodać zestaw SDK usługi Application Insights do projektu](../../azure-monitor/app/windows-desktop.md).
* Upewnij się, że masz [Visual Studio 2013 Update 3 lub nowszym](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Jest on wstępnie zainstalowany, za pomocą narzędzi Developer Analytics, które zawierają zestaw SDK usługi Application Insights.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **Developer Analytics Tools** jest zainstalowane i włączone. Jeśli tak, kliknij przycisk **aktualizacje** można sprawdzić, czy jest aktualizacja.
* Otwórz okno dialogowe Nowy projekt i wybierz aplikację sieci Web ASP.NET. Jeśli widzisz ma opcji Application Insights, są zainstalowane narzędzia. Jeśli nie, spróbuj odinstalować, a następnie zainstalować ponownie Developer Analytics Tools.

## <a name="q02"></a>Dodawanie usługi Application Insights nie powiodło się
*Podczas próby dodania usługi Application Insights do istniejącego projektu, widzę komunikat o błędzie.*

Prawdopodobne przyczyny:

* Komunikacja z portalem usługi Application Insights nie powiodła się; lub
* Występuje problem z kontem platformy Azure;
* Masz tylko [dostęp do odczytu do subskrypcji lub grupy, którym chcesz utworzyć nowy zasób](../../azure-monitor/app/resources-roles-access-control.md).

Poprawka:

* Sprawdź podane poświadczenia logowania dla konta bezpośrednio do platformy Azure.
* W przeglądarce, sprawdź, czy masz dostęp do [witryny Azure portal](https://portal.azure.com). Otwórz okno Ustawienia i czy ma żadnych ograniczeń.
* [Dodaj usługę Application Insights do istniejącego projektu](../../azure-monitor/app/asp-net.md): W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję "Dodaj usługę Application Insights."

## <a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucz Instrumentacji nie może być pusty"
Prawdopodobnie wystąpił problem podczas były używane do instalowania usługi Application Insights lub może być karty rejestrowania.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **usługi Application Insights > Konfiguruj usługę Application Insights**. Zobaczysz okno dialogowe, które umożliwiające logowanie do platformy Azure, a następnie utwórz zasób usługi Application Insights lub ponownego użycia istniejącej.

## <a name="NuGetBuild"></a> "Pakiety NuGet są brakujące" na serwerze kompilacji
*Wszystko, co tworzy OK I jestem debugowania na mojej maszynie deweloperskiej, kiedy pojawia się błąd NuGet na serwerze kompilacji.*

Zobacz [Przywracanie pakietów NuGet](https://docs.nuget.org/Consume/Package-Restore) i [automatyczne przywracanie pakietu](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Brak polecenia menu, aby otworzyć usługę Application Insights w programie Visual Studio
*Gdy klikam prawym przyciskiem myszy mój projekt w Eksploratorze rozwiązań nie widać żadnych poleceń usługi Application Insights lub nie jest wyświetlane na polecenie Otwórz usługę Application Insights.*

Prawdopodobne przyczyny:

* Jeśli ręcznie utworzono zasób usługi Application Insights lub jeśli projekt jest typu, który nie jest obsługiwana za pomocą narzędzi usługi Application Insights.
* Narzędzia Developer Analytics są wyłączone w programu Visual Studio.
* Programu Visual Studio jest starsza niż 2013 Update 3.

Poprawka:

* Upewnij się, że Twoja wersja programu Visual Studio 2013 z aktualizacją 3 lub nowszej.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **narzędzia Developer Analytics** jest zainstalowane i włączone. Jeśli tak, kliknij przycisk **aktualizacje** można sprawdzić, czy jest aktualizacja.
* Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań. Jeśli widzisz polecenia **usługi Application Insights > Konfiguruj usługę Application Insights**, za jego pomocą łączyć projektu do zasobu usługi Application Insights.

W przeciwnym razie typu projektu nie są bezpośrednio obsługiwane przez narzędzia Developer Analytics. Aby wyświetlić dane telemetryczne, zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz pozycję Application Insights na pasku nawigacyjnym po lewej stronie i wybierz swoją aplikację.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Odmowa dostępu" Otwieranie usługi Application Insights w programie Visual Studio
*Polecenia menu "Otwórz usługę Application Insights" powoduje przejście do witryny Azure portal, ale pojawia się błąd "odmowa dostępu".*

Logowania firmy Microsoft, co ostatnio w domyślnej przeglądarce, nie ma dostępu do [zasobu, który został utworzony, gdy usługa Application Insights została dodana do tej aplikacji](../../azure-monitor/app/asp-net.md). Istnieją dwie prawdopodobne przyczyny:

* Masz więcej niż jedno konto Microsoft — może być służbowego i osobistego konta Microsoft? Ostatnio używana w domyślnej przeglądarce logowanie dla konta innego niż ten, który ma dostęp do [Dodaj usługę Application Insights do projektu](../../azure-monitor/app/asp-net.md).
  * Poprawka: Kliknij swoją nazwę użytkownika w prawym górnym rogu okna przeglądarki, a następnie zaloguj. Następnie zaloguj się przy użyciu konta, które ma dostęp. Na pasku nawigacyjnym po lewej stronie kliknij usługę Application Insights i wybierz aplikację.
* Ktoś inny dodane do projektu usługi Application Insights i ich nie zapewniają [dostęp do grupy zasobów](../../azure-monitor/app/resources-roles-access-control.md) , w której została utworzona.
  * Poprawka: Jeśli używane konto organizacyjne, ich można dodać do zespołu; lub ich może nadać Ci indywidualnej operacji dostępu do grupy zasobów.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Zasób" nie można odnaleźć na otwieranie usługi Application Insights w programie Visual Studio
*Polecenia menu "Otwórz usługę Application Insights" powoduje przejście do witryny Azure portal, ale pojawia się błąd "nie znaleziono zasobu".*

Prawdopodobne przyczyny:

* Usunięto zasób usługi Application Insights dla aplikacji; lub
* Klucz Instrumentacji został ustawiony lub zmienione w pliku ApplicationInsights.config, edytując je bezpośrednio, bez aktualizowania pliku projektu.

Klucz Instrumentacji w kontrolkach plik ApplicationInsights.config wysyłania danych telemetrycznych. Wiersz w pliku projektu steruje zasobu, który jest otwierany, gdy użyjesz polecenia w programie Visual Studio.

Poprawka:

* W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję Application Insights, Konfiguruj usługę Application Insights. W oknie dialogowym albo można wysyłać dane telemetryczne do istniejącego zasobu lub Utwórz nową. lub:
* Otwórz zasób bezpośrednio. Zaloguj się do [witryny Azure portal](https://portal.azure.com), kliknij usługę Application Insights na pasku nawigacyjnym po lewej stronie, a następnie wybierz aplikację.

## <a name="where-do-i-find-my-telemetry"></a>Gdzie znaleźć swoją telemetrię?
*Pomimo zalogowania się celu [portalu Microsoft Azure](https://portal.azure.com), i wyświetlane głównego pulpitu nawigacyjnego platformy Azure. Dlatego gdzie znaleźć Moje dane usługi Application Insights?*

* Na pasku nawigacyjnym po lewej stronie kliknij usługę Application Insights, a następnie nazwy aplikacji. Jeśli masz żadnych projektów, musisz [dodawać i konfigurować usługi Application Insights w projekcie sieci web](../../azure-monitor/app/asp-net.md).  
  Zobaczysz tam Niektóre wykresy podsumowujące. Kliknij je, aby wyświetlić więcej szczegółów.
* W programie Visual Studio podczas debugowania aplikacji, kliknij przycisk Application Insights.

## <a name="q03"></a> Brak danych serwera (lub żadne dane w ogóle)
*Czy uruchomiono aplikację, a potem usługi Application Insights w systemie Microsoft Azure, ale wszystkie wykresy pokazują "Dowiedz się, jak zbierać..." lub "Nie skonfigurowano."* Ewentualnie *tylko dane widoku strony i użytkownika, ale bez danych serwera.*

* Uruchom aplikację w trybie debugowania w programie Visual Studio (F5). Za pomocą aplikacji w taki sposób, aby wygenerować dane telemetryczne. Upewnij się, że można wyświetlić w oknie danych wyjściowych programu Visual Studio rejestrowane zdarzenia.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Otwórz w portalu usługi Application Insights [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md). Dane zwykle są wyświetlane w tym miejscu najpierw.
* Kliknij przycisk Odśwież. Blok odświeża się okresowo, ale możesz także zrobić to ręcznie. Interwał odświeżania wynosi więcej czasu większe zakresy czasu.
* Sprawdź, czy klucze Instrumentacji są zgodne. W głównym bloku aplikacji w portalu usługi Application Insights w **Essentials** listy rozwijanej, Przyjrzyj się **klucz Instrumentacji**. Następnie w projekcie w programie Visual Studio, otwórz plik ApplicationInsights.config i Znajdź `<instrumentationkey>`. Sprawdź, czy dwa klucze są takie same. W przeciwnym razie:  
  * W portalu kliknij przycisk Application Insights i poszukaj zasób aplikacji za pomocą Strzałka w prawo; lub
  * W Eksploratorze rozwiązań w usłudze Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz pozycję Application Insights, Konfiguruj. Zresetuj aplikację, aby wysyłać dane telemetryczne do właściwy zasób.
  * Jeśli nie można odnaleźć pasującego klucze, należy sprawdzić, czy w programie Visual Studio, jak używasz tych samych poświadczeń logowania do portalu.
* W [głównego pulpitu nawigacyjnego Microsoft Azure](https://portal.azure.com), spójrz na mapę usługi kondycji. W przypadku niektórych alertów oznaczeń, poczekaj na ich powrotu do OK i następnie zamknięcie i ponowne otwarcie bloku aplikacji usługi Application Insights.
* Należy także sprawdzić [naszym blogu stan](https://blogs.msdn.microsoft.com/servicemap-status/).
* Czy napisałeś cały kod dla [SDK po stronie serwera](../../azure-monitor/app/api-custom-events-metrics.md) , mogą ulec zmianie klucz Instrumentacji w `TelemetryClient` wystąpień lub `TelemetryContext`? Lub czy napisałeś [konfiguracji filtru lub próbkowania](../../azure-monitor/app/api-filtering-sampling.md) może on filtrować się zbyt dużo?
* Jeśli edytowano plik applicationinsights.config został dostosowany, dokładnie sprawdź konfigurację [TelemetryInitializers i TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Typ o nazwie nieprawidłowo lub parametru może spowodować zestawu SDK do wysyłania żadnych danych.

## <a name="q04"></a>Brak danych użycia wyświetleń stron, przeglądarek
*Widzę danych na wykresach czasu odpowiedzi serwera i żądaniami serwera, ale nie w czasie ładowania widoku strony i w blokach przeglądarka i użycie.*

Dane pochodzą ze skryptów na stronach sieci web. 

* Jeśli dodano usługę Application Insights do istniejącego projektu sieci web, [trzeba ręcznie dodać skrypty](../../azure-monitor/app/javascript.md).
* Upewnij się, że program Internet Explorer nie są wyświetlane witryny w trybie zgodności.
* Użyj funkcji debugowania w przeglądarce (F12 w niektórych przeglądarkach, następnie wybierz sieć), aby sprawdzić, czy dane są wysyłane do `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Brak danych zależności lub wyjątku
Zobacz [telemetrii zależności](../../azure-monitor/app/asp-net-dependencies.md) i [dane telemetryczne dotyczące wyjątków](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Brak danych wydajności
Dane dotyczące wydajności (procesora CPU, we/wy i tak dalej) jest dostępna dla [usług sieci web Java](../../azure-monitor/app/java-collectd.md), [aplikacje komputerowe Windows](../../azure-monitor/app/windows-desktop.md), [usług IIS i aplikacje sieci web usług jeśli instalowany jest monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md), i [Usług azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). znajdziesz go w obszarze Ustawienia serwerów.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Brak danych (serwer), ponieważ po opublikowaniu aplikacji się z serwerem
* Sprawdź, faktycznie kopiowane przez firmę Microsoft. DLL dotycząca usługi Application Insights do serwera, wraz z Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* W zaporze, może być konieczne [otworzyć Niektóre porty TCP](../../azure-monitor/app/ip-addresses.md).
* W przypadku korzystania z serwera proxy, aby wysłać poza siecią firmową, ustaw [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) w pliku Web.config
* Windows Server 2008: Upewnij się, że zainstalowano następujące aktualizacje: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Używany do wyświetlania danych, ale została zatrzymana
* Sprawdź [blogu stan](https://blogs.msdn.com/b/applicationinsights-status/).
* Możesz przekroczyć miesięczny limit punktów danych? Otwórz ustawienia/przydział i cennik, aby dowiedzieć się. Jeśli tak, możesz uaktualnić swój plan lub płacić za dodatkową pojemność. Zobacz [ceny schemat](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których oczekiwano
Jeśli Twoja aplikacja wysyła dużo danych, a używasz zestawu SDK Application Insights dla platformy ASP.NET w wersji 2.0.0-beta3 lub nowszej, [próbkowanie adaptacyjne](../../azure-monitor/app/sampling.md) funkcja może działać i wysyłanie tylko ułamka telemetrii.

Można ją wyłączyć, ale nie jest to zalecane. Próbkowanie jest zaprojektowana tak, że poprawnie powiązane dane telemetryczne są przesyłane w celach diagnostycznych.

## <a name="client-ip-address-is-0000"></a>Adres IP klienta ma wartość 0.0.0.0

Na 5 lutego 2018 r. ogłosiliśmy, że firma Microsoft usunęła rejestrowania adres IP klienta. Nie ma to wpływu na lokalizacji geograficznej.

> [!NOTE]
> Jeśli potrzebujesz pierwsze 3 oktety adresu IP, możesz użyć [inicjatora telemetrii](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) można dodać atrybutu niestandardowego.
> Nie ma to wpływu na dane zebrane przed 5 lutego 2018 r.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Nieprawidłowe dane geograficzne w danych telemetrycznych użytkownika
Miasto, region i kraj wymiary są uzyskiwane z adresów IP, a nie zawsze są dokładne. Te adresy IP są przetwarzane jako pierwsze dla lokalizacji, a następnie został 0.0.0.0 ma być przechowywany.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek „nie można odnaleźć metody” podczas uruchamiania w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Wersja 4.6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj wersję 4.6 w każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="troubleshooting-logs"></a>Dzienniki rozwiązywania problemów

Wykonaj te instrukcje, aby przechwycić dzienniki rozwiązywania problemów dla używanej platformy.

### <a name="net-framework"></a>.NET Framework

1. Zainstaluj [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) pakietu NuGet. Wersję, którą należy zainstalować musi odpowiadać bieżącej zainstalowanej wersji programu `Microsoft.ApplicationInsighs`

2. Zmodyfikuj plik applicationinsights.config został dostosowany do obejmują następujące czynności:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Aplikacja musi mieć uprawnienia do zapisu do skonfigurowanej lokalizacji

3. Uruchom ponownie proces, te nowe ustawienia są pobierane przez zestaw SDK

4. Po zakończeniu, należy cofnąć te zmiany.

### <a name="net-core"></a>.NET Core

1. Zainstaluj [Microsoft.AspNetCore.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.ApplicationInsights.HostingStartup) pakietu NuGet. Wersję, którą należy zainstalować musi odpowiadać bieżącej zainstalowanej wersji programu `Microsoft.ApplicationInsights`

2. Modyfikowanie `ConfigureServices` method in Class metoda swoje `Startup.cs` klasy.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Aplikacja musi mieć uprawnienia do zapisu do skonfigurowanej lokalizacji

3. Uruchom ponownie proces, te nowe ustawienia są pobierane przez zestaw SDK

4. Po zakończeniu, należy cofnąć te zmiany.

## <a name="still-not-working"></a>Nadal nie działa...
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
