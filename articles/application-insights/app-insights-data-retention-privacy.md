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
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: mbullwin
ms.openlocfilehash: 0ee712b24478b52dfc5864e59e885e3b9dd6137b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294070"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights


Po zainstalowaniu [Azure Application Insights] [ start] zestawu SDK w aplikacji, wysyła dane telemetryczne dotyczące aplikacji w chmurze. Oczywiście odpowiedzialny deweloperzy zapoznać się dokładnie przesyłanych danych, co się dzieje z danymi i jak można zachować kontrolę nad go. W szczególności mógł zostać wysłany poufnych danych, gdzie są przechowywane i jak bezpieczne jest? 

Pierwszy, krótki odpowiedzi:

* Moduły standardowe telemetrii systemem "fabrycznej" prawdopodobnie nie wysyłać poufne dane do usługi. Dane telemetryczne dotyczy obciążenia, metryki wydajności i użycia wyjątek raportów i innych danych diagnostycznych. Adresy URL; są widoczne w raportach diagnostycznych danych głównym użytkownikiem ale aplikacji nie powinna w każdym przypadku poufne dane w postaci zwykłego tekstu w adresie URL.
* Można napisać kod, który wysyła dodatkowe telemetria niestandardowa, które zapewniają pomoc podczas diagnostyki i monitorowania użycia. (Rozszerzania jest doskonałym funkcji usługi Application Insights). Możliwe byłoby, przez pomyłkę, aby napisać ten kod, co zawierają one prywatnych i innych poufnych danych. Jeśli aplikacja działa z takich danych, należy zastosować procesy oceny tworzonego kodu.
* Podczas tworzenia i testowania aplikacji, jest proste sprawdzić, co jest wysyłany przez zestaw SDK. Dane są wyświetlane w oknie danych wyjściowych debugowania w IDE i przeglądarki. 
* Dane są przechowywane w [Microsoft Azure](http://azure.com) serwerów w USA i Europie. (Ale aplikację można uruchomić dowolnego miejsca). Platforma Azure ma [silne zabezpieczenie przetwarza i spełnia szeroką gamę standardów zgodności](https://azure.microsoft.com/support/trust-center/). Tylko dla Ciebie i Twojego zespołu wyznaczonych mieć dostęp do danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do jej tylko w określonych okolicznościach ograniczone z wiedzy użytkownika. Szyfrowane podczas przesyłania, ale nie na serwerach.

Te odpowiedzi dokładniejszego rosnącego dalszej części tego artykułu. Jako autonomiczna, zostało zaprojektowane tak, aby przedstawiono go do współpracowników, którzy nie należą do zespołu bezpośrednim.

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
[Azure Application Insights] [ start] jest usługa firmy Microsoft, która pomaga zwiększyć wydajność i użyteczność działającej aplikacji. Monitoruje aplikację cały czas, który jest uruchomiony podczas testowania i po jej wdrożeniu lub opublikowane. Usługa Application Insights tworzy wykresów i tabel, które zawierają, na przykład, jakich porach dnia otrzymasz większość użytkowników jest stopień reakcji aplikacji i jak zaspokaja zewnętrznych usług, których ona zależy. Jeśli występują awarie, awarii lub problemów z wydajnością, można przeszukiwać dane telemetryczne szczegółowo diagnozowanie przyczyn. I usługa otrzymasz wiadomości e-mail w przypadku zmiany w dostępności i wydajności aplikacji.

Aby uzyskać tę funkcję, należy zainstalować zestaw SDK usługi Application Insights w aplikacji, która staje się częścią kodu. Gdy aplikacja jest uruchomiona, zestawu SDK monitoruje jego działania i wysyłanie danych telemetrycznych do usługi Application Insights. Jest to usługa w chmurze hostowana przez [Microsoft Azure](http://azure.com). (Ale usługi Application Insights działa w przypadku dowolnej aplikacji, nie tylko te, które są hostowane na platformie Azure).

![Zestaw SDK w aplikacji wysyła dane telemetryczne z usługą Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Usługa Application Insights przechowuje i analizuje dane telemetryczne. Aby wyświetlić analizy lub wyszukiwania za pomocą przechowywane dane telemetryczne, zaloguj się do konta platformy Azure i otworzyć zasobu usługi Application Insights dla aplikacji. Mogą również współużytkować dostęp do danych z innych członków zespołu lub z określonej subskrybenci platformy Azure.

Może być danymi wyeksportowanymi z usługi Application Insights, na przykład z bazą danych lub narzędzi zewnętrznych. Zapewnia narzędzia specjalny klucz, który można uzyskać z usługi. Klucz można odwołać, jeśli jest to konieczne. 

Zestawy SDK Insights aplikacji są dostępne w różnych typów aplikacji: sieci web usług hostowanych na własnych serwerach J2EE lub ASP.NET lub w środowisku Azure. sieci Web klientów — to znaczy z kodem uruchomionym na stronie sieci web; aplikacje komputerowe i usług. aplikacje urządzenia, takie jak Windows Phone, iOS i Android. Wszystkie one wysyłania danych telemetrycznych do tej samej usługi.

## <a name="what-data-does-it-collect"></a>Jakie dane go zbiera?
### <a name="how-is-the-data-is-collected"></a>Jak są dane są zbierane?
Istnieją trzy źródła danych:

* Zestaw SDK, integracja z aplikacją albo [do rozwoju](app-insights-asp-net.md) lub [w czasie wykonywania](app-insights-monitor-performance-live-website-now.md). Istnieją różne zestawy SDK dla aplikacji różnych typów. Istnieje również [zestawu SDK dla stron sieci web](app-insights-javascript.md), który ładuje do przeglądarki użytkownika końcowego oraz strony.
  
  * Każdy zestaw SDK zawiera szereg [moduły](app-insights-configuration-with-applicationinsights-config.md), używających różnych technik służąca do gromadzenia różnych typów danych telemetrycznych.
  * Po zainstalowaniu zestawu SDK do rozwoju, można użyć jej interfejsu API Aby wysłać własne dane telemetryczne, oprócz standardowe moduły. Ta telemetria niestandardowa może zawierać żadnych danych, który chcesz wysłać.
* W niektórych serwerów sieci web są również agentów, które są uruchamiane równolegle z aplikacji i wysłać dane telemetryczne dotyczące Procesora, pamięci i miejsce zajmowane przez sieć. Na przykład, maszynach wirtualnych platformy Azure, hostach Docker i [serwerów J2EE](app-insights-java-agent.md) mogą mieć takich czynników.
* [Badania dostępności](app-insights-monitor-web-app-availability.md) procesy są uruchamiane przez firmę Microsoft, który wysyłania żądań do aplikacji sieci web w regularnych odstępach czasu. Wyniki są wysyłane do usługi Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jakiego rodzaju dane są zbierane?
Dostępne są następujące główne kategorie:

* [Dane telemetryczne serwera w sieci Web](app-insights-asp-net.md) -żądania HTTP.  Identyfikator URI, czas przetwarzania żądania, kod odpowiedzi, adres IP klienta. Identyfikator sesji.
* [Strony sieci Web](app-insights-javascript.md) -liczby stron, użytkownika i sesji. Czas ładowania strony. Wyjątki. Wywołania AJAX.
* Wydajności. liczniki — pamięci, Procesora, we/wy, miejsce zajmowane przez sieć.
* Klient i serwer kontekst — systemu operacyjnego, ustawienia regionalne, typu urządzenia, przeglądarki, rozdzielczość ekranu.
* [Wyjątki](app-insights-asp-net-exceptions.md) i awariami - **zrzuty stosu**, kompilacji identyfikator, typ Procesora. 
* [Zależności](app-insights-asp-net-dependencies.md) -wywołań zewnętrznych usług takich jak REST, SQL, AJAX. Identyfikator URI lub parametry połączenia, czas trwania, Powodzenie, polecenia.
* [Badania dostępności](app-insights-monitor-web-app-availability.md) — czas trwania testu i kroki, odpowiedzi.
* [Dzienniki śledzenia](app-insights-asp-net-trace-logs.md) i [telemetria niestandardowa](app-insights-api-custom-events-metrics.md) - **niczego kodu do dzienników lub telemetrii**.

[Więcej szczegółów](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak sprawdzić, jakie zbierane?
Jeśli projektujesz aplikacji przy użyciu programu Visual Studio, uruchom aplikację w trybie debugowania (F5). Dane telemetryczne zostanie wyświetlony w oknie danych wyjściowych. Z tego miejsca można skopiować go i sformatuj go jako JSON do łatwego inspekcji. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

W oknie diagnostyki również jest bardziej przejrzysta widoku.

Dla stron sieci web Otwórz okno debugowania w przeglądarce.

![Naciśnij klawisz F12, a następnie otwórz kartę sieci.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Czy można napisać kod, aby filtrować dane telemetryczne przed wysłaniem?
Będzie to możliwe pisząc [telemetrii procesora wtyczki](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak długo są przechowywane dane?
Punkty danych pierwotnych (to znaczy elementy, które można kwerendy w module analiz i inspekcji w wyszukiwaniu) są przechowywane przez 90 dni. Jeśli chcesz przechowywać dane dłużej, niż można użyć [Eksport ciągły](app-insights-export-telemetry.md) Aby skopiować go do konta magazynu.

Zagregowane dane (czyli liczby, średnie oraz innych danych statystycznych, widoczny w Eksploratorze Metryka) są przechowywane w ziarno 1 minutę przez 90 dni.

## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
Dane są widoczne i, jeśli masz konto organizacji członków zespołu. 

Można można wyeksportować przez Ciebie i Twojego zespołu i można go skopiować do innych lokalizacji i przekazywane do innych osób.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Microsoft czego informacje, które Moja aplikacja wysyła do usługi Application Insights?
Firma Microsoft wykorzystuje dane tylko w celu świadczenia usług do Ciebie.

## <a name="where-is-the-data-held"></a>Gdzie jest przechowywanych danych
* W USA, Europa lub Azja południowo-wschodnia. Podczas tworzenia nowego zasobu usługi Application Insights, można wybrać lokalizację. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Czy oznacza to, że Moja aplikacja musi być obsługiwana w USA, Europa lub Azja południowo-wschodnia?
* Nie. Aplikację można uruchomić dowolnego miejsca, w hostach lokalnie lub w chmurze.

## <a name="how-secure-is-my-data"></a>Jak bezpieczne jest danych?
Usługa Application Insights jest usługą platformy Azure. Zasady zabezpieczeń są opisane w [Azure zabezpieczeń, prywatności i zgodności oficjalny dokument](http://go.microsoft.com/fwlink/?linkid=392408).

Dane są przechowywane na serwerach Microsoft Azure. Konta w portalu Azure, konta ograniczenia zostały opisane w [Azure zabezpieczeń, prywatności i zgodności dokumentu](http://go.microsoft.com/fwlink/?linkid=392408).

Dostęp do danych przez personel firmy Microsoft jest ograniczony. Możemy uzyskać dostęp do danych tylko za zgodą użytkownika, a jeśli zachodzi konieczność obsługi usługi Application Insights. 

Dane w agregacji w aplikacjach wszystkich klientów (np. przesyłania danych i średni rozmiar śladów) są używane do poprawy usługi Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Dane telemetryczne do kogoś innego może kolidować z danych usługi Application Insights?
Dodatkowe dane telemetryczne można wysyłania do swojego konta przy użyciu klucza instrumentacji, który można znaleźć w kodzie strony sieci web. Za mało dodatkowych danych Twoje metryki może nie odzwierciedlać prawidłowo wydajności i użycia aplikacji.

Jeśli współużytkujesz kodu z innych projektów, pamiętaj, aby usunąć klucz instrumentacji.

## <a name="is-the-data-encrypted"></a>Czy dane są szyfrowane?
Nie wewnątrz serwerów w chwili obecnej.

Wszystkie dane są szyfrowane podczas przenoszenia go między centrami danych.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Czy dane są szyfrowane podczas przesyłania z mojej aplikacji na serwerach usługi Application Insights?
Tak, firma Microsoft używania protokołu https do wysyłania danych do portalu z niemal wszystkich zestawów SDK, w tym serwerów sieci web, urządzeń i stron sieci web protokołu HTTPS. Jedynym wyjątkiem jest dane przesyłane z zwykłych stron sieci web HTTP. 

## <a name="personally-identifiable-information"></a>Dane osobowe
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>Czy osobiście informacji osobowych mógł zostać wysłany do usługi Application Insights?
Tak, jest możliwe. 

Jako ogólne wytyczne:

* Większość standardowych telemetrii (to znaczy telemetrii wysyłane bez pisania żadnego kodu) nie ma jawnego dane osobowe. Jednak może być możliwe do identyfikowania osób przez wnioskowania z kolekcji zdarzeń.
* Komunikaty wyjątku i śledzenia mogą zawierać dane osobowe
* Telemetria niestandardowa — to znaczy wywołań takich jak TrackEvent napisany za pomocą interfejsu API lub dziennika śledzenia kodu — może zawierać żadnych danych, którą wybierzesz.

Tabela na końcu tego dokumentu zawiera bardziej szczegółowe opisy zebranych danych.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>Mogę zobowiązani do przestrzegania przepisów eksportowych w odniesieniu do danych osobowych?
Tak. Jest obowiązek upewnij się, że zbierania i używania danych spełnia z prawami i przepisami oraz z warunkami usług Online firmy Microsoft.

Klientów należy odpowiednio informuje o dane, które aplikacji służy do zbierania i używania danych.

#### <a name="can-my-users-turn-off-application-insights"></a>Moi użytkownicy mogą wyłączyć funkcję usługi Application Insights?
Nie bezpośrednio. Firma Microsoft nie udostępnia przełącznik, który użytkownicy mogą pracować wyłączyć usługi Application Insights.

Jednak taka funkcja można zaimplementować w aplikacji. Wszystkie zestawy SDK obejmują ustawienia interfejsu API, która wyłącza gromadzenia danych telemetrii. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>Moja aplikacja przypadkowo gromadzi informacje poufne. Można usługi Application Insights usunąć te dane, nie będzie on zachowane
Usługa Application Insights nie filtru lub usuwać dane. Należy odpowiednio zarządzania danymi i unikanie wysyłania tych danych do usługi Application Insights.

## <a name="data-sent-by-application-insights"></a>Dane wysyłane przez usługę Application Insights
Zestawy SDK różnią się między platformami i jest wiele składników, które można zainstalować. (Zobacz [usługi Application Insights — omówienie][start].) Każdy składnik wysyła innych danych.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasy danych przesyłanych w różnych scenariuszy
| Twoja Akcja | Klasy danych zebranych (patrz tabela dalej) |
| --- | --- |
| [Dodaj zestaw SDK usługi Application Insights do projektu sieci web .NET][greenbrown] |Kontekstu serwera<br/>Wywnioskowane<br/>Liczniki wydajności<br/>Żądania<br/>**Wyjątki**<br/>Sesja<br/>użytkownicy |
| [Zainstaluj Monitor stanu na serwerze IIS][redfield] |Zależności<br/>Kontekstu serwera<br/>Wywnioskowane<br/>Liczniki wydajności |
| [Dodaj zestaw SDK usługi Application Insights do aplikacji sieci web Java][java] |Kontekstu serwera<br/>Wywnioskowane<br/>Żądanie<br/>Sesja<br/>użytkownicy |
| [Dodaj zestaw SDK JavaScript do strony sieci web][client] |ClientContext <br/>Wywnioskowane<br/>Strona<br/>ClientPerf<br/>Ajax |
| [Definiowanie właściwości domyślne][apiproperties] |**Właściwości** na wszystkie standardowe i niestandardowe zdarzenia |
| [Wywołanie TrackMetric][api] |Wartości numeryczne<br/>**właściwości** |
| [Wywołanie Śledź *][api] |Nazwa zdarzenia<br/>**właściwości** |
| [Wywołanie TrackException][api] |**Wyjątki**<br/>Zrzut stosu<br/>**właściwości** |
| Zestaw SDK nie można zebrać danych. Na przykład: <br/> -Nie można uzyskać dostępu do liczników wydajności<br/> -wyjątku w inicjatorze telemetrii |Diagnostyka zestawu SDK |

Aby uzyskać [zestawy SDK dla innych platform][platforms], zobacz swoje dokumenty.

#### <a name="the-classes-of-collected-data"></a>Klasy zebranych danych
| Klasa zebranych danych | Obejmuje (nie stanowi wyczerpującej listy) |
| --- | --- |
| **właściwości** |**Wszystkie dane - ustaleniami kodu** |
| DeviceContext |Identyfikator, adresu IP, ustawienia regionalne, model urządzenia, sieci, typ sieci, nazwa producenta OEM, rozdzielczość ekranu wystąpienia roli, nazwa roli, typu urządzenia |
| ClientContext |System operacyjny, ustawienia regionalne, języka, sieci, rozdzielczość okna |
| Sesja |Identyfikator sesji |
| Kontekstu serwera |Nazwa komputera, ustawienia regionalne, systemu operacyjnego, urządzenia, sesji użytkownika, kontekst użytkownika, operacji |
| Wywnioskowane |Lokalizacja geograficzna z adresu IP, timestamp, systemu operacyjnego, przeglądarki |
| Metryki |Nazwa metryki i wartości |
| Zdarzenia |Nazwa zdarzenia i wartości |
| PageViews |Nazwa adresu URL i strony lub ekranu |
| Wydajności klienta |Nazwa adresu URL/strony, czas ładowania przeglądarki |
| Ajax |Połączenia HTTP ze strony sieci web do serwera |
| Żądania |Adres URL, czas trwania, kod odpowiedzi |
| Zależności |Typ (SQL, HTTP,...), parametry połączenia lub identyfikator URI, synchronizacji/asynchroniczne, czas trwania, Powodzenie, instrukcji SQL (za pomocą Monitora stanu) |
| **Wyjątki** |Typ, **komunikat**, stosy wywołań, plików i wierszy number, identyfikator wątku źródła |
| Awarie |Identyfikator procesu, identyfikator procesu nadrzędnego, identyfikator wątku awarii; poprawka aplikacji, identyfikator kompilacji;  Typ wyjątku, adres, reason; symbole zaciemnionego i rejestrów, binarne początkowy i końcowy adres, nazwa pliku binarnego i ścieżki, typ procesora |
| Ślad |**Komunikat** i poziom ważności |
| Liczniki wydajności |Czas procesora, pamięci, liczby żądań, stawki wyjątek, bajtów prywatnych procesu, stawki we/wy, czas trwania żądania, długość kolejki żądań |
| Dostępność |Kod odpowiedzi testu sieci Web, czas trwania każdego kroku testu, nazwa testu, timestamp, Powodzenie, czas odpowiedzi, lokalizacji testu |
| Diagnostyka zestawu SDK |Komunikat śledzenia lub wyjątku |

Możesz [wyłącz niektóre dane ApplicationInsights.config edycji][config]

## <a name="credits"></a>Napisy końcowe
Ten produkt zawiera GeoLite2 dane utworzone przez MaxMind dostępnej w sklepie [ http://www.maxmind.com ](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

