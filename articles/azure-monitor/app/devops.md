---
title: Wydajności aplikacji sieci Web — monitorowanie usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak usługa Application Insights dopasowuje się do cyklu devOps
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 24b0bc01b5cb4f1d2696a7c9526d586c9b42d0fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899714"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Szczegółowa diagnostyka dla aplikacji internetowych i usług dzięki usłudze Application Insights
## <a name="why-do-i-need-application-insights"></a>Dlaczego muszę usługi Application Insights?
Usługa Application Insights monitoruje uruchomionej aplikacji sieci web. Informuje o awarii i problemów z wydajnością i pomaga analizować, jak klienci korzystają z Twojej aplikacji. Dla aplikacji działających na wielu platformach (ASP.NET, Java EE Node.js,...) i lepiej jest hostowana w chmurze lub lokalnie. 

![Aspekty złożoności opracowywania aplikacji sieci web](./media/devops/010.png)

Jest to niezbędne do monitorowania nowoczesnych aplikacji, gdy jest on uruchomiony. Co najważniejsze chcesz wykrywanie błędów, zanim większość klientów. Także odnajdywać i rozwiązuj problemy z wydajnością, gdy nie krytycznego, być może spowolnić lub powodować pewne niedogodności dla użytkowników. A gdy system działa zgodnie z oczekiwaniami, chcesz wiedzieć, co użytkownicy robią z nią: Są one przy użyciu najnowszych funkcji? One powiodły się z nim?

Nowoczesnych aplikacji sieci web są tworzone w cyklu ciągłe dostarczanie: zwolnić nową funkcję lub ulepszenie; Sprawdź, jak dobrze działa w przypadku użytkowników; Zaplanuj kolejny krok w rozwoju, w oparciu o tę wiedzę. Kluczowym elementem tego cyklu jest w fazie obserwacji. Application Insights udostępnia narzędzia umożliwiające monitorowanie aplikacji sieci web, wydajność i użycie.

Najważniejszych aspektów tego procesu jest diagnostyki i diagnostyki. Jeśli aplikacja ulegnie awarii, biznesowe są są tracone. Podstawową rolą monitorowania Framework jest w związku z tym wykrywanie błędów w niezawodny sposób będą, powiadomienie natychmiast, aby przedstawić informacje potrzebne do zdiagnozowania problemu. Jest to dokładnie, jak działa usługa Application Insights.

### <a name="where-do-bugs-come-from"></a>Skąd pochodzą usterki?
Błędy w internetowych systemach zwykle wynikają z problemy z konfiguracją lub zły interakcje między ich wiele składników. Pierwsze zadanie, gdy co dzień do czynienia ze zdarzeniem aktywnej witryny jest w związku z tym zidentyfikowanie locus problemu: które składnik lub relacji jest przyczyną?

Niektóre z NAS, obejmujących szarego włosów Pamiętaj, aby prostsze ery, w którym program komputerowy uruchomiono w jednym komputerze. Deweloperzy będą ją dokładnie przetestuj przed dostarczeniem go; i konieczności wysłane, czy rzadko Zobacz lub zastanów się go ponownie. Użytkownicy będą musiały wystawione przez wiele lat z pozostałych usterkami. 

Teraz są tak bardzo różne rzeczy. Twoja aplikacja ma mnóstwo różnych urządzeń z systemem i może być trudne w celu zagwarantowania dokładnie takie samo zachowanie na każdym z nich. Hostowanie aplikacji w chmurze oznacza szybko można naprawić błędy, ale oznacza to również ciągłe konkurencji i przy założeniu, nowych funkcji w krótkich odstępach czasu. 

W tych warunkach jedynym sposobem na kontrolowanie firmy na podstawie liczby usterek jest zautomatyzowane testy jednostkowe. Nie można wysyłać do ręcznie ponownie przetestuj wszystkie elementy na dostarczaniu wartości, co. Test jednostkowy jest teraz powszechnie używane część procesu kompilacji. Narzędzi, takich jak Xamarin Test Cloud pomaga, zapewniając automatyczne interfejsu użytkownika, testy w wielu wersjach przeglądarki. Tych metod badań umożliwiają nam mamy nadzieję, że współczynnik błędów znalezionych wewnątrz aplikacji można ograniczyć do minimum.

Typowych aplikacji internetowych ma wiele składników na żywo. Oprócz klienta (w aplikacji przeglądarki lub urządzenia) i serwera sieci web prawdopodobnie można przetwarzania znacznej wewnętrznej bazy danych. Być może wewnętrznej bazy danych jest potok składników lub im kolekcję współpracujących fragmentów. I wiele z nich nie będzie w kontrolce — są one usługami zewnętrznymi, na których polegasz.

W konfiguracji, takie jak te może być trudne i uneconomical do testowania lub przewiduje się, co trybu awaryjnego innych niż sam system na żywo. 

### <a name="questions-"></a>Pytania dotyczące...
Kilka pytań prosimy gdy pracujemy nad systemem internetowym:

* Moja aplikacja uległa awarii? 
* Co dokładnie tak się stało? — Jeśli go nie powiodło się żądanie chcę wiedzieć, jak masz istnieje. Potrzebujemy śledzenia zdarzeń...
* Czy Moja aplikacja wystarczająco szybko? Jak długo trwa odpowiadanie na żądania typowe?
* Czy serwer może obsłużyć obciążenia? Gdy wzrośnie liczba żądań, czas odpowiedzi przechowywania stałego?
* Jak szybko są moich zależnościach — interfejsy API REST, baz danych i inne składniki, które wywołuje aplikację. W szczególności jeśli system jest powolne, to jest mój składnik lub otrzymuję powolne odpowiedzi od kogoś innego?
* Jest moją aplikację w górę lub w dół? Jego wynika z całego świata? Powiadom mnie, jeśli go nie będzie możliwy...
* Co to jest główną przyczynę? Był błąd w składniku Moje lub zależności? Jest to problem komunikację?
* Ilu użytkowników dotyczy problem? Jeśli mam więcej niż jeden problem do rozwiązania, które są najbardziej istotne?

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
![Podstawowy przepływ pracy usługi Application Insights](./media/devops/020.png)

1. Usługa Application Insights umożliwia Instrumentację aplikacji i wysyła dane telemetryczne na jego temat, gdy aplikacja jest uruchomiona. Możesz tworzyć zestaw SDK usługi Application Insights do aplikacji lub zastosować Instrumentację w czasie wykonywania. Pierwszej metody jest bardziej elastyczna, podczas dodawania własnych danych telemetrycznych do regularnego modułów.
2. Dane telemetryczne są wysyłane do portalu usługi Application Insights, gdzie są przechowywane i przetwarzane. (Mimo że usługa Application Insights jest hostowany na platformie Microsoft Azure, może monitorować wszystkie aplikacje sieci web — aplikacje nie tylko platforma Azure).
3. Dane telemetryczne są prezentowane w postaci wykresów i tabel zdarzeń.

Istnieją dwa główne rodzaje danych telemetrycznych: wystąpienia zagregowane i raw. 

* Dane wystąpienia obejmuje na przykład raportu żądania, które zostały odebrane przez aplikację sieci web. Można znaleźć i sprawdzić szczegóły żądania za pomocą narzędzia Search w portalu Application Insights. Wystąpienie obejmuje dane, takie jak czas jaką przebyła aplikacja odpowiadanie na żądania, a także żądanego adresu URL, zbliżenie lokalizacji klienta oraz innych danych.
* Zagregowane dane zawiera liczby zdarzeń na jednostkę czasu, aby można było porównać to liczba żądań z czasem odpowiedzi. Zawiera on również średnie metryki, takie jak czasy odpowiedzi żądania.

Główne kategorie danych są:

* Żądania do aplikacji (zwykle żądania HTTP) z danymi na adres URL, czas reakcji i powodzenie lub niepowodzenie.
* Zależności - wywołania REST i SQL przez aplikację, także z identyfikatora URI, czasy reakcji i Powodzenie
* Wyjątki, ślady stosu w tym.
* Dane widoku strony, które pochodzą z przeglądarek użytkowników.
* Metryki, takie jak liczniki wydajności, a także metryki, napisane samodzielnie przez Ciebie. 
* Zdarzenia niestandardowe, które służą do śledzenia zdarzeń biznesowych
* Ślady dzienników używana do debugowania.

## <a name="case-study-real-madrid-fc"></a>Analiza przypadku: Real Madryt F.C.
Usługa sieci web [Real Madryt Football Club](https://www.realmadrid.com/) służy około 450 milionom fanów z całego świata. Fanów uzyskać do niego dostęp zarówno za pośrednictwem przeglądarki sieci web i aplikacje mobilne Club. Fanów nie tylko książki biletów, ale również dostęp do klipów informacji i wideo na wyniki, graczy i nadchodzących gry. Za pomocą filtrów można wyszukiwania, takie jak liczby celów oceniane. Dostępne są także łącza do mediów społecznościowych. Środowisko użytkownika jest wysoce spersonalizowanych i został zaprojektowany jako komunikacja dwukierunkowa, dzięki którym można zaangażować fanów.

Rozwiązanie [system usług i aplikacji w systemie Microsoft Azure to](https://www.microsoft.com/inculture/sports/real-madrid/). Skalowalność to kluczowe: ruch jest zmienna i mogą dotrzeć bardzo dużej ilości danych, podczas i wokół dopasowań.

Rzeczywiste Madryt, ważne jest do monitorowania wydajności systemu. Usługa Azure Application Insights oferuje kompleksowy wgląd w systemie, zapewniając niezawodne i wysoki poziom usługi. 

Klub zapewnia również szczegółowe informacje dotyczące jego fanów: gdy są one (w Hiszpanii są tylko 3%), jaki odsetek mają w graczy, wyniki historyczne i kolejnych gier i sposobach reagowania do dopasowania wyników.

Większość z tych danych telemetrycznych są automatycznie zbierane z nie dodano kod, który uproszczone rozwiązanie i zmniejszyć złożoność operacyjną.  Przypadku Real Madryt, Application Insights zajmuje się punktów danych telemetrycznych MLD 3.8 każdego miesiąca.

Real Madryt używa modułu usługi Power BI, aby wyświetlić ich dane telemetryczne.

![Power BI widok danych telemetrycznych usługi Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Wykrywanie inteligentne
[Diagnostyka proaktywna](../../azure-monitor/app/proactive-diagnostics.md) jest najnowszych funkcji. Bez żadnej specjalnej konfiguracji przez Ciebie usługi Application Insights automatycznie wykrywa i ostrzega o tym nietypowe wzrostami współczynniki błędów w aplikacji. Jest to inteligentny zignorować tła okazjonalnych awarii i wzrostu, które są po prostu proporcjonalny do wzrost liczby żądań. Na przykład jeśli wystąpi awaria w jednej z usług, których zależysz lub nową kompilację, po prostu wdrożonych nie działa tak dobrze, a następnie będziesz wiedzieć o nim, jak najszybciej Przyjrzyj się swój adres e-mail. (I istnieją elementy webhook, dzięki czemu można wyzwalać inne aplikacje)

Innym aspektem ta funkcja wykonuje codzienne szczegółowej analizy telemetrii, wyszukiwanie nietypowe wzorce wydajności, które są trudne do odnajdywania. Na przykład prowadzą spadek wydajności związany z określonym obszarze geograficznym lub przy użyciu wersji przeglądarki.

W obu przypadkach ten alert nie tylko informuje objawy został odnaleziony, ale również zapewnia dane, których potrzebujesz, aby pomóc w zdiagnozowaniu problemu, takie jak raporty odpowiedni wyjątek.

![Wyślij wiadomość e-mail z aktywną diagnostykę](./media/devops/030.png)

Klient Samtec powiedział: "W ciągu ostatnich funkcją jednorazowej migracji, znaleźliśmy-scaled bazy danych, który osiągnięcia limitów zasobów, jego przyczyną przekroczenia limitu czasu. Alerty proaktywne wykrywanie dostarczone za pośrednictwem dosłownie anonsowanej możemy zostały sklasyfikowaniu problem, bardzo w czasie rzeczywistym. Ten alert, w połączeniu z alertów platformy Azure pomógł nam niemal natychmiast rozwiązać ten problem. Łączny czas przestoju < 10 minut."

## <a name="live-metrics-stream"></a>Stream metryki na żywo
Wdrażanie najnowszych kompilacji może być środowisko zaniepokojonym wyrazem. Jeśli wystąpią jakiekolwiek problemy, chcesz wiedzieć o nich natychmiast, tak aby kopi, jeśli to konieczne. Live Stream metryki zapewnia kluczowe metryki z czasem oczekiwania wynoszącym około 1 sekundy.

![Metryki na żywo](./media/devops/0040.png)

I pozwala natychmiast Zbadaj próbkę z wszelkimi błędami i wyjątkami.

![Zdarzenia awarii na żywo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikacji
Mapa aplikacji automatycznie odnajduje topologię aplikacji, rozmieszczania informacje o wydajności powstałej, co pozwala łatwo identyfikować wąskie gardła wydajności i problematyczne przepływy w środowisku rozproszonym. Umożliwia ona odnajdywanie zależności aplikacji w usługach platformy Azure. Zrozumienie można sklasyfikowanie problemu, jeśli jest związane z kodem lub zależności powiązane i z jednego miejsca przechodzenia do szczegółów w powiązanych Diagnostyka środowiska. Na przykład aplikacji może być się niepowodzeniem z powodu obniżenie wydajności w warstwie SQL. Za pomocą mapy aplikacji można natychmiast zobaczyć i przejść do SQL Index Advisor lub szczegółowe informacje o zapytaniach środowiska.

![Mapa aplikacji](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Za pomocą [Analytics](../../azure-monitor/app/analytics.md), możesz pisać zapytania dowolnego w zaawansowanym językiem podobnego do SQL.  Diagnozowanie w stosie całej aplikacji staje się proste zgodnie z różnych perspektyw nawiązywanie połączeń i można zadać odpowiednie pytania do skorelowania wydajność usługi za pomocą metryki biznesowe i środowiskiem pracy klienta. 

Można tworzyć zapytania, wszystkie wystąpienia dane telemetryczne i metryki nieprzetworzonych danych przechowywanych w portalu. Język zawiera filtr sprzężenia, agregacji i innych operacji. Można obliczyć pola i przeprowadzania analizy statystycznej. Ma graficznego i tabelaryczne wizualizacje.

![Wykres zapytań i wyniki analizy](./media/devops/0025.png)

Na przykład jest łatwe:

* Segmentowanie danych wydajności żądania aplikacji według warstwy klienta, aby zrozumieć ich środowisko pracy.
* Wyszukiwanie nazw zdarzenie niestandardowe lub konkretnych kodach błędów podczas badań działającej witryny.
* Przejść do szczegółów w użycie aplikacji w konkretnych klientów, aby zrozumieć sposób nabyte i przyjętych funkcji.
* Śledzenie sesji i najlepsze czasy odpowiedzi dla określonych użytkowników, zespoły pomocy technicznej i operacji do świadczenia błyskawicznej pomocy technicznej.
* Ustal, często używanej aplikacji funkcji w odpowiedzi na pytania priorytetyzacji funkcji.

Klient DNN powiedział: "Usługi application Insights podał Brak częścią równanie są możliwe do łączenia, sortowanie, zapytania i filtrowanie danych zgodnie z potrzebami. Co nasz zespół do znalezionych danych za pomocą zaawansowanego języka zapytań ma umożliwiło nam uzyskiwanie przydatnego wglądu i rozwiązywanie problemów za pomocą ich własnych ingenuity i środowisko nie zostało jeszcze wiemy, boss Said WE had. Wiele ciekawych odpowiedzi udzielane przez firmę pytania, począwszy od *"I wonder if...".* "

## <a name="development-tools-integration"></a>Integracja narzędzia tworzenia
### <a name="configuring-application-insights"></a>Konfigurowanie usługi Application Insights
Program Visual Studio i Eclipse ma narzędzia, aby skonfigurować poprawną pakiety zestawu SDK dla projektu, które tworzysz. Brak polecenia menu, aby dodać usługę Application Insights.

Jeśli masz się przy użyciu struktury rejestrowania śledzenia, takie jak narzędzia Log4N, NLog lub System.Diagnostics.Trace, wówczas masz do dyspozycji możliwość wysyłania dzienników do usługi Application Insights wraz z innymi telemetrii tak, aby umożliwi łatwe korelowanie śladów przy użyciu żądania, zależności wywołania i wyjątków.

### <a name="search-telemetry-in-visual-studio"></a>Wyszukaj telemetrię w programie Visual Studio
Podczas tworzenia i debugowania funkcji, można przeglądać i przeszukiwać dane telemetryczne bezpośrednio w programie Visual Studio przy użyciu tych samych obiektach wyszukiwania, tak jak w portalu sieci web.

A gdy usługa Application Insights rejestruje wyjątek, można wyświetlić punkt danych w programie Visual Studio i przejść bezpośrednio do odpowiedniego kodu.

![Wyszukiwania usługi Visual Studio](./media/devops/060.png)

Podczas debugowania, masz opcję, aby zachować dane telemetryczne w komputerze deweloperskim, wyświetlając go w programie Visual Studio, ale bez wysyłania go do portalu. Ta opcja lokalnego unika mieszanie debugowanie przy użyciu telemetrii produkcji.

### <a name="work-items"></a>Elementy robocze
Gdy zostanie zgłoszony alert, Application Insights może automatycznie tworzyć element roboczy w systemie śledzenia pracy.

## <a name="but-what-about"></a>Ale co...?
* [Prywatność i przechowywanie](../../azure-monitor/app/data-retention-privacy.md) -telemetrii jest przechowywana na serwerach bezpiecznej platformy Azure.
* Wydajność — wpływ jest bardzo niskie. Telemetria jest partii.
* [Cennik](../../azure-monitor/app/pricing.md) — pracę można rozpocząć bezpłatnie i że nadal podczas pracy w małą liczbą.


## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
Rozpoczynanie pracy z usługą Application Insights jest łatwe. Dostępne są następujące główne opcje:

* [Serwery IIS](../../azure-monitor/app/monitor-performance-live-website-now.md), a także [usługi Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrumentacja projektu podczas programowania. Można to zrobić [ASP.NET](../../azure-monitor/app/asp-net.md) lub [Java](../../azure-monitor/app/java-get-started.md) aplikacji, jak również [Node.js](../../azure-monitor/app/nodejs.md) i hostem [innych typów](../../azure-monitor/app/platforms.md). 
* Instrument [dowolnej strony sieci web](../../azure-monitor/app/javascript.md) , dodając krótki wstawki.

