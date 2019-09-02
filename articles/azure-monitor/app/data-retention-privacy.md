---
title: Przechowywanie i przechowywanie danych w usłudze Azure Application Insights | Microsoft Docs
description: Zasady przechowywania i zasad zachowania poufności informacji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mbullwin
ms.openlocfilehash: df441a55ef4a9a40fe4defcabca5f667eeddbf29
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207294"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights

Po zainstalowaniu zestawu [Azure Application Insights][start] SDK w aplikacji wysyła ona dane telemetryczne dotyczące aplikacji do chmury. W naturalny sposób zainteresowani deweloperzy chcą dokładnie wiedzieć, jakie dane są wysyłane, co się dzieje z danymi i jak mogą one zachować kontrolę nad nimi. W szczególności mogą być wysyłane poufne dane, gdzie są przechowywane i jak są bezpieczne? 

Najpierw krótka odpowiedź:

* Standardowe moduły telemetrii, które działają "poza Box", prawdopodobnie nie będą wysyłać poufnych danych do usługi. Dane telemetryczne są powiązane z metrykami obciążenia, wydajności i użycia, raportami o wyjątkach i innymi danymi diagnostycznymi. Główne dane użytkownika widoczne w raportach diagnostycznych są adresami URL; Jednak aplikacja nie powinna w żadnym przypadku umieszczać poufnych danych w postaci zwykłego tekstu w adresie URL.
* Można napisać kod, który wysyła dodatkowe niestandardowe dane telemetryczne, aby ułatwić diagnostykę i użycie monitorowania. (To rozszerzalność to świetna funkcja Application Insights.) Można to zrobić przez pomyłkę, aby napisać ten kod w taki sposób, aby zawierał dane osobowe i inne poufne. Jeśli aplikacja współpracuje z takimi danymi, należy zastosować dokładne procesy przeglądu do całego kodu, który napiszesz.
* Podczas tworzenia i testowania aplikacji można łatwo sprawdzić, co jest wysyłane przez zestaw SDK. Dane są wyświetlane w oknach wyjściowych debugowania środowiska IDE i przeglądarki. 
* Dane są przechowywane na serwerach [Microsoft Azure](https://azure.com) w Stanach Zjednoczonych i Europie. (Ale aplikacja może działać w dowolnym miejscu). Platforma Azure ma [silne procesy zabezpieczeń i spełnia szeroki zakres standardów zgodności](https://azure.microsoft.com/support/trust-center/). Tylko ty i Twój Wyznaczeni zespół mają dostęp do danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do niego tylko w określonych ograniczonych okolicznościach z Twoją wiedzą. Jest on szyfrowany podczas przesyłania i przechowywania.

W dalszej części tego artykułu szczegółowo opracowano te odpowiedzi. Jest ona zaprojektowana jako samodzielna, aby można było ją pokazać współpracownikom, którzy nie są częścią swojego bezpośredniego zespołu.

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
[Azure Application Insights][start] to usługa świadczona przez firmę Microsoft, która pomaga w ulepszaniu wydajności i użyteczności działającej aplikacji. Monitoruje aplikację przez cały czas jej działania, zarówno podczas testowania, jak i po jej opublikowaniu lub wdrożeniu. Application Insights tworzy wykresy i tabele, które pokazują Ci, na przykład pory dnia, w jaki każdy dzień otrzymuje większość użytkowników, jak reaguje na aplikację i jak jest obsługiwany przez wszystkie zewnętrzne usługi, od których zależy. W przypadku awarii, błędów lub problemów z wydajnością można wyszukać szczegółowe dane telemetryczne, aby zdiagnozować przyczynę. A usługa wyśle Ci wiadomości e-mail w przypadku zmiany dostępności i wydajności aplikacji.

Aby można było skorzystać z tej funkcji, należy zainstalować zestaw SDK Application Insights w aplikacji, który będzie częścią kodu. Gdy aplikacja jest uruchomiona, zestaw SDK monitoruje swoją operację i wysyła dane telemetryczne do usługi Application Insights. Jest to usługa w chmurze hostowana przez [Microsoft Azure](https://azure.com). (Ale Application Insights działa dla wszystkich aplikacji, a nie tylko tych, które są hostowane na platformie Azure).

Usługa Application Insights przechowuje i analizuje dane telemetryczne. Aby wyświetlić analizę lub przeszukać dane telemetryczne przechowywane, zaloguj się do konta platformy Azure i Otwórz zasób Application Insights aplikacji. Dostęp do danych można również udostępnić innym członkom zespołu lub określonym subskrybentom platformy Azure.

Dane można eksportować z usługi Application Insights, na przykład do bazy danych lub narzędzi zewnętrznych. Każdemu narzędziu można udostępnić klucz specjalny uzyskany od usługi. Klucz można odwołać w razie potrzeby. 

Zestawy SDK Application Insights są dostępne dla różnych typów aplikacji: usług sieci Web hostowanych we własnych serwerach Java EE lub ASP.NET lub na platformie Azure; Klienci sieci Web — to oznacza kod uruchomiony na stronie sieci Web; aplikacje i usługi pulpitu; aplikacje urządzenia, takie jak Windows Phone, iOS i Android. Wszystkie dane telemetryczne są wysyłane do tej samej usługi.

## <a name="what-data-does-it-collect"></a>Jakie dane są zbierane?
### <a name="how-is-the-data-is-collected"></a>Jak są zbierane dane?
Istnieją trzy źródła danych:

* Zestaw SDK, który można zintegrować z aplikacją [w trakcie programowania](../../azure-monitor/app/asp-net.md) lub w [czasie wykonywania](../../azure-monitor/app/monitor-performance-live-website-now.md). Istnieją różne zestawy SDK dla różnych typów aplikacji. Istnieje również [zestaw SDK dla stron sieci Web](../../azure-monitor/app/javascript.md), który ładuje się do przeglądarki użytkownika końcowego wraz ze stroną.
  
  * Każdy zestaw SDK ma wiele [modułów](../../azure-monitor/app/configuration-with-applicationinsights-config.md), które używają różnych technik do zbierania różnych typów danych telemetrycznych.
  * Jeśli instalujesz zestaw SDK w programie Development, możesz użyć jego interfejsu API do wysyłania własnej telemetrii (oprócz modułów standardowych). Ta niestandardowa Telemetria może zawierać dowolne dane, które chcesz wysłać.
* Na niektórych serwerach sieci Web są również agenci, którzy uruchamiają się wraz z aplikacją i wysyłają dane telemetryczne dotyczące użycia procesora CPU, pamięci i sieci. Na przykład serwery Azure Virtual Machines, Docker Hosts i [Java EE](../../azure-monitor/app/java-agent.md) mogą mieć takich agentów.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) to procesy uruchamiane przez firmę Microsoft, które wysyłają żądania do aplikacji sieci Web w regularnych odstępach czasu. Wyniki są wysyłane do usługi Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jakie rodzaje danych są zbierane?
Główne kategorie to:

* Dane telemetryczne [serwera sieci Web](../../azure-monitor/app/asp-net.md) — żądania HTTP.  Identyfikator URI, czas trwania przetwarzania żądania, kod odpowiedzi, adres IP klienta. Identyfikator sesji.
* [Strony sieci Web](../../azure-monitor/app/javascript.md) — liczba stron, użytkowników i sesji. Czasy ładowania strony. Liczba wyjątków. Wywołania AJAX.
* Liczniki wydajności — pamięć, procesor CPU, we/wy, zajętość sieci.
* Kontekst klienta i serwera — system operacyjny, ustawienia regionalne, typ urządzenia, przeglądarka, rozdzielczość ekranu.
* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md) i awarie — **zrzuty stosu**, identyfikator kompilacji, typ procesora CPU. 
* [Zależności](../../azure-monitor/app/asp-net-dependencies.md) — wywołania usług zewnętrznych, takich jak REST, SQL i AJAX. Identyfikator URI lub parametry połączenia, czas trwania, powodzenie, polecenie.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) — czas trwania testu i kroków, odpowiedzi.
* [Dzienniki śledzenia](../../azure-monitor/app/asp-net-trace-logs.md) i [niestandardowa Telemetria](../../azure-monitor/app/api-custom-events-metrics.md) - —**wszystko, co jest używane w dziennikach lub danych**telemetrycznych.

[Więcej szczegółów](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak mogę sprawdzić, co jest zbierane?
Jeśli tworzysz aplikację przy użyciu programu Visual Studio, uruchom aplikację w trybie debugowania (F5). Dane telemetryczne pojawiają się w oknie danych wyjściowych. Z tego miejsca możesz skopiować go i sformatować jako kod JSON w celu zapewnienia łatwej inspekcji. 

![](./media/data-retention-privacy/06-vs.png)

W oknie Diagnostyka znajduje się również bardziej czytelny widok.

W przypadku stron sieci Web otwórz okno debugowanie przeglądarki.

![Naciśnij klawisz F12 i Otwórz kartę Sieć.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Czy mogę napisać kod, aby odfiltrować dane telemetryczne przed wysłaniem?
Jest to możliwe dzięki napisaniu [wtyczki procesora](../../azure-monitor/app/api-filtering-sampling.md)telemetrii.

## <a name="how-long-is-the-data-kept"></a>Jak długo są przechowywane dane?
Pierwotne punkty danych (czyli elementy, które można badać w analizie i inspekcji wyszukiwania) są przechowywane przez maksymalnie 730 dni. Można [wybrać okres przechowywania](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) równy 30, 60, 90, 120, 180, 270, 365, 550 lub 730. Jeśli chcesz przechowywać dane dłużej niż 730 dni, możesz użyć [eksportu ciągłego](../../azure-monitor/app/export-telemetry.md) , aby skopiować go do konta magazynu podczas pozyskiwania danych. 

Dane przechowywane dłużej niż 90 dni spowodują naliczenie opłat za dodawanie. Dowiedz się więcej o cenach Application Insights na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Zagregowane dane (czyli liczniki, średnie i inne dane statystyczne widoczne w Eksploratorze metryk) są zachowywane w ciągu 1 minuty przez 90 dni.

[Migawki debugowania](../../azure-monitor/app/snapshot-debugger.md) są przechowywane przez piętnaście dni. Te zasady przechowywania jest ustawiona na podstawie poszczególnych aplikacji. Jeśli potrzebujesz zwiększyć tę wartość, możesz poprosić o zwiększenie przez otwarcie zgłoszenia do pomocy technicznej w witrynie Azure portal.

## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
Dane są widoczne dla Ciebie i, jeśli masz konto organizacji, członków zespołu. 

Mogą być eksportowane przez Ciebie i członków zespołu i mogą być kopiowane do innych lokalizacji i przesyłane do innych osób.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Do czego służy firma Microsoft z informacjami wysyłanymi przez aplikację do Application Insights?
Firma Microsoft używa danych tylko w celu udostępnienia usługi.

## <a name="where-is-the-data-held"></a>Gdzie są przechowywane dane?
* Lokalizację można wybrać podczas tworzenia nowego zasobu Application Insights. Dowiedz się więcej o dostępności Application Insights [](https://azure.microsoft.com/global-infrastructure/services/?products=all)na region.

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Czy oznacza to, że moja aplikacja musi być hostowana w USA, Europie czy Azji Południowo-Wschodnia?
* Nie. Aplikacja może działać w dowolnym miejscu — na własnych hostach lokalnych lub w chmurze.

## <a name="how-secure-is-my-data"></a>Jak bezpieczne są moje dane?
Application Insights to usługa platformy Azure. Zasady zabezpieczeń są opisane w [oficjalny dokument dotyczący zabezpieczeń, ochrony prywatności i zgodności z platformą Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Dane są przechowywane na serwerach Microsoft Azure. W przypadku kont w witrynie Azure Portal ograniczenia konta są opisane w [dokumencie zabezpieczenia, prywatność i zgodność platformy Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Dostęp do danych przez personel firmy Microsoft jest ograniczony. Dostęp do danych odbywa się tylko przy użyciu uprawnień użytkownika, a jeśli jest to konieczne, aby obsługiwać korzystanie z Application Insights. 

Dane zagregowane dla wszystkich naszych aplikacji klientów (takie jak stawki danych i średni rozmiar śladów) są używane do ulepszania Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Czy dane telemetryczne innej osoby mogą przeszkadzać w danych Application Insights?
Mogą oni wysyłać dodatkowe dane telemetryczne do konta za pomocą klucza instrumentacji, który można znaleźć w kodzie stron sieci Web. Dzięki wystarczającej ilości dodatkowych danych metryki nie będą poprawnie przedstawiać wydajności i użycia aplikacji.

Jeśli współużytkujesz kod z innymi projektami, pamiętaj o usunięciu klucza Instrumentacji.

## <a name="is-the-data-encrypted"></a>Czy dane są szyfrowane?
Wszystkie dane są szyfrowane w czasie spoczynku i przenoszone między centrami danych.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Czy dane są szyfrowane podczas przesyłania z mojej aplikacji do Application Insights serwerów?
Tak, protokół HTTPS jest używany do wysyłania danych do portalu z niemal wszystkich zestawów SDK, w tym serwerów sieci Web, urządzeń i stron sieci Web HTTPS. Jedynym wyjątkiem są dane wysyłane z zwykłych stron sieci Web HTTP.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Czy zestaw SDK tworzy tymczasowy magazyn lokalny?

Tak, niektóre kanały telemetrii będą utrzymywać dane lokalnie, jeśli nie można nawiązać połączenia z punktem końcowym. Zapoznaj się z poniższymi tematami, aby zobaczyć, które platformy i kanały telemetrii mają oddziaływać.

Kanały telemetrii korzystające z lokalnego magazynu tworzenie plików tymczasowych w katalogach TEMP lub APPDATA, które są ograniczone do określonego konta, na którym działa aplikacja. Może się tak zdarzyć, gdy punkt końcowy był tymczasowo niedostępny lub osiągnięto limit ograniczania. Po rozwiązaniu tego problemu kanał telemetrii zostanie wznowiony, wysyłając wszystkie nowe i utrwalone dane.

Te utrwalone dane nie są szyfrowane lokalnie. Jeśli jest to problem, Przejrzyj dane i Ogranicz zbieranie danych prywatnych. (Aby uzyskać więcej informacji [, zobacz Jak eksportować i usuwać dane prywatne](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) ).

Jeśli klient musi skonfigurować ten katalog z określonymi wymaganiami dotyczącymi zabezpieczeń, można go skonfigurować na platformę. Upewnij się, że proces z uruchomioną aplikacją ma dostęp do zapisu w tym katalogu, ale upewnij się również, że ten katalog jest chroniony, aby uniknąć odczytania danych telemetrycznych przez niezamierzonych użytkowników.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`służy do utrwalania danych. Ta lokalizacja nie jest konfigurowalna z katalogu konfiguracji, a uprawnienia dostępu do tego folderu są ograniczone do określonego użytkownika z wymaganymi poświadczeniami. (Zobacz [](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) tutaj implementację).

###  <a name="net"></a>.Net

Domyślnie `ServerTelemetryChannel` używa folderu `%localAppData%\Microsoft\ApplicationInsights` danych lokalnej aplikacji lub folderu `%TMP%`tymczasowego bieżącego użytkownika. (Zobacz [](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj implementację).


Plik konfiguracji:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Za pośrednictwem kodu:

- Usuń ServerTelemetryChannel z pliku konfiguracji
- Dodaj następujący fragment kodu do konfiguracji:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Domyślnie `ServerTelemetryChannel` używa folderu `%localAppData%\Microsoft\ApplicationInsights` danych lokalnej aplikacji lub folderu `%TMP%`tymczasowego bieżącego użytkownika. (Zobacz [](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) tutaj implementację). W środowisku systemu Linux magazyn lokalny zostanie wyłączony, chyba że zostanie określony folder magazynu.

Poniższy fragment kodu pokazuje, `ServerTelemetryChannel.StorageFolder` `ConfigureServices()` jak ustawić w metodzie `Startup.cs` klasy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Zobacz [Konfiguracja niestandardowa AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) , aby uzyskać więcej informacji. )

### <a name="nodejs"></a>Node.js

Domyślnie `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` jest używany do utrwalania danych. Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz [](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) tutaj implementację).

Prefiks `appInsights-node` folderu można przesłonić, zmieniając wartość w czasie wykonywania zmiennej `Sender.TEMPDIR_PREFIX` statycznej znalezionej w elemencie [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak mogę wysyłać dane do Application Insights przy użyciu protokołu TLS 1,2?

Aby zapewnić bezpieczeństwo danych przesyłanych do Application Insightsych punktów końcowych, zdecydowanie zachęcamy klientów do konfigurowania aplikacji do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**, i branży szybko rozwijających się do porzucenia pomocy technicznej dla tych starszych protokołów. 

[Rady standardami bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) ustawił [terminu 30 czerwca 2018 r.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) wyłączyć starsze wersje protokołu TLS/SSL i uaktualniania, aby lepiej zabezpieczyć protokołów. Gdy platforma Azure pozostanie w starszej wersji, jeśli aplikacja/klienci nie mogą komunikować się za pomocą co najmniej protokołu TLS 1,2, nie będzie możliwe wysyłanie danych do Application Insights. Podejście wykonywane do testowania i weryfikowania obsługi protokołu TLS aplikacji będzie się różnić w zależności od systemu operacyjnego/platformy, a także od języka/platformy używanej przez aplikację.

Firma Microsoft nie zaleca jawnej konfiguracji aplikacji, aby używała protokołu TLS 1,2, chyba że jest to absolutnie konieczne, ponieważ może to spowodować przerwanie funkcji zabezpieczeń na poziomie platformy, które umożliwiają automatyczne wykrywanie i korzystanie z nowszych bezpieczniejszych protokołów w miarę ich używania dostępne, takie jak TLS 1,3. Zalecamy przeprowadzenie szczegółowej inspekcji kodu aplikacji w celu sprawdzenia zakodowana określonych wersji protokołu TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Wskazówki dotyczące platformy/języka

|Języka/platformy | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
| Azure App Services  | Obsługiwane, może być wymagana konfiguracja. | Obsługa została ogłoszona w kwietniu 2018. Zapoznaj się z ogłoszeniem, aby uzyskać [szczegółowe informacje o konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplikacje funkcji platformy Azure | Obsługiwane, może być wymagana konfiguracja. | Obsługa została ogłoszona w kwietniu 2018. Zapoznaj się z ogłoszeniem, aby uzyskać [szczegółowe informacje o konfiguracji](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Obsługiwane, konfiguracja zależy od wersji. | Aby uzyskać szczegółowe informacje o konfiguracji dla programu .NET 4,7 i wcześniejszych wersji, zobacz [te instrukcje](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor stanu | Obsługiwane, wymagana konfiguracja | Monitor stanu korzysta z[konfiguracji platformy .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)  +  [konfiguracji systemu operacyjnego](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)w celu zapewnienia obsługi protokołu TLS 1,2.
|Node.js |  Obsługiwane w programie v 10.5.0 może być wymagana konfiguracja. | Informacje o konfiguracji specyficzne dla aplikacji zawiera [Oficjalna dokumentacja protokołu TLS/SSL w programie Node. js](https://nodejs.org/api/tls.html) . |
|Java | Obsługiwane, JDK support for TLS 1,2 zostało dodane w [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) i [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 [domyślnie używa protokołu TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Dystrybucje systemu Linux, zwykle zależą od [OpenSSL](https://www.openssl.org) obsługę protokołu TLS 1.2.  | Sprawdź [dziennika zmian OpenSSL](https://www.openssl.org/news/changelog.html) aby upewnić się, używana wersja biblioteki openssl jest obsługiwana.|
| Windows 8.0 10 | Obsługiwane i domyślnie włączona. | Aby upewnić się, że nadal używasz [domyślne ustawienia](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| System Windows Server 2012 2016 | Obsługiwane i domyślnie włączona. | Aby upewnić się, że nadal używasz [ustawienia domyślne](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie jest włączony domyślnie. | Zobacz [zabezpieczeń TLS (Transport Layer), ustawień rejestru](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) strony, aby uzyskać szczegółowe informacje o sposobie włączania.  |
| Windows Server 2008 SP2 | Obsługa protokołu TLS 1.2 wymaga aktualizacji. | Zobacz [aktualizacji, aby dodać obsługę protokołu TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) w systemie Windows Server 2008 z dodatkiem SP2. |
|Windows Vista | Nieobsługiwane. | ND

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Sprawdź wersję OpenSSL, w której działa dystrybucja systemu Linux

Aby sprawdzić zainstalowaną wersję programu OpenSSL, Otwórz Terminal i uruchom polecenie:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Uruchamianie testowej transakcji TLS 1,2 w systemie Linux

Aby uruchomić podstawowy test wstępny, aby sprawdzić, czy system Linux może komunikować się za pośrednictwem protokołu TLS 1,2. Otwórz Terminal i uruchom:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dane osobowe przechowywane w Application Insights

Nasz [artykuł dotyczący danych osobowych w Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) zawiera szczegółowe omówienie tego problemu.

#### <a name="can-my-users-turn-off-application-insights"></a>Czy moi użytkownicy mogą wyłączyć Application Insights?
Nie bezpośrednio. Nie udostępniamy przełącznika, który użytkownicy mogą wykonywać, aby wyłączyć Application Insights.

Można jednak zaimplementować takie funkcje w aplikacji. Wszystkie zestawy SDK zawierają ustawienie interfejsu API, które powoduje wyłączenie zbierania danych telemetrycznych. 

## <a name="data-sent-by-application-insights"></a>Dane wysyłane przez Application Insights
Zestawy SDK różnią się między platformami i istnieje kilka składników, które można zainstalować. (Zobacz [Application Insights — przegląd][start]). Każdy składnik wysyła różne dane.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasy danych wysyłanych w różnych scenariuszach

| Twoja akcja | Zebrane klasy danych (zobacz następną tabelę) |
| --- | --- |
| [Dodawanie Application Insights SDK do projektu sieci Web platformy .NET][greenbrown] |ServerContext<br/>Wywnioskować<br/>Liczniki wydajności<br/>Żądania<br/>**Wyjątki**<br/>Sesja<br/>Użytkownicy |
| [Instalowanie monitor stanu w usługach IIS][redfield] |Zależności<br/>ServerContext<br/>Wywnioskować<br/>Liczniki wydajności |
| [Dodawanie Application Insights SDK do aplikacji sieci Web Java][java] |ServerContext<br/>Wywnioskować<br/>Żądanie<br/>Sesja<br/>Użytkownicy |
| [Dodaj zestaw SDK JavaScript do strony sieci Web][client] |ClientContext <br/>Wywnioskować<br/>Strona<br/>ClientPerf<br/>Ajax |
| [Definiowanie właściwości domyślnych][apiproperties] |**Właściwości** wszystkich zdarzeń standardowych i niestandardowych |
| [TrackMetric wywołań][api] |Wartości liczbowe<br/>**Aœciwoœci** |
| [Śledzenie wywołań *][api] |Nazwa zdarzenia<br/>**Aœciwoœci** |
| [Wywołanie metody Trackexception][api] |**Wyjątki**<br/>Zrzut stosu<br/>**Aœciwoœci** |
| Zestaw SDK nie może zbierać danych. Na przykład: <br/> -nie można uzyskać dostępu do liczników wydajności<br/> -wyjątek w inicjatorze telemetrii |Diagnostyka zestawu SDK |

W przypadku [zestawów SDK dla innych platform][platforms]Zobacz dokumenty.

#### <a name="the-classes-of-collected-data"></a>Klasy zebranych danych

| Klasa zebranych danych | Zawiera (nie jest to pełna lista) |
| --- | --- |
| **Aœciwoœci** |**Wszystkie dane — określone przez kod** |
| DeviceContext |Identyfikator, adres IP, ustawienia regionalne, model urządzenia, Sieć, typ sieci, nazwa OEM, rozdzielczość ekranu, wystąpienie roli, nazwa roli, typ urządzenia |
| ClientContext |System operacyjny, ustawienia regionalne, język, Sieć, rozdzielczość okna |
| Sesja |Identyfikator sesji |
| ServerContext |Nazwa komputera, ustawienia regionalne, system operacyjny, urządzenie, sesja użytkownika, kontekst użytkownika, operacja |
| Wywnioskować |Lokalizacja geograficzna z adresu IP, sygnatury czasowej, systemu operacyjnego, przeglądarki |
| Metryki |Nazwa i wartość metryki |
| Events |Nazwa i wartość zdarzenia |
| PageViews |Adres URL i nazwa strony lub nazwa ekranu |
| Wydajność klienta |Nazwa adresu URL/strony, czas ładowania przeglądarki |
| Ajax |Wywołania HTTP ze strony sieci Web na serwer |
| Żądania |Adres URL, czas trwania, kod odpowiedzi |
| Zależności |Typ (SQL, HTTP,...), parametry połączenia lub identyfikator URI, synchronizacja/Async, czas trwania, sukces, instrukcja SQL (z monitor stanu) |
| **Wyjątki** |Typ, **komunikat**, stosy wywołań, plik źródłowy i numer wiersza, identyfikator wątku |
| Stąp |Identyfikator procesu, identyfikator procesu nadrzędnego, identyfikator wątku awarii; Poprawka aplikacji, identyfikator, kompilacja;  Typ wyjątku, adres, Przyczyna; zasłonięte symbole i rejestry, binarne adresy startowe i końcowe, nazwa binarna i ścieżka, typ procesora |
| Ślad |**Komunikat** i poziom ważności |
| Liczniki wydajności |Czas procesora, dostępna pamięć, szybkość żądania, szybkość wyjątku, bajty prywatne procesu, szybkość operacji we/wy, czas żądania, Długość kolejki żądań |
| Dostępność |Kod odpowiedzi testu sieci Web, czas trwania każdego kroku testu, nazwa testu, sygnatura czasowa, sukces, czas odpowiedzi, lokalizacja testu |
| Diagnostyka zestawu SDK |Komunikat lub wyjątek śledzenia |

[Niektóre dane można wyłączyć, edytując plik ApplicationInsights. config][config]

> [!NOTE]
> Adres IP klienta jest używany do wywnioskowania lokalizacji geograficznej, ale domyślnie dane IP nie są już przechowywane i wszystkie zera są zapisywane w skojarzonym polu. Aby dowiedzieć się więcej na temat obsługi danych osobowych, zalecamy korzystanie z tego [artykułu](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Jeśli zachodzi potrzeba zapisania danych adresów IP w [artykule dotyczącym zbierania adresów IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) , przeprowadzisz Cię przez opcje.

## <a name="credits"></a>Napisy końcowe
Ten produkt zawiera dane GeoLite2 utworzone przez MaxMind, dostępne z [https://www.maxmind.com](https://www.maxmind.com).



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
