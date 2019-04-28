---
title: Przechowywanie danych i magazynu w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Deklaracja zasad przechowywania i ochrona prywatności
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mbullwin
ms.openlocfilehash: 0f8f1c5585eb13506baea1e5ddbe611cc931758e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899252"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights

Po zainstalowaniu [usługi Azure Application Insights] [ start] zestawu SDK w Twojej aplikacji wysyła telemetrię dotyczącą swojej aplikacji w chmurze. Oczywiście deweloperzy odpowiada chcą wiedzieć, dokładnie przesyłanych danych, co się dzieje z danymi i jak można zachować kontrolę nad nim. W szczególności można było wysłać dane poufne, gdzie są przechowywane i jak bezpieczne jest? 

Pierwszy, krótka odpowiedź:

* Moduły standardowe dane telemetryczne, które Uruchom "fabrycznej" prawdopodobnie nie wysyłać danych poufnych do usługi. Dane telemetryczne dotyczy obciążenia, metryki wydajności i użycia, raporty dotyczące wyjątków i inne dane diagnostyczne. Dane użytkownika głównego widoczne w raporty diagnostyczne są adresami URL; ale aplikacja nie powinna w każdym przypadku poufnych danych w postaci zwykłego tekstu w adresie URL.
* Można napisać kod, który wysyła dodatkowe niestandardowych danych telemetrycznych do udzielenia odpowiedzi na dane diagnostyczne i użycia monitorowania. (Rozszerzania jest atrakcyjną funkcją usługi Application Insights). Będzie to możliwe, przez pomyłkę, aby napisać ten kod, aby obejmowała osobistych, jak i innych poufnych danych. Jeśli aplikacja działa przy użyciu tych danych, należy zastosować procesy szczegółowej weryfikacji do tworzonego kodu.
* Podczas tworzenia i testowania aplikacji, to łatwo sprawdzić, co jest wysyłane przez zestaw SDK. Dane są wyświetlane w oknie danych wyjściowych debugowania w IDE i przeglądarki. 
* Dane są przechowywane w [Microsoft Azure](https://azure.com) serwerów w Stanach Zjednoczonych lub Europy. (Ale aplikację można uruchomić gdziekolwiek). Platforma Azure ma [silne zabezpieczenie przetwarza i spełnia szeroki zakres standardami](https://azure.microsoft.com/support/trust-center/). Tylko dla Ciebie i Twojego zespołu wyznaczonym należy mieć dostęp do danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do jej tylko w szczególnych okolicznościach ograniczone, korzystając ze znajomości. Jest on zaszyfrowany przesyłanych i magazynowanych.

W pozostałej części tego artykułu bardziej pełni rosnącego odpowiedzi na te pytania. Ustalono, aby być na niezależne tak, aby je pokazać współpracownikom, którzy nie należą do natychmiastowego zespołu.

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
[Usługa Azure Application Insights] [ start] jest usługę oferowaną przez firmę Microsoft, która pomaga zwiększyć wydajność i użyteczność działającej aplikacji. Monitoruje ona cały czas, którym jest uruchomiona, podczas testowania i po jej wdrożeniu lub opublikowanych aplikacji. Usługa Application Insights tworzy wykresy i tabele, w których znajdziesz informacje, na przykład, jakich porach dnia otrzymasz większość użytkowników, jak szybko reagujących aplikacji jest i jak dobrze jest obsługiwany przez zewnętrzne usługi, których jest zależna. Jeśli występują awarie, błędy lub problemy z wydajnością, możesz wyszukać dane telemetryczne szczegółowo przyczynę. I Usługa wyśle do Ciebie wiadomości e-mail, jeśli wystąpiły jakiekolwiek zmiany w dostępności i wydajności aplikacji.

W aplikacji, która staje się częścią jego kodu, aby pobrać tę funkcję, zainstaluj zestaw SDK usługi Application Insights. Gdy aplikacja jest uruchomiona, zestaw SDK monitoruje jego działanie i wysyła dane telemetryczne do usługi Application Insights. Jest to usługa w chmurze hostowana przez [Microsoft Azure](https://azure.com). (Ale usługi Application Insights działa w przypadku aplikacji, nie tylko te, które są hostowane na platformie Azure).

Usługa Application Insights są przechowywane i analizuje dane telemetryczne. Aby wyświetlić analizy lub przeszukaj zachowane dane telemetryczne, zaloguj się do konta platformy Azure i otwórz zasób usługi Application Insights dla aplikacji. Dostęp do danych można także udostępnić innym członkom zespołu lub z określoną subskrypcją platformy Azure.

Może mieć danych wyeksportowanych z usługi Application Insights, na przykład do bazy danych lub do zewnętrznego narzędzia. Każde narzędzie zapewnia specjalny klucz, który można uzyskać z usługi. Mogą być odwoływane klucz, jeśli to konieczne. 

Zestawy SDK usługi Application Insights są dostępne dla zakresu typów aplikacji: sieci web usług hostowanych w Twoich własnych serwerach Java EE lub ASP.NET lub w środowisku Azure. sieci Web klientów — oznacza to, że kod uruchomiony na stronie sieci web; aplikacje komputerowe i usług. aplikacje urządzenia, takie jak Windows Phone, iOS i Android. Wszystkie one wysyłają dane telemetryczne do tej samej usługi.

## <a name="what-data-does-it-collect"></a>Jakie dane są jej zbierane?
### <a name="how-is-the-data-is-collected"></a>Jak to dane są zbierane?
Istnieją trzy źródła danych:

* Zestaw SDK, integracja z aplikacją albo [w rozwoju](../../azure-monitor/app/asp-net.md) lub [w czasie wykonywania](../../azure-monitor/app/monitor-performance-live-website-now.md). Istnieją różne zestawy SDK dla typy innej aplikacji. Istnieje również [zestawu SDK dla stron sieci web](../../azure-monitor/app/javascript.md), który ładuje do przeglądarki użytkownika końcowego oraz strony.
  
  * Każdy zestaw SDK zawiera szereg [modułów](../../azure-monitor/app/configuration-with-applicationinsights-config.md), które używają różnych technik zbierać różne rodzaje danych telemetrycznych.
  * Po zainstalowaniu zestawu SDK w rozwoju, można użyć jej interfejsu API do wysyłania własnej telemetrii, oprócz standardowych modułów. Tej niestandardowej telemetrii może zawierać żadnych danych, które mają zostać wysłane.
* Na niektórych serwerach sieci web są również agentów razem aplikacji, które wysyłają dane telemetryczne dotyczące procesora CPU, pamięci i rozszerzenia sieci. Na przykład maszyny wirtualne platformy Azure, hostów platformy Docker i [serwery Java EE](../../azure-monitor/app/java-agent.md) mogą mieć takich agentów.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) są uruchamiane procesy, przez firmę Microsoft, które wysyłają żądania do aplikacji sieci web w regularnych odstępach czasu. Wyniki są wysyłane do usługi Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jakiego rodzaju dane są zbierane?
Dostępne są następujące główne kategorie:

* [Dane telemetryczne serwera Web](../../azure-monitor/app/asp-net.md) -żądania HTTP.  Identyfikator URI, czas poświęcony na przetworzenie żądania, kod odpowiedzi, adres IP klienta. Identyfikator sesji.
* [Strony sieci Web](../../azure-monitor/app/javascript.md) — liczby stron, użytkowników i sesji. Czasy ładowania stron. Liczba wyjątków. Wywołania AJAX.
* Wydajność liczniki — pamięci, procesora CPU, we/wy, zajętość sieci.
* Klient i serwer kontekst — system operacyjny, ustawienia regionalne, typu urządzenia, przeglądarki, rozdzielczość ekranu.
* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md) i awarie - **zrzuty stosu**, identyfikator, typ Procesora kompilacji. 
* [Zależności](../../azure-monitor/app/asp-net-dependencies.md) -wywołania usług zewnętrznych, takich jak REST i SQL, AJAX. Identyfikator URI lub parametrów połączenia, czas trwania, Powodzenie, polecenia.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) — czas trwania testów i kroków, odpowiedzi.
* [Dzienniki śledzenia](../../azure-monitor/app/asp-net-trace-logs.md) i [niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md) - **niczego kodu w dziennikach lub dane telemetryczne**.

[Więcej szczegółów](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak sprawdzić, jakie są zbierane?
Jeśli tworzysz aplikację za pomocą programu Visual Studio, należy uruchomić tę aplikację w trybie debugowania (F5). Dane telemetryczne pojawią się w oknie danych wyjściowych. Z tego miejsca możesz skopiować go i sformatować je jako dane JSON łatwą inspekcję. 

![](./media/data-retention-privacy/06-vs.png)

Dostępna jest również bardziej czytelny widok w oknie diagnostyki.

Dla stron sieci web Otwórz okno debugowania w przeglądarce.

![Naciśnij klawisz F12, aby otworzyć kartę sieci.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Czy można napisać kod, aby filtrować dane telemetryczne przed wysłaniem?
Będzie to możliwe, pisząc [wtyczki procesora telemetrii](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak długo są przechowywane dane?
Punkty danych pierwotnych (czyli elementów, które można wykonywać zapytania w usłudze Analytics i sprawdzanie w wyszukiwaniu) są przechowywane przez 90 dni. Jeśli zachodzi potrzeba przechowywać danych dłużej niż ta, możesz użyć [Eksport ciągły](../../azure-monitor/app/export-telemetry.md) skopiowania go do konta magazynu.

Zagregowane dane (oznacza to, liczby, średnie i innych danych statystycznych, który zostanie wyświetlony w Eksploratorze metryk) są zachowane w ziarna wynoszącym 1 minutę przez 90 dni.

[Debugowanie migawek](../../azure-monitor/app/snapshot-debugger.md) są przechowywane przez 7 dni. Te zasady przechowywania jest ustawiona na podstawie poszczególnych aplikacji. Jeśli potrzebujesz zwiększyć tę wartość, możesz poprosić o zwiększenie przez otwarcie zgłoszenia do pomocy technicznej w witrynie Azure portal.

## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
Dane są widoczne dla użytkownika oraz, jeśli masz konto organizacji, członków zespołu. 

Można można wyeksportować przez Ciebie i członków zespołu i może być skopiowany do innej lokalizacji i przekazywane do innych osób.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Co robi firmy Microsoft informacje, które Moja aplikacja wysyła do usługi Application Insights?
Firma Microsoft używa danych tylko w celu świadczenie tej usługi.

## <a name="where-is-the-data-held"></a>Gdzie są przechowywane dane?
* W USA, Europa lub Azja południowo-wschodnia. Podczas tworzenia nowego zasobu usługi Application Insights, można wybrać lokalizację. 

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Oznacza, że Moja aplikacja musi być obsługiwana w USA, Europa lub Azja południowo-wschodnia?
* Nie. Aplikację można uruchomić dowolnego miejsca i w hostów w środowisku lokalnym lub w chmurze.

## <a name="how-secure-is-my-data"></a>Jak bezpieczne są moje dane?
Usługa Application Insights to usługa platformy Azure. Zasady zabezpieczeń są opisane w artykule [oficjalny dokument zabezpieczenia platformy Azure, ochrony prywatności i zgodności](https://go.microsoft.com/fwlink/?linkid=392408).

Dane są przechowywane na serwerach Microsoft Azure. W przypadku kont w witrynie Azure Portal konta ograniczenia są opisane w [dokumentu zabezpieczeń platformy Azure, ochrony prywatności i zgodności](https://go.microsoft.com/fwlink/?linkid=392408).

Dostęp do danych przez personel firmy Microsoft jest ograniczona. Mamy dostęp do danych, tylko za zgodą użytkownika, a jeśli jest konieczna Obsługa korzystania z usługi Application Insights. 

Dane w agregacji w aplikacjach wszystkich naszych klientów (np. przesyłania danych i średni rozmiar danych śledzenia) są używane do poprawy usługi Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Czy dane telemetryczne osoby może kolidować z Moje dane usługi Application Insights?
Można dodatkowe dane telemetryczne są wysyłane do swojego konta przy użyciu klucza instrumentacji, który można znaleźć w kodzie strony sieci web. Wystarczającą ilość dodatkowych danych metryk będzie poprawnie reprezentuje wydajność i użycie aplikacji.

Jeśli kod jest współużytkowana z innymi projektami, pamiętaj, aby usunąć klucz instrumentacji.

## <a name="is-the-data-encrypted"></a>Czy dane są szyfrowane?
Wszystkie dane są szyfrowane, gdy i ponieważ przechodzi między danymi centrów.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Czy dane są szyfrowane podczas przesyłania z mojej aplikacji na serwerach usługi Application Insights?
Tak, będziemy używać protokołu https z wysyłania danych do portalu z niemal wszystkie zestawy SDK, w tym serwerów sieci web, urządzeń i stron sieci web protokołu HTTPS. Jedynym wyjątkiem jest dane wysyłane z zwykły stron sieci web HTTP.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Zestaw SDK tworzy tymczasowy magazyn lokalny?

Tak, niektóre kanałów danych Telemetrycznych utrwalić dane lokalnie, jeśli nie można nawiązać połączenia z punktem końcowym. Przejrzyj poniżej można zobaczyć, które struktury i danych telemetrycznych kanałów dotyczy problem.

Kanały dane telemetryczne, które korzystają z magazynu lokalnego utworzyć pliki tymczasowe w katalogach TEMP lub folder dane aplikacji, które są ograniczone do określonego konta działania aplikacji. Może się tak zdarzyć, gdy punkt końcowy został tymczasowo niedostępna lub zostanie osiągnięty limit ograniczania przepustowości. Gdy ten problem został rozwiązany, kanału danych telemetrycznych zostanie wznowiona wysyłania wszystkich nowych i istniejących danych.

Ta utrwalone dane nie są szyfrowane lokalnie. Jeśli jest to niepożądane, sprawdź dane i ograniczyć zbieranie prywatnych danych. (Zobacz [sposobu eksportowania i usuwania danych prywatnych](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) Aby uzyskać więcej informacji.)

Jeśli klient potrzebuje do konfigurowania tego katalogu z wymaganiami zabezpieczeń można skonfigurować na framework. Upewnij się, że proces uruchamiania aplikacji ma dostęp do zapisu do tego katalogu, ale także upewnij się, że ten katalog jest chroniona w celu uniknięcia dane telemetryczne są odczytywane przez udostępnianie niepowołanym użytkownikom.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` Służy do utrwalanie danych. Ta lokalizacja nie można skonfigurować z poziomu katalogu konfiguracji i uprawnień dostępu do tego folderu są ograniczone do określonego użytkownika przy użyciu wymaganych poświadczeń. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) tutaj.)

###  <a name="net"></a>.Net

Domyślnie `ServerTelemetryChannel` używa bieżącego użytkownika lokalnej aplikacji danych folderu `%localAppData%\Microsoft\ApplicationInsights` lub folderu tymczasowego `%TMP%`. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj.)


Za pomocą pliku konfiguracji:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Za pomocą kodu:

- Usuń ServerTelemetryChannel z pliku konfiguracji
- Dodaj następujący fragment do konfiguracji:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Domyślnie `ServerTelemetryChannel` używa bieżącego użytkownika lokalnej aplikacji danych folderu `%localAppData%\Microsoft\ApplicationInsights` lub folderu tymczasowego `%TMP%`. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj.) W środowisku systemu Linux magazynu lokalnego zostanie wyłączone, o ile nie określono folderu magazynu.

Poniższy fragment kodu przedstawia sposób ustawiania `ServerTelemetryChannel.StorageFolder` w `ConfigureServices()`  metody usługi `Startup.cs` klasy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Zobacz [Konfiguracja niestandardowa AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) Aby uzyskać więcej informacji. )

### <a name="nodejs"></a>Node.js

Domyślnie `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` służy do utrwalanie danych. Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) tutaj.)

Prefiks folderu `appInsights-node` można przesłonić, zmieniając wartość czasu wykonania zmienna statyczna `Sender.TEMPDIR_PREFIX` w [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak wysyłać dane do usługi Application Insights przy użyciu protokołu TLS 1.2?

Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do punktów końcowych usługi Application Insights, zdecydowanie zachęcamy klientów do skonfigurowania aplikacji użyj co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**, i branży szybko rozwijających się do porzucenia pomocy technicznej dla tych starszych protokołów. 

[Rady standardami bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) ustawił [terminu 30 czerwca 2018 r.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) wyłączyć starsze wersje protokołu TLS/SSL i uaktualniania, aby lepiej zabezpieczyć protokołów. Gdy starsze obsługuje jest już w Azure, jeśli aplikacja/klienci nie mogą komunikować się za pośrednictwem co najmniej protokołu TLS 1.2, nie będzie mogła wysyłać dane do usługi Application Insights. Podejście, które należy wykonać w celu testowania i sprawdzania poprawności Obsługa protokołu TLS Twojej aplikacji różnią się w zależności od tego, czy system operacyjny/platformy, a także języka/platformy, używanych przez aplikację.

Firma Microsoft nie zaleca się jawne ustawienie bezwzględnie konieczne, ponieważ może to spowodować awarię funkcji zabezpieczenia na poziomie platformy, które pozwalają na automatyczne wykrywanie i korzystać z nowsze protokoły bezpieczniejsze staną się one aplikację, aby używać tylko protokołu TLS 1.2, chyba że dostępne, takich jak TLS 1.3. Firma Microsoft zaleca wykonanie dokładnego audyt kodu aplikacji pod kątem hardcoding określonych wersji protokołu TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Wskazówki dotyczące określonego języka/platformy

|Języka/platformy | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
| Azure App Services  | Obsługiwane, może być wymagana konfiguracja. | Obsługa zostało zapowiedziane w kwietniu 2018. Przeczytaj ogłoszenie dla [szczegóły konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikacje funkcji platformy Azure | Obsługiwane, może być wymagana konfiguracja. | Obsługa zostało zapowiedziane w kwietniu 2018. Przeczytaj ogłoszenie dla [szczegóły konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Obsługiwane, konfiguracja jest zależna od wersji. | Informacje o konfiguracji szczegółowe dla platformy .NET 4.7 i wcześniejsze wersje można znaleźć [w instrukcjach](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor stanu | Obsługiwane, wymagana jest Konfiguracja | Monitor stanu opiera się na [Konfiguracja systemu operacyjnego](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [konfiguracja .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) do obsługi protokołu TLS 1.2.
|Node.js |  Obsługiwane w v10.5.0, może być wymagana konfiguracja. | Użyj [oficjalnej dokumentacji środowiska Node.js protokołów TLS/SSL](https://nodejs.org/api/tls.html) dla żadnej konfiguracji określonej aplikacji. |
|Java | Obsługiwane, zestawu JDK obsługę protokołu TLS 1.2 został dodany w [zestaw JDK 6 aktualizacja 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) i [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Używa JDK 8 [protokołu TLS 1.2, domyślnie](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Dystrybucje systemu Linux, zwykle zależą od [OpenSSL](https://www.openssl.org) obsługę protokołu TLS 1.2.  | Sprawdź [dziennika zmian OpenSSL](https://www.openssl.org/news/changelog.html) aby upewnić się, używana wersja biblioteki openssl jest obsługiwana.|
| Windows 8.0 10 | Obsługiwane i domyślnie włączona. | Aby upewnić się, że nadal używasz [domyślne ustawienia](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| System Windows Server 2012 2016 | Obsługiwane i domyślnie włączona. | Aby upewnić się, że nadal używasz [ustawienia domyślne](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie jest włączony domyślnie. | Zobacz [zabezpieczeń TLS (Transport Layer), ustawień rejestru](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) strony, aby uzyskać szczegółowe informacje o sposobie włączania.  |
| Windows Server 2008 SP2 | Obsługa protokołu TLS 1.2 wymaga aktualizacji. | Zobacz [aktualizacji, aby dodać obsługę protokołu TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) w systemie Windows Server 2008 z dodatkiem SP2. |
|Windows Vista | Nieobsługiwane. | ND

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Upewnij się, jakie wersją biblioteki OpenSSL dystrybucji systemu Linux jest uruchomiona.

Aby sprawdzić, jakie wersją biblioteki OpenSSL zainstalowaniu, otwórz terminal i uruchom:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Uruchom test transakcji protokołu TLS 1.2 w systemie Linux

Aby uruchomić podstawowe test wstępny, aby zobaczyć, jeśli w systemie Linux mogą komunikować się za pośrednictwem protokołu TLS 1.2. Otwórz terminal i uruchom:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dane osobowe przechowywane w usłudze Application Insights

Nasze [usługi Application Insights dane osobowe artykułu](../../azure-monitor/platform/personal-data-mgmt.md) ten problem, szczegółowe.

#### <a name="can-my-users-turn-off-application-insights"></a>Moi użytkownicy wyłączyć usługi Application Insights?
Nie bezpośrednio. Firma Microsoft nie oferuje przełącznik, który użytkownicy mogą działać wyłączenia usługi Application Insights.

Jednakże można zaimplementować taka funkcja w aplikacji. Wszystkie zestawy SDK obejmują ustawienia interfejsu API, która wyłącza zbieranie danych telemetrycznych. 

## <a name="data-sent-by-application-insights"></a>Dane wysyłane przez usługę Application Insights
Zestawy SDK różnią się między platformami, a jest wiele składników, które można zainstalować. (Zobacz [Application Insights — omówienie][start].) Każdy składnik wysyła różnych danych.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasy danych przesyłanych w różnych scenariuszach

| Twoja Akcja | Klasy danych zebranych (zobacz następną tabelę) |
| --- | --- |
| [Dodaj zestaw SDK usługi Application Insights do projektu sieci web platformy .NET][greenbrown] |ServerContext<br/>Wywnioskowane<br/>Liczniki wydajności<br/>Żądania<br/>**Wyjątki**<br/>Sesja<br/>użytkownicy |
| [Zainstaluj Monitor stanu na serwerze IIS][redfield] |Zależności<br/>ServerContext<br/>Wywnioskowane<br/>Liczniki wydajności |
| [Dodaj zestaw SDK usługi Application Insights do aplikacji sieci web Java][java] |ServerContext<br/>Wywnioskowane<br/>Żądanie<br/>Sesja<br/>użytkownicy |
| [Dodaj zestaw SDK JavaScript do strony sieci web][client] |ClientContext <br/>Wywnioskowane<br/>Strona<br/>ClientPerf<br/>Ajax |
| [Zdefiniuj właściwości domyślne][apiproperties] |**Właściwości** na wszystkich standardowych i niestandardowych zdarzeń |
| [Wywołanie TrackMetric][api] |Wartości liczbowe<br/>**Właściwości** |
| [Śledzenie wywołań *][api] |Nazwa zdarzenia<br/>**Właściwości** |
| [Metoda TrackException wywołania][api] |**Wyjątki**<br/>Zrzut stosu<br/>**Właściwości** |
| Zestaw SDK nie może zbierać dane. Na przykład: <br/> — nie ma dostępu do liczników wydajności<br/> — wyjątek w inicjatora telemetrii |Zestaw SDK diagnostyki |

Aby uzyskać [zestawy SDK dla innych platform][platforms], zobacz swoje dokumenty.

#### <a name="the-classes-of-collected-data"></a>Klasy zebranych danych

| Klasa zebranych danych | Zawiera (nie stanowi wyczerpującej listy) |
| --- | --- |
| **Właściwości** |**Wszystkie dane — ustalany na podstawie kodu** |
| DeviceContext |Identyfikator adresu IP, ustawienia regionalne, model urządzenia, sieć, typ sieci, nazwa producenta OEM, rozdzielczość ekranu i wystąpienia roli, nazwa roli, rodzaju urządzenia |
| ClientContext |System operacyjny, ustawienia regionalne, języka, sieć, rozdzielczość okna |
| Sesja |Identyfikator sesji |
| ServerContext |Nazwa maszyny, ustawień regionalnych systemu operacyjnego, urządzenie, sesja użytkownika, kontekst użytkownika, operacji |
| Wywnioskowane |Lokalizacja geograficzna z adresu IP, timestamp, systemu operacyjnego, przeglądarki |
| Metryki |Nazwa metryki i wartości |
| Zdarzenia |Nazwa zdarzenia i wartości |
| PageViews |Nazwa adresu URL i strony lub ekranu |
| Wydajności klienta |Nazwa adresu URL/strony, czas ładowania przez przeglądarkę |
| Ajax |Wywołania HTTP ze strony sieci web serwera |
| Żądania |Adres URL, czas trwania, kod odpowiedzi |
| Zależności |Typ (SQL, HTTP,...), parametry połączenia lub identyfikatora URI synchronizacji/async, czas trwania, Powodzenie, instrukcja SQL (za pomocą Monitora stanu) |
| **Wyjątki** |Typ, **komunikat**, stosy wywołań, źródło pliku i wierszu numer identyfikatora wątku |
| Awarie |Identyfikator procesu, identyfikator procesu nadrzędnego, identyfikator wątku awarii; poprawki aplikacji, identyfikator kompilacji.  Typ wyjątku, adres, reason; symbole zaciemnionego i rejestry, binarne początkowy i końcowy adres, nazwa pliku binarnego i ścieżki, typ procesora |
| Ślad |**Komunikat** i poziom ważności |
| Liczniki wydajności |Czas procesora, pamięci, żądań zakończonych, częstotliwość występowania wyjątków, prywatne bajty procesu, we/wy, czas trwania żądania długość kolejki żądań |
| Dostępność |Kod odpowiedzi testu sieci Web, czas trwania każdego kroku testu, nazwa testu, sygnatury czasowej, Powodzenie, czas odpowiedzi, lokalizacji testu |
| Zestaw SDK diagnostyki |Komunikat śledzenia i wyjątków |

Możesz [wyłączyć niektórych danych, edytując plik ApplicationInsights.config][config]

> [!NOTE]
> Klient IP jest używany w celu lokalizacji geograficznej, ale domyślnie nie jest już przechowywania danych adresów IP i zer są zapisywane w odpowiednim polu. Aby dowiedzieć się więcej na temat obsługi danych osobowych jest to zalecane [artykułu](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Jeśli potrzebujesz przechować adres IP możesz to zrobić za pomocą [inicjatora telemetrii](./../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="credits"></a>Napisy końcowe
Ten produkt zawiera dane GeoLite2 utworzone przez MaxMind dostępne [ https://www.maxmind.com ](https://www.maxmind.com).



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md

