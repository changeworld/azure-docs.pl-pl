---
title: Wydajności aplikacji sieci Web monitorowania - Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak zastosowań usługi Application Insights w cyklu opracowywania oprogramowania
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 24e249bb515c509f2fba1f943ac5e23a1ea9965e
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808482"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Szczegółowa diagnostyka dla aplikacji sieci Web i usług dzięki usłudze Application Insights
## <a name="why-do-i-need-application-insights"></a>Dlaczego muszę usługi Application Insights?
Usługa Application Insights monitoruje uruchomionej aplikacji sieci web. Informuje o awarii i problemy z wydajnością i pomaga analizować, jak klienci korzystają z aplikacji. Ponieważ działa aplikacji działających na wiele platform (ASP.NET, J2EE, Node.js,...) i znajduje się w chmurze lub lokalnie. 

![Aspekty złożoności opracowywania aplikacji sieci web](./media/app-insights-devops/010.png)

Jest to niezbędne do monitorowania aplikacji modern jest uruchomiona. Najważniejsze który chcesz wykryć błędów zanim większość klientów. Chce również odnaleźć i rozwiązać problemy z wydajnością, gdy nie krytyczny, może to spowolnić rzeczy lub powodować pewne niedogodności dla użytkowników. I przy system działa zgodnie z oczekiwaniami, należy znać, co robią użytkownicy z nim: są one przy użyciu najnowszych funkcji? Są one pomyślne z nim?

Nowoczesnych aplikacji sieci web są tworzone w cyklu ciągłego dostarczania: wydania nowej funkcji lub ulepszenia; Sprawdź, jak działa dla użytkowników. Zaplanuj kolejny krok Programowanie oparte na wiedzy. Część klucza cyklu jest na etapie obserwacji. Usługa Application Insights udostępnia narzędzia do monitorowania wydajności i użycia aplikacji sieci web.

Najważniejszym aspektem tego procesu jest diagnostyki i diagnostyki. W przypadku niepowodzenia aplikacji biznesowych jest trwa utracone. Podstawową rolą platformy monitorowania w związku z tym jest niezawodnie wykrywanie błędów, powiadomienie zostanie natychmiast i wyświetlane informacje potrzebne do zdiagnozowania problemu. Jest to dokładnie, czego usługi Application Insights.

### <a name="where-do-bugs-come-from"></a>Skąd pochodzą usterki?
Awarie w sieci web systemów wynikają zwykle z powodu problemów z konfiguracją lub zły interakcje między ich wiele składników. Pierwszym zadaniem podczas działania zdarzenia na żywo lokacji w związku z tym jest ustalenie locus problemu: które składnika lub relacji jest przyczyną?

Niektórzy, z szarym włosów do zapamiętania ery prostsze, w którym program komputerowy były uruchamiane w jednym komputerze. Deweloperzy może przetestować jej starannie przed wysłaniem i o wysłane, czy Zobacz lub rzadko traktować go ponownie. Użytkownicy musieliby wystawione przez wiele lat z pozostałych usterki. 

Dlatego bardzo różnią się teraz rzeczy. Aplikacja ma nadmiar różnych urządzeń do uruchamiania na i może być trudne do zagwarantowania dokładnie takie samo zachowanie na każdej z nich. Hosting aplikacji w chmurze oznacza fast można naprawić błędy, ale oznacza to również ciągłego konkurencji i oczekuje nowe funkcje w częstych odstępach czasu. 

W tych warunkach jedynym sposobem na kontrolowanie przedsiębiorstwa na liczbę usterek jest automatyczne testy jednostkowe. Byłoby niemożliwe ręcznie ponownie przetestuj wszystkie elementy w każdym dostarczania. Testu jednostkowego jest teraz popularne część procesu kompilacji. Narzędzia, takie jak chmury testowej Xamarin pomaga, zapewniając automatyczne interfejsu użytkownika, testów na wielu wersji przeglądarki. Te systemy testowania umożliwiają nadzieję, że częstotliwość błędów znajduje się w aplikacji można ograniczone do minimum.

Aplikacje sieci web typowe ma wiele składników na żywo. Oprócz klienta (w aplikacji przeglądarki lub urządzenia) i serwera sieci web jest prawdopodobnie przetwarzania znacznej wewnętrznej bazy danych. Prawdopodobnie wewnętrznej bazy danych jest potoku składników lub zbiór im brać części. I wiele z nich nie będzie formantu — są one usług zewnętrznych, na których jest zależna.

W konfiguracji, takich jak te może być trudne i uneconomical do testowania lub przewiduje się, co trybu awaryjnego możliwe innych niż system na żywo. 

### <a name="questions-"></a>Pytania...
Pytaniami poprosimy, gdy jest tworzenie system sieci web:

* Moja aplikacja awarii 
* Dokładnie naturę? — Jeśli go nie powiodło się żądanie warto wiedzieć, jak uzyskano istnieje. Potrzebujemy śledzenia zdarzeń...
* Czy Moja aplikacja tyle szybko? Jak długo trwa odpowiadać na żądania typowe?
* Serwer może obsłużyć obciążenie? Gdy to liczba żądań wzrośnie, ma czas odpowiedzi praw stałą?
* Stopień reakcji są moje zależności - interfejsów API REST, baz danych i innymi składnikami, które wywołuje Moja aplikacja. W szczególności jeśli system jest powolne, jest mój składnik lub otrzymuję powolne odpowiedzi od kogoś innego?
* Jest Moja aplikacja w górę lub w dół? Go wynika z całego świata? Powiadom mnie, jeśli zatrzymuje...
* Co to jest główną przyczynę? Został błąd w składniku Moje lub zależności? Jest to problem komunikacji?
* Wpływ na ilu użytkowników? Mając więcej niż jeden problem do rozwiązania problemu, który jest najważniejsze?

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
![Podstawowy przepływ pracy z usługi Application Insights](./media/app-insights-devops/020.png)

1. Usługa Application Insights instruments aplikacji i wysyła dane telemetryczne dotyczące go, gdy aplikacja jest uruchomiona. Możesz utworzyć zestaw SDK usługi Application Insights do aplikacji albo można zastosować Instrumentacji w czasie wykonywania. Pierwszej metody jest bardziej elastyczne, ponieważ można dodać własne dane telemetryczne do regularnych modułów.
2. Dane telemetryczne są wysyłane do portalu usługi Application Insights, w którym są przechowywane i przetwarzane. (Mimo że usługi Application Insights jest hostowana na platformie Microsoft Azure, może monitorować wszystkich aplikacji sieci web — nie tylko Azure aplikacji).
3. Dane telemetryczne są prezentowane w postaci wykresów i tabel zdarzeń.

Istnieją dwa główne rodzaje danych telemetrii: wystąpień zagregowane i raw. 

* Na przykład dane wystąpienia obejmuje raportu żądania, które zostały odebrane przez aplikację sieci web. Można znaleźć i sprawdzić szczegóły żądania przy użyciu narzędzia wyszukiwania w portalu usługi Application Insights. Wystąpienie obejmuje dane, takie jak jak długo trwało aplikacji odpowiadanie na żądanie, a także żądanego adresu URL, przybliżona lokalizacji klienta i innych danych.
* Zagregowane dane obejmuje liczby zdarzeń na jednostkę czasu, aby porównać liczbę żądań w czasie odpowiedzi. Obejmuje on też średnie metryk, takich jak czas odpowiedzi na żądanie.

Główne kategorie danych są:

* Żądania do aplikacji (zwykle żądań HTTP), z danymi na adres URL, czas odpowiedzi i powodzenie lub niepowodzenie.
* Zależności - SQL i REST wywołań przez aplikację, a także z identyfikatora URI, czas reakcji i Powodzenie
* Wyjątki, w tym śladów stosu.
* Dane wyświetleń strony, które pochodzą z przeglądarek użytkowników.
* Metryki, np. liczniki wydajności, a także metryki pisania samodzielnie. 
* Niestandardowe zdarzenia, których można śledzić zdarzenia biznesowe
* Ślady dziennika używane do debugowania.

## <a name="case-study-real-madrid-fc"></a>— Analiza przypadków: F.C. rzeczywistych Madrycie
Usługa sieci web [rzeczywistych klub Football Madrycie](http://www.realmadrid.com/) służy wentylatory około 450 milionów na świecie. Wentylatory dostępny zarówno za pośrednictwem przeglądarki sieci web i aplikacji mobilnych klub. Wentylatory można nie tylko zarezerwować biletów, ale także uzyskać dostęp do informacji i wideo klipy na wyniki, odtwarzaczach i gry nadchodzących. Mogą one wyszukiwania z filtrami, takie jak numery celów oceny. Dostępne są także łącza do mediów społecznościowych. Środowisko użytkownika jest bardzo spersonalizowanych i został zaprojektowany jako komunikacja dwukierunkowa nawiązanie wentylatory.

Rozwiązanie [to system usług i aplikacji w systemie Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). Skalowalność to decydujące znaczenie: ruch jest zmienną i mogą dotrzeć bardzo dużej ilości, podczas i wokół dopasowań.

Rzeczywiste madryckiego jest niezbędne do monitorowania wydajności systemu. Azure Application Insights udostępnia obszerne przez system, zapewniając niezawodne i wysoki poziom usług. 

Klub również pobiera szczegółowy opis jego wentylatorów: gdy są one (w Hiszpanii są tylko % 3), jakie odsetek mają w odtwarzaczy, historycznych wyniki i gry nadchodzących i jak odpowiadać do dopasowania wyników.

Większość tych danych telemetrycznych automatycznie są zbierane z nie dodano kod, który uproszczone rozwiązanie i zmniejszyć złożoność działania.  Rzeczywiste Madryt, usługi Application Insights zajmuje się punkty telemetrii MLD 3.8 każdego miesiąca.

Rzeczywiste Madrycie używa modułu usługi Power BI można wyświetlić ich telemetrii.

![Power BI widok telemetrii usługi Application Insights](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Wykrywanie inteligentne
[Proaktywna Diagnostyka](app-insights-proactive-diagnostics.md) jest funkcją ostatnie. Bez żadnej specjalnej konfiguracji przez użytkownika usługi Application Insights automatycznie wykrywa i ostrzega o nietypowych wzrostu w awariami w aplikacji. Jest inteligentne ignorowania tła sporadycznych błędów i wzrostu, które są po prostu proporcjonalny do wzrost żądania. Tak na przykład jeśli awarii w jednym z usługi, które zależą od lub nowej kompilacji, można wdrożyć tylko nie działa tak dobrze, a następnie będzie wiadomo o nim, jak przyjrzeć się poczty e-mail. (I istnieją elementów webhook, dzięki czemu możesz wyzwolić innych aplikacji.)

Innym aspektem tej funkcji wykonuje codzienne dokładnych analiz telemetrii, wyszukiwanie nietypowe wzorce wydajności, które są trudne do odnajdywania. Na przykład może znaleźć wydajności skojarzonego z określonym obszarze geograficznym lub w wersji przeglądarki.

W obu przypadkach alert nie tylko informuje objawy zostanie odnaleziony, ale również zapewnia dane, które należy do zdiagnozowania problemu, takie jak raporty odpowiedni wyjątek.

![Wiadomości e-mail z proaktywna Diagnostyka](./media/app-insights-devops/030.png)

Samtec klienta powiedzieć: "podczas ostatnie funkcji cutover znaleziono-skalowania bazy danych naciśnięcie limit zasobów i powoduje przekroczeń limitu czasu. Alerty o wykryciu aktywnego dostarczone dosłownie anonsowanej możemy zostały klasyfikowane problem, bardzo blisko czasu rzeczywistego. Ten alert w połączeniu z platformą Azure alerty pomogły niemal natychmiast rozwiązać problem. Łączny czas przestojów spowodowanych < 10 minut."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Można w trosce środowisko wdrażania ostatniej kompilacji. Jeśli występują problemy, chcesz od razu wiedzieć o je tak, aby można Wycofaj w razie potrzeby. Strumień na żywo metryk umożliwia kluczowych metryk z opóźnieniem około jednej sekundy.

![Metryki na żywo](./media/app-insights-devops/040.png)

I pozwala bezpośrednio sprawdzić przykładowe jakiekolwiek błędy lub wyjątki.

![Zdarzenia błędów na żywo](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikacji
Mapowanie aplikacji automatycznie odnajduje topologii aplikacji r. informacje o wydajności na nim, aby można było łatwo zidentyfikować wąskich gardeł zmniejszających wydajność i przepływów powodować problemy w środowisku rozproszonym. Umożliwia ona odnajdywanie zależności aplikacji w usługach Azure. Problem może klasyfikowanie zrozumienie, jeśli powiązane kodu lub zależności powiązane i z jednego miejsca Przechodzenie do szczegółów w powiązanych diagnostyki wystąpić. Na przykład aplikacji może być się niepowodzeniem z powodu spadek wydajności w warstwie SQL. Mapowanie aplikacji możesz od razu zobacz temat i przejść do szczegółów w Doradcę w zakresie indeksowania SQL lub wystąpić szczegółowe informacje o zapytań.

![Mapa aplikacji](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights analityka
Z [Analytics](app-insights-analytics.md), możesz pisać zapytania dowolnego zaawansowanych języka przypominającego SQL.  Diagnozowanie w stosie całą aplikację staje się łatwe zgodnie z różnych perspektyw nawiązywanie połączeń i należy zadać odpowiednie pytania do skorelowania wydajność usługi z metryki biznesowe i obsługi klienta. 

Umożliwia wysyłanie zapytań wystąpienia telemetrii i metryki nieprzetworzone dane przechowywane w portalu. Język obejmuje filtr sprzężenia, agregacji i innych operacji. Można obliczyć pola i wykonywać analizy statystycznej. Brak wizualizacji zarówno tabelarycznej i graficznej.

![Wykres zapytania i wyniki analizy](./media/app-insights-devops/025.png)

Na przykład jest łatwe:

* Segmentu danych wydajności aplikacji żądania przez klienta warstw zrozumienie ich obsługi.
* Wyszukiwanie określonych kodów błędów lub nazwy niestandardowe zdarzenie podczas badania działającą witrynę.
* Przechodzenie do użycia aplikacji określonych odbiorców, aby zrozumieć sposób uzyskać i przyjęte funkcji.
* Śledzenie sesji i czasy odpowiedzi dla określonych użytkowników umożliwiają zespołom operacji i pomocy technicznej zapewnić obsługę błyskawicznych klienta.
* Określ funkcje najczęściej używanych aplikacji odpowiedzi na pytania priorytetyzacji funkcji.

Dana klienta DNN: "usługi Application Insights podał Brak częścią równości jest możliwe do zestawienia, sortowania, kwerendy i filtrowanie danych zgodnie z potrzebami. Stosowanie naszego zespołu, aby znaleźć dane z językiem zaawansowanych zapytań pozwoliło nam można znaleźć szczegółowe informacje i rozwiązywania problemów za pomocą ich własnych ingenuity i środowisko nie został jeszcze wiemy, że było. Wiele odpowiedzi interesujące pochodzą z pytania, począwszy od *"I swoje if...".*"

## <a name="development-tools-integration"></a>Programowanie narzędzia integracji
### <a name="configuring-application-insights"></a>Konfigurowanie usługi Application Insights
Visual Studio i Eclipse mieć narzędzia do konfigurowania poprawne pakietów SDK dla projektu, które tworzysz. Brak polecenia menu, aby dodać usługi Application Insights.

Jeśli wystąpi używa struktury rejestrowania śledzenia, takie jak Log4N, NLog lub System.Diagnostics.Trace, następnie możesz uzyskać możliwość wysyłania dzienników do usługi Application Insights oraz inne dane telemetryczne, dzięki czemu można łatwo skorelować śladów z żądaniami, zależności wywołania i wyjątki.

### <a name="search-telemetry-in-visual-studio"></a>Wyszukaj dane telemetryczne w programie Visual Studio
Podczas opracowywania i debugowania funkcji, można wyświetlać i wyszukaj dane telemetryczne bezpośrednio w programie Visual Studio przy użyciu tej samej funkcji wyszukiwania w portalu sieci web.

I po zalogowaniu się wyjątku usługi Application Insights, można wyświetlić punktu danych w programie Visual Studio i przejść bezpośrednio do odpowiedni kod.

![Visual Studio wyszukiwania](./media/app-insights-devops/060.png)

Podczas debugowania, masz opcję, aby zachować dane telemetryczne w komputerze deweloperskim, wyświetlając go w programie Visual Studio, ale bez wysyłania go do portalu. Ta opcja lokalnego pozwala uniknąć mieszanie debugowania o telemetrii produkcji.

### <a name="build-annotations"></a>Tworzenie adnotacji
Jeśli używasz programu Visual Studio Team Services do tworzenia i wdrażania aplikacji, wdrożenie adnotacje wyświetlane na wykresach w portalu. Jeśli z najnowszej wersji miała wpływu na podstawie metryk, staje się oczywiste.

![Tworzenie adnotacji](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Elementy robocze
Gdy zostanie zgłoszony alert, usługi Application Insights może automatycznie tworzyć elementu roboczego pracy śledzenia systemu.

## <a name="but-what-about"></a>Ale co zrobić...?
* [Prywatność i magazynu](app-insights-data-retention-privacy.md) -telemetrii jest przechowywana na serwerach Azure bezpieczne.
* Wydajność — wpływ jest bardzo niskie. Dane telemetryczne jest umieścić w zadaniu wsadowym.
* [Cennik](app-insights-pricing.md) — możesz rozpocząć pracę bezpłatnie i który będzie kontynuowane, podczas pracy w niskim poziomie.


## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do korzystania z usługi Application Insights jest bardzo proste. Główne dostępne opcje to:

* Instrumentacja aplikacji sieci web już uruchomiona. Dzięki temu wszystkie dane telemetryczne wbudowanych wydajności. Nie jest dostępny do [Java](app-insights-java-live.md) i [serwery IIS](app-insights-monitor-performance-live-website-now.md), a także do [aplikacje sieci web Azure](app-insights-azure.md).
* Instrumentacja projektu w czasie projektowania. Można to zrobić [ASP.NET](app-insights-asp-net.md) lub [Java](app-insights-java-get-started.md) aplikacji, a także [Node.js](app-insights-nodejs.md) i hosta [innych typów](app-insights-platforms.md). 
* Instrumentem [dowolną stronę sieci web](app-insights-javascript.md) przez dodanie fragment kodu krótki.

