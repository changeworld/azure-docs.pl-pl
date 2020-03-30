---
title: Przechowywanie i przechowywanie danych w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Oświadczenie dotyczące przechowywania i polityki prywatności
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275999"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Gromadzenie, przechowywanie i przechowywanie danych w usłudze Application Insights

Po zainstalowaniu pakietu Azure [Application Insights][start] SDK w aplikacji wysyła dane telemetryczne dotyczące aplikacji do chmury. Oczywiście odpowiedzialni deweloperzy chcą dokładnie wiedzieć, jakie dane są wysyłane, co dzieje się z danymi i jak mogą zachować nad nim kontrolę. W szczególności, czy dane poufne mogą być wysyłane, gdzie są przechowywane i jak bezpieczne są? 

Po pierwsze, krótka odpowiedź:

* Standardowe moduły telemetryczne, które są uruchamiane "po wyjęciu z pudełka" jest mało prawdopodobne, aby wysłać poufne dane do usługi. Dane telemetryczne dotyczą metryki obciążenia, wydajności i użycia, raporty wyjątków i inne dane diagnostyczne. Głównymi danymi użytkownika widocznymi w raportach diagnostycznych są adresy URL; ale aplikacja w żadnym wypadku nie powinna umieszczać poufnych danych w postaci zwykłego tekstu w adresie URL.
* Można napisać kod, który wysyła dodatkowe dane telemetryczne niestandardowe, aby pomóc w diagnostyce i monitorowania użycia. (Ta rozszerzalność jest świetną funkcją usługi Application Insights). Byłoby możliwe, przez pomyłkę, aby napisać ten kod tak, że zawiera dane osobowe i inne poufne dane. Jeśli aplikacja działa z takimi danymi, należy zastosować proces dokładnego przeglądu do całego kodu, który piszesz.
* Podczas tworzenia i testowania aplikacji można łatwo sprawdzić, co jest wysyłane przez sdk. Dane są wyświetlane w oknach wyjściowych debugowania IDE i przeglądarki. 
* Dane są przechowywane na serwerach [platformy Microsoft Azure](https://azure.com) w STANACH Zjednoczonych lub Europie. (Ale aplikacja może działać w dowolnym miejscu). Platforma Azure ma [silne procesy zabezpieczeń i spełnia szeroki zakres standardów zgodności.](https://azure.microsoft.com/support/trust-center/) Tylko Ty i Twój wyznaczony zespół macie dostęp do Twoich danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do niego tylko w określonych ograniczonych okolicznościach, o których wiadomo. Jest szyfrowany podczas transportu i w spoczynku.
*   Przejrzyj zebrane dane, ponieważ mogą to obejmować dane, które są dozwolone w niektórych okolicznościach, ale nie inne.  Dobrym przykładem jest nazwa urządzenia. Nazwa urządzenia z serwera nie ma wpływu na prywatność i jest przydatna, ale nazwa urządzenia z telefonu lub laptopa może mieć wpływ na prywatność i być mniej przydatna. SDK opracowany głównie do serwerów docelowych, będzie zbierać nazwę urządzenia domyślnie i może to być konieczne do nadania zarówno normalnych zdarzeń i wyjątków.

Dalsza część artykułu jest bardziej szczegółowo na temat tych odpowiedzi. Został zaprojektowany tak, aby był samodzielny, dzięki czemu można go pokazać kolegom, którzy nie są częścią twojego najbliższego zespołu.

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
[Usługa Azure Application Insights][start] to usługa świadczona przez firmę Microsoft, która pomaga zwiększyć wydajność i użyteczność aplikacji na żywo. Monitoruje aplikację przez cały czas, gdy jest uruchomiona, zarówno podczas testowania, jak i po opublikowaniu lub wdrożeniu. Usługa Application Insights tworzy wykresy i tabele, które pokazują, na przykład, o jakich porach dnia otrzymujesz większość użytkowników, jak elastyczna jest aplikacja i jak dobrze jest obsługiwana przez wszystkie usługi zewnętrzne, od których zależy. Jeśli występują awarie, awarie lub problemy z wydajnością, można szczegółowo przeszukiwać dane telemetryczne, aby zdiagnozować przyczynę. A usługa wyśle ci e-maile, jeśli nastąpią jakiekolwiek zmiany w dostępności i wydajności aplikacji.

Aby uzyskać tę funkcję, należy zainstalować SDK usługi Application Insights w aplikacji, która staje się częścią jego kodu. Gdy aplikacja jest uruchomiona, SDK monitoruje jego działanie i wysyła dane telemetryczne do usługi Application Insights. Jest to usługa w chmurze obsługiwana przez platformę [Microsoft Azure](https://azure.com). (Ale usługa Application Insights działa dla wszystkich aplikacji, nie tylko aplikacji, które są hostowane na platformie Azure.)

Usługa application insights przechowuje i analizuje dane telemetryczne. Aby wyświetlić analizę lub przeszukiwać przechowywane dane telemetryczne, zaloguj się do konta platformy Azure i otwórz zasób usługi Application Insights dla aplikacji. Można również udostępnić dostęp do danych innym członkom zespołu lub określonym subskrybentom platformy Azure.

Dane mogą być eksportowane z usługi Application Insights, na przykład do bazy danych lub do narzędzi zewnętrznych. Każdemu narzędziu można uzyskać specjalny klucz z usługi. Klucz można odwołać w razie potrzeby. 

Zestawy SDK usługi Application Insights są dostępne dla wielu typów aplikacji: usług sieci web hostowanych na własnych serwerach Java EE lub ASP.NET lub na platformie Azure; klientów internetowych - czyli kodu uruchomionego na stronie internetowej; aplikacje i usługi klasyczne; aplikacji na urządzenia, takich jak Windows Phone, iOS i Android. Wszystkie one wysyłają dane telemetryczne do tej samej usługi.

## <a name="what-data-does-it-collect"></a>Jakie dane gromadzi?
Istnieją trzy źródła danych:

* SDK, który można zintegrować z aplikacją [w fazie rozwoju](../../azure-monitor/app/asp-net.md) lub w czasie [wykonywania.](../../azure-monitor/app/monitor-performance-live-website-now.md) Istnieją różne skupy SDK dla różnych typów aplikacji. Istnieje również [SDK dla stron internetowych,](../../azure-monitor/app/javascript.md)który ładuje się do przeglądarki użytkownika końcowego wraz ze stroną.
  
  * Każdy moduł SDK ma wiele [modułów,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)które używają różnych technik do zbierania różnych typów danych telemetrycznych.
  * Jeśli zainstalujesz SDK w rozwoju, można użyć jego interfejsu API do wysyłania własnych danych telemetrycznych, oprócz standardowych modułów. Ta niestandardowa telemetria może zawierać dowolne dane, które chcesz wysłać.
* W niektórych serwerach sieci web istnieją również agenci, którzy działają obok aplikacji i wysyłają dane telemetryczne dotyczące procesora CPU, pamięci i obłożenia sieci. Na przykład maszyny wirtualne platformy Azure, hosty platformy Docker i [serwery Java EE](../../azure-monitor/app/java-agent.md) mogą mieć takich agentów.
* Testy dostępności to [procesy](../../azure-monitor/app/monitor-web-app-availability.md) uruchamiane przez firmę Microsoft, które wysyłają żądania do aplikacji sieci web w regularnych odstępach czasu. Wyniki są wysyłane do usługi Usługi Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jakie rodzaje danych są zbierane?
Główne kategorie to:

* [Dane telemetryczne serwera sieci Web](../../azure-monitor/app/asp-net.md) — żądania HTTP.  Uri, czas przetwarzania żądania, kod odpowiedzi, adres IP klienta. `Session id`.
* [Strony sieci Web](../../azure-monitor/app/javascript.md) — liczy się strona, użytkownik i sesja. Czasy ładowania strony. Wyjątki. Ajax wzywa.
* Liczniki wydajności - pamięć, procesor, we/wy, obłożenie sieci.
* Kontekst klienta i serwera — system operacyjny, ustawienia regionalne, typ urządzenia, przeglądarka, rozdzielczość ekranu.
* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md) i awarie - `build id` **zrzuty stosu**, typ procesora. 
* [Zależności](../../azure-monitor/app/asp-net-dependencies.md) — wywołania usług zewnętrznych, takich jak REST, SQL, AJAX. Identyfikator URI lub parametry połączenia, czas trwania, sukces, polecenie.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) - czas trwania testu i kroki, odpowiedzi.
* [Śledź dzienniki](../../azure-monitor/app/asp-net-trace-logs.md) i [niestandardowe dane telemetryczne,](../../azure-monitor/app/api-custom-events-metrics.md) - **które kodujesz do dzienników lub danych telemetrycznych.**

[Więcej szczegółów](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak mogę sprawdzić, co jest zbierane?
Jeśli tworzysz aplikację przy użyciu programu Visual Studio, uruchom aplikację w trybie debugowania (F5). Dane telemetryczne są wyświetlane w oknie Dane wyjściowe. Stamtąd można go skopiować i sformatować jako JSON w celu łatwej kontroli. 

![](./media/data-retention-privacy/06-vs.png)

Istnieje również bardziej czytelny widok w oknie Diagnostyka.

W przypadku stron internetowych otwórz okno debugowania przeglądarki.

![Naciśnij klawisz F12 i otwórz kartę Sieć.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Czy mogę napisać kod, aby filtrować dane telemetryczne przed wysłaniem?
Byłoby to możliwe, pisząc [wtyczkę procesora telemetrii](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak długo przechowywane są dane?
Nieprzetworzone punkty danych (czyli elementy, które można wysyłać w Analytics i sprawdzać w wyszukiwarce) są przechowywane przez maksymalnie 730 dni. Można [wybrać czas przechowywania](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 lub 730 dni. Jeśli chcesz przechowywać dane dłużej niż 730 dni, możesz użyć [eksportu ciągłego,](../../azure-monitor/app/export-telemetry.md) aby skopiować je na konto magazynu podczas pozyskiwania danych. 

Dane przechowywane dłużej niż 90 dni będą naliczać dodatkowe opłaty. Dowiedz się więcej o cenach usługi Application Insights na [stronie cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Zagregowane dane (czyli liczby, średnie i inne dane statystyczne widoczne w Eksploratorze metryk) są przechowywane z ziarnem 1 minuty przez 90 dni.

[Migawki debugowania](../../azure-monitor/app/snapshot-debugger.md) są przechowywane przez 15 dni. Ta zasada przechowywania jest ustawiana na podstawie dla aplikacji. Jeśli chcesz zwiększyć tę wartość, możesz zażądać zwiększenia, otwierając przypadek pomocy technicznej w witrynie Azure portal.

## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
Dane są widoczne dla Ciebie, a jeśli masz konto w organizacji, członków zespołu. 

Może być eksportowany przez Ciebie i Członków Twojego zespołu i może zostać skopiowany do innych lokalizacji i przekazany innym osobom.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Co firma Microsoft robi z informacjami wysyłana przez aplikację do usługi Application Insights?
Firma Microsoft korzysta z tych danych wyłącznie w celu świadczenia usługi.

## <a name="where-is-the-data-held"></a>Gdzie przechowywane są dane?
* Lokalizację można wybrać podczas tworzenia nowego zasobu usługi Application Insights. Dowiedz się więcej o dostępności usługi Application Insights dla regionu [tutaj](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Czy to oznacza, że moja aplikacja musi być hostowana w USA, Europie lub Azji Południowo-Wschodniej?
* Nie. Aplikacja może działać w dowolnym miejscu, we własnych hostach lokalnych lub w chmurze.

## <a name="how-secure-is-my-data"></a>Jak bezpieczne są moje dane?
Usługa Application Insights to usługa azure. Zasady zabezpieczeń są opisane w [dokumencie white paper dotyczący zabezpieczeń, prywatności i zgodności platformy Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

Dane są przechowywane na serwerach platformy Microsoft Azure. W przypadku kont w witrynie Azure portal ograniczenia dotyczące kont są opisane w [dokumencie Zabezpieczenia, Prywatność i Zgodność platformy Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

Dostęp pracowników firmy Microsoft do danych użytkownika jest ograniczony. Uzyskujemy dostęp do Twoich danych tylko za Twoją zgodą i jeśli jest to konieczne do obsługi korzystania z usługi Application Insights. 

Dane zbiorcze we wszystkich aplikacjach naszych klientów (takie jak szybkość transmisji danych i średni rozmiar śladów) są używane do ulepszania usługi Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Czy dane telemetryczne innej osoby mogą zakłócać działanie danych usługi Application Insights?
Mogą wysyłać dodatkowe dane telemetryczne na twoje konto za pomocą klucza instrumentacji, który można znaleźć w kodzie stron internetowych. Z wystarczającą ilością dodatkowych danych metryki nie będą poprawnie reprezentować wydajność i użycie aplikacji.

Jeśli udostępniasz kod innym projektom, pamiętaj, aby usunąć klucz instrumentacji.

## <a name="is-the-data-encrypted"></a>Czy dane są szyfrowane?
Wszystkie dane są szyfrowane w spoczynku i w miarę przenoszenia się między centrami danych.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Czy dane są szyfrowane podczas przesyłania z mojej aplikacji do serwerów usługi Application Insights?
Tak, używamy https do wysyłania danych do portalu z prawie wszystkich zestawów SDK, w tym serwerów internetowych, urządzeń i stron internetowych HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Czy SDK tworzy tymczasową pamięć lokalną?

Tak, niektóre kanały telemetryczne będą zachowywać dane lokalnie, jeśli nie można osiągnąć punktu końcowego. Zapoznaj się poniżej, aby zobaczyć, które struktury i kanały telemetryczne są zagrożone.

Kanały telemetryczne korzystające z magazynu lokalnego tworzą pliki tymczasowe w katalogach TEMP lub APPDATA, które są ograniczone do określonego konta z uruchomieniem aplikacji. Może się to zdarzyć, gdy punkt końcowy był tymczasowo niedostępny lub nagrałeś limit ograniczania przepustowości. Po rozwiązaniu tego problemu kanał telemetryczny wznowi wysyłanie wszystkich nowych i utrwalonych danych.

Te utrwalone dane nie są szyfrowane lokalnie. Jeśli jest to problem, przejrzyj dane i ogranicz gromadzenie danych prywatnych. (Aby uzyskać więcej informacji, zobacz [Jak wyeksportować i usunąć prywatne dane).](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data)

Jeśli klient musi skonfigurować ten katalog z określonymi wymaganiami dotyczącymi zabezpieczeń, można go skonfigurować według struktury. Upewnij się, że proces uruchamiania aplikacji ma dostęp do zapisu do tego katalogu, ale upewnij się, że ten katalog jest chroniony, aby uniknąć odczytu danych telemetrycznych przez niezamierzonych użytkowników.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`służy do utrwalania danych. Ta lokalizacja nie jest konfigurowana z katalogu konfiguracyjnego, a uprawnienia dostępu do tego folderu są ograniczone do określonego użytkownika z wymaganymi poświadczeniami. (Aby uzyskać więcej informacji, zobacz [implementację](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Domyślnie `ServerTelemetryChannel` używa folderu danych lub folderu `%localAppData%\Microsoft\ApplicationInsights` `%TMP%`temp bieżącego użytkownika. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj.)


Za pomocą pliku konfiguracyjnego:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Poprzez kod:

- Usuń servertelemetryChannel z pliku konfiguracyjnego
- Dodaj ten fragment kodu do konfiguracji:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore (NetCore)

Domyślnie `ServerTelemetryChannel` używa folderu danych lub folderu `%localAppData%\Microsoft\ApplicationInsights` `%TMP%`temp bieżącego użytkownika. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj.) W środowisku systemu Linux magazyn lokalny zostanie wyłączony, chyba że określono folder magazynu.

Poniższy fragment kodu pokazuje, jak `ServerTelemetryChannel.StorageFolder` ustawić `ConfigureServices()` w `Startup.cs` metodzie klasy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Aby uzyskać więcej informacji, zobacz [AspNetCore Konfiguracja niestandardowa](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Domyślnie `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` jest używany do utrwalania danych. Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz [implementacji](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) tutaj.)

Prefiks `appInsights-node` folderu można zastąpić, zmieniając wartość środowiska uruchomieniowego zmiennej `Sender.TEMPDIR_PREFIX` statycznej znajdującej się w pliku [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (przeglądarka)

[Html5 Session Storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) służy do utrwalania danych. Stosowane są dwa oddzielne `AI_buffer` `AI_sent_buffer`bufory: i . Dane telemetryczne, które są wsadowe `AI_buffer`i oczekują na wysłanie, są przechowywane w pliku . Dane telemetryczne, które `AI_sent_buffer` właśnie zostały wysłane jest umieszczany w dopóki serwer pozyskiwania odpowiada, że został pomyślnie odebrany. Po pomyślnym odebraniu danych telemetrycznych jest usuwany ze wszystkich buforów. W przypadku błędów przejściowych (na przykład użytkownik traci łączność `AI_buffer` sieciową), dane telemetryczne pozostają w dopóki nie zostanie pomyślnie odebrane lub serwer pozyskiwania odpowiada, że dane telemetryczne są nieprawidłowe (na przykład zły schemat lub zbyt stary).

Bufory telemetrii można [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) `false`wyłączyć, ustawiając na . Gdy magazyn sesji jest wyłączony, tablica lokalna jest używana jako magazyn trwały. Ponieważ zestaw JavaScript SDK działa na urządzeniu klienckim, użytkownik ma dostęp do tej lokalizacji magazynu za pośrednictwem narzędzi programistycznych przeglądarki.

### <a name="opencensus-python"></a>OpenCensus Python

Domyślnie OpenCensus Python SDK używa `%username%/.opencensus/.azure/`bieżącego folderu użytkownika . Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz [implementacji](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) tutaj.) Folder z utrwalonych danych zostanie nazwany po pliku Python, który wygenerował dane telemetryczne.

Lokalizację pliku magazynu można zmienić, przekazując `storage_path` parametr w konstruktorze używanego eksportera.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak wysłać dane do usługi Application Insights przy użyciu protokołu TLS 1.2?

Aby ubezpieczyć bezpieczeństwo danych przesyłanych do punktów końcowych usługi Application Insights, zdecydowanie zachęcamy klientów do skonfigurowania aplikacji do używania co najmniej zabezpieczeń warstwy transportu (TLS) 1.2. Starsze wersje TLS/Secure Sockets Layer (SSL) okazały się podatne na ataki i chociaż nadal działają, aby umożliwić wsteczną kompatybilność, nie są **zalecane,** a branża szybko rezygnuje z obsługi tych starszych protokołów. 

[Rada Standardów Bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) wyznaczyła [termin 30 czerwca 2018 r.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) na wyłączenie starszych wersji protokołu TLS/SSL i uaktualnienie do bezpieczniejszych protokołów. Gdy platforma Azure porzuci starszą obsługę, jeśli aplikacja/klienci nie mogą komunikować się za pomocą co najmniej protokołu TLS 1.2, nie będzie można wysyłać danych do usługi Application Insights. Podejście, które można podjąć, aby przetestować i zweryfikować obsługę TLS aplikacji będzie się różnić w zależności od systemu operacyjnego/platformy, a także języka/struktury używanej przez aplikację.

Nie zaleca się jawnego ustawiania aplikacji tak, aby używała tylko protokołu TLS 1.2, chyba że jest to konieczne, ponieważ może to przerwać funkcje zabezpieczeń na poziomie platformy, które umożliwiają automatyczne wykrywanie i korzystanie z nowszych, bezpieczniejszych protokołów, gdy tylko staną się dostępne, takich jak TLS 1.3. Firma Microsoft zaleca przeprowadzenie szczegółowego audytu kodu aplikacji, aby sprawdzić, czy nie ma twardego kodowania określonych wersji TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Wytyczne dotyczące platformy/języka

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
| Azure App Services  | Obsługiwana może być konfiguracja. | Wsparcie zostało ogłoszone w kwietniu 2018 roku. Przeczytaj anons, aby [uzyskać szczegółowe informacje o konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikacje funkcji platformy Azure | Obsługiwana może być konfiguracja. | Wsparcie zostało ogłoszone w kwietniu 2018 roku. Przeczytaj anons, aby [uzyskać szczegółowe informacje o konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Obsługiwana konfiguracja zależy od wersji. | Szczegółowe informacje o konfiguracji dla platformy .NET 4.7 i wcześniejszych wersji można znaleźć w [tych instrukcjach](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor stanu | Obsługiwane, wymagana konfiguracja | Monitor stanu opiera się na [konfiguracji systemu operacyjnego](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET do](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) obsługi protokołu TLS 1.2.
|Node.js |  Obsługiwane w wersji 10.5.0 może być wymagana konfiguracja. | Użyj [oficjalnej dokumentacji Node.js TLS/SSL](https://nodejs.org/api/tls.html) dla dowolnej konfiguracji specyficznej dla aplikacji. |
|Java | Obsługa JDK dla protokołu TLS 1.2 została dodana w [aktualizacjach JDK 6 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) i [JDK 7.](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html) | JDK 8 [domyślnie używa protokołu TLS 1.2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Dystrybucje Linuksa zwykle polegają na [OpenSSL](https://www.openssl.org) dla obsługi TLS 1.2.  | Sprawdź [OpenSSL Changelog,](https://www.openssl.org/news/changelog.html) aby potwierdzić, że twoja wersja OpenSSL jest obsługiwana.|
| Windows 8.0 - 10 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatku SP1 i windows server 2008 R2 z dodatku SP1 | Obsługiwane, ale domyślnie nie włączone. | Szczegółowe informacje na temat włączania można znaleźć na stronie [ustawień rejestru TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2008 SP2 | Obsługa protokołu TLS 1.2 wymaga aktualizacji. | Zobacz [Aktualizacja, aby dodać obsługę protokołu TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) w systemie Windows Server 2008 z dodatkiem SP2. |
|Windows Vista | Nieobsługiwane. | Nie dotyczy

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Sprawdź, jaka wersja OpenSSL jest uruchomiona w Twojej dystrybucji Linuksa

Aby sprawdzić, jaką wersję openssl masz zainstalowaną, otwórz terminal i uruchom:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Uruchamianie testowej transakcji TLS 1.2 w systemie Linux

Aby uruchomić wstępny test, aby sprawdzić, czy system Linux może komunikować się za pomocą protokołu TLS 1.2., otwórz terminal i uruchom:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dane osobowe przechowywane w usłudze Application Insights

W [artykule na](../../azure-monitor/platform/personal-data-mgmt.md) temat danych osobowych usługi Application Insights szczegółowo omówiono ten problem.

#### <a name="can-my-users-turn-off-application-insights"></a>Czy moi użytkownicy mogą wyłączyć usługi Application Insights?
Nie bezpośrednio. Nie udostępniamy przełącznika, który użytkownicy mogą obsługiwać, aby wyłączyć usługa Application Insights.

Można jednak zaimplementować taką funkcję w aplikacji. Wszystkie sdks zawierają ustawienie interfejsu API, który wyłącza zbieranie danych telemetrycznych. 

## <a name="data-sent-by-application-insights"></a>Dane przesyłane przez aplikację Insights
Pakiety SDK różnią się między platformami i istnieje kilka składników, które można zainstalować. (Patrz Wgląd w [aplikacje — omówienie).][start] Każdy składnik wysyła różne dane.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasy danych wysyłanych w różnych scenariuszach

| Twoja akcja | Zebrane klasy danych (patrz następna tabela) |
| --- | --- |
| [Dodawanie sdk usługi Application Insights do projektu sieci web .NET][greenbrown] |Kontekst serwera<br/>Wywnioskować<br/>Liczniki wydajności<br/>Żądania<br/>**Wyjątki**<br/>Sesja<br/>użytkownicy |
| [Instalowanie Monitora stanu w serwisach IIS][redfield] |Zależności<br/>Kontekst serwera<br/>Wywnioskować<br/>Liczniki wydajności |
| [Dodawanie sdk usługi Application Insights do aplikacji sieci Web Java][java] |Kontekst serwera<br/>Wywnioskować<br/>Żądanie<br/>Sesja<br/>użytkownicy |
| [Dodawanie kodu JavaScript SDK do strony sieci Web][client] |KlientContext <br/>Wywnioskować<br/>Strona<br/>ClientPerf (KlientPerf)<br/>AJAX |
| [Definiowanie właściwości domyślnych][apiproperties] |**Właściwości** wszystkich zdarzeń standardowych i niestandardowych |
| [Metryk połączeń][api] |Wartości liczbowe<br/>**Właściwości** |
| [Ścieżka połączeń*][api] |Nazwa wydarzenia<br/>**Właściwości** |
| [Wywołanie TrackException][api] |**Wyjątki**<br/>Zrzut stosu<br/>**Właściwości** |
| Zestaw SDK nie może zbierać danych. Przykład: <br/> - nie może uzyskać dostępu do liczników perf<br/> - wyjątek w inicjatorze telemetrii |Diagnostyka SDK |

W przypadku [sdk dla innych platform][platforms]zobacz ich dokumenty.

#### <a name="the-classes-of-collected-data"></a>Klasy zebranych danych

| Klasa zebranych danych | Zawiera (nie wyczerpującą listę) |
| --- | --- |
| **Właściwości** |**Wszelkie dane - określone przez kod** |
| DeviceContext (Tekst urządzenia) |`Id`, IP, Ustawienia regionalne, Model urządzenia, sieć, typ sieci, nazwa OEM, rozdzielczość ekranu, Wystąpienie roli, Nazwa roli, Typ urządzenia |
| KlientContext |System operacyjny, ustawienia regionalne, język, sieć, rozdzielczość okna |
| Sesja |`session id` |
| Kontekst serwera |Nazwa komputera, ustawienia regionalne, system operacyjny, urządzenie, sesja użytkownika, kontekst użytkownika, działanie |
| Wywnioskować |lokalizacja geograficzna z adresu IP, sygnatury czasowej, systemu operacyjnego, przeglądarki |
| Metryki |Nazwa i wartość metryki |
| Zdarzenia |Nazwa i wartość zdarzenia |
| Odsłon |Adres URL i nazwa strony lub nazwa ekranu |
| Klient perf |Nazwa adresu URL/strony, czas ładowania przeglądarki |
| AJAX |Wywołania HTTP ze strony sieci Web na serwer |
| Żądania |Adres URL, czas trwania, kod odpowiedzi |
| Zależności |Typ(SQL, HTTP, ...), ciąg połączenia lub identyfikator URI, synchronizacja/asynchrona, czas trwania, sukces, instrukcja SQL (z Monitorem stanu) |
| **Wyjątki** |Typ, **wiadomość**, stosy połączeń, plik źródłowy, numer wiersza,`thread id` |
| Ulega awarii |`Process id`, `parent process id`, `crash thread id`; poprawka aplikacji, `id`, build;  typ wyjątku, adres, powód; zaciemnione symbole i rejestry, binarne adresy początkowe i końcowe, nazwa i ścieżka binarna, typ procesora |
| Ślad |**Poziom wiadomości** i ważności |
| Liczniki wydajności |Czas procesora, dostępna pamięć, szybkość żądania, częstotliwość wyjątków, przetwarzanie bajtów prywatnych, szybkość we/wy, czas trwania żądania, długość kolejki żądań |
| Dostępność |Kod odpowiedzi testu sieci Web, czas trwania każdego kroku testu, nazwa testu, sygnatura czasowa, powodzenie, czas odpowiedzi, lokalizacja testu |
| Diagnostyka SDK |Śledzenie komunikatu lub wyjątku |

Niektóre dane można [wyłączyć, edytując plik ApplicationInsights.config][config]

> [!NOTE]
> Adres IP klienta jest używany do wywnioskowania lokalizacji geograficznej, ale domyślnie dane IP nie są już przechowywane, a wszystkie zera są zapisywane w skojarzonym polu. Aby dowiedzieć się więcej na temat przetwarzania danych osobowych, zalecamy ten [artykuł](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Jeśli chcesz przechowywać dane adresów IP, nasz [artykuł zbierania adresów IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) poprowadzi Cię przez twoje opcje.

## <a name="credits"></a>Środki
Ten produkt zawiera dane GeoLite2 utworzone przez [https://www.maxmind.com](https://www.maxmind.com)MaxMind, dostępne od .



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
