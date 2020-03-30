---
title: Monitorowanie wydajności aplikacji sieci Web — usługa Azure Application Insights
description: Jak usługa Application Insights pasuje do cyklu devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669696"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Szczegółowa diagnostyka dla aplikacji internetowych i usług dzięki usłudze Application Insights
## <a name="why-do-i-need-application-insights"></a>Dlaczego potrzebuję usługi Application Insights?
Usługa Application Insights monitoruje uruchomiającą aplikację internetową. Informuje o awariach i problemach z wydajnością oraz pomaga analizować sposób korzystania z aplikacji przez klientów. Działa dla aplikacji działających na wielu platformach (ASP.NET, Java EE, Node.js, ...) i jest hostowany w chmurze lub lokalnie. 

![Aspekty złożoności dostarczania aplikacji internetowych](./media/devops/010.png)

Jest to niezbędne do monitorowania nowoczesnej aplikacji, gdy jest uruchomiona. Co najważniejsze, chcesz wykryć awarie, zanim większość klientów to zrobi. Chcesz również odkryć i rozwiązać problemy z wydajnością, które, choć nie katastrofalne, być może spowolnić lub spowodować pewne niedogodności dla użytkowników. A kiedy system działa w sposób zadowalający, chcesz wiedzieć, co użytkownicy robią z nim: Czy korzystają z najnowszej funkcji? Czy im się to udaje?

Nowoczesne aplikacje internetowe są opracowywane w cyklu ciągłego dostarczania: zwolnić nową funkcję lub poprawę; obserwować, jak dobrze działa dla użytkowników; plan następnego przyrostu rozwoju w oparciu o tę wiedzę. Kluczową częścią tego cyklu jest faza obserwacji. Usługa Application Insights udostępnia narzędzia do monitorowania aplikacji sieci web pod kątem wydajności i użycia.

Najważniejszym aspektem tego procesu jest diagnostyka i diagnoza. Jeśli aplikacja nie powiedzie się, a następnie działalność jest tracona. Główną rolą struktury monitorowania jest zatem niezawodne wykrywanie awarii, natychmiastowe powiadamianie i przedstawianie informacji potrzebnych do zdiagnozowania problemu. Jest to dokładnie to, co robi usługa Application Insights.

### <a name="where-do-bugs-come-from"></a>Skąd pochodzą błędy?
Awarie w systemach sieci web zazwyczaj wynikają z problemów z konfiguracją lub nieprawidłowych interakcji między ich wieloma składnikami. Pierwszym zadaniem podczas rozwiązywania incydentu na żywo miejscu jest zatem określenie miejsca problemu: który składnik lub relacja jest przyczyną?

Niektórzy z nas, ci z siwymi włosami, pamiętają prostszą epokę, w której program komputerowy działał na jednym komputerze. Deweloperzy będą go dokładnie przetestować przed wysyłką; i po wysłaniu go, rzadko zobaczyć lub pomyśleć o tym ponownie. Użytkownicy musieliby pogodzić się z resztkowymi błędami przez wiele lat. 

Teraz sprawy są bardzo różne. Aplikacja ma mnóstwo różnych urządzeń do uruchomienia i może być trudne do zagwarantowania dokładnie takie samo zachowanie na każdym z nich. Hosting aplikacji w chmurze oznacza, że błędy można szybko naprawić, ale oznacza to również ciągłą konkurencję i oczekiwanie nowych funkcji w częstych odstępach czasu. 

W tych warunkach jedynym sposobem, aby zachować kontrolę nad licznikiem błędów jest automatyczne testowanie jednostkowe. Niemożliwe byłoby ręczne ponowne przetestowanie wszystkiego przy każdej dostawie. Test jednostkowy jest teraz powszechną częścią procesu kompilacji. Narzędzia, takie jak xamarin test cloud pomocy, zapewniając automatyczne testowanie interfejsu użytkownika w wielu wersjach przeglądarki. Te systemy testowania pozwalają nam mieć nadzieję, że liczba błędów znalezionych w aplikacji może być ograniczona do minimum.

Typowe aplikacje sieci web mają wiele składników na żywo. Oprócz klienta (w aplikacji przeglądarki lub urządzenia) i serwera sieci web, istnieje prawdopodobieństwo, że istnieje znaczne przetwarzanie zaplecza. Być może zaplecze jest potok składników, lub luźniejsze kolekcji dzieł współpracujących. A wiele z nich nie będzie pod twoją kontrolą - są to usługi zewnętrzne, od których zależy.

W konfiguracjach takich jak te, może być trudne i nieekonomiczne do testowania lub przewidywać, każdy możliwy tryb awarii, inne niż w samym systemie na żywo. 

### <a name="questions-"></a>Pytania...
Kilka pytań, które zadajemy podczas opracowywania systemu internetowego:

* Czy moja aplikacja ulega awarii? 
* Co dokładnie się stało? - Jeśli nie udało się wniosek, chcę wiedzieć, jak to się tam stało. Potrzebujemy śladu wydarzeń...
* Czy moja aplikacja jest wystarczająco szybka? Jak długo trwa odpowiadanie na typowe żądania?
* Czy serwer może obsłużyć obciążenie? Kiedy liczba żądań wzrasta, czy czas odpowiedzi utrzymuje się na stałym poziomie?
* Jak responsywne są moje zależności — interfejsy API REST, bazy danych i inne składniki, które wywołuje moja aplikacja. W szczególności, jeśli system jest powolny, jest to mój składnik, czy dostaję powolne odpowiedzi od kogoś innego?
* Czy moja aplikacja jest w górę czy w dół? Czy można go zobaczyć z całego świata? Daj mi znać, jeśli się zatrzyma ....
* Co jest główną przyczyną? Czy błąd w moim składniku lub zależności? Czy jest to kwestia komunikacji?
* Ilu użytkowników ma wpływ? Jeśli mam więcej niż jeden problem do rozwiązania, który jest najważniejszy?

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
![Podstawowy przepływ pracy usługi Application Insights](./media/devops/020.png)

1. Usługa Application Insights instrumentów aplikacji i wysyła dane telemetryczne o tym, gdy aplikacja jest uruchomiona. Można utworzyć zestaw SDK usługi Application Insights w aplikacji lub można zastosować instrumentacji w czasie wykonywania. Pierwsza metoda jest bardziej elastyczna, ponieważ można dodać własne dane telemetryczne do modułów regularnych.
2. Dane telemetryczne są wysyłane do portalu usługi Application Insights, gdzie są przechowywane i przetwarzane. (Mimo że usługa Application Insights jest hostowana na platformie Microsoft Azure, może monitorować wszystkie aplikacje sieci Web — nie tylko aplikacje platformy Azure).
3. Dane telemetryczne są prezentowane w postaci wykresów i tabel zdarzeń.

Istnieją dwa główne typy danych telemetrycznych: zagregowane i nieprzetworzone wystąpienia. 

* Dane wystąpienia obejmują na przykład raport żądania odebranego przez aplikację sieci web. Szczegółowe informacje o żądaniu można znaleźć i sprawdzić za pomocą narzędzia wyszukiwania w portalu usługi Application Insights. Wystąpienie będzie zawierać dane, takie jak czas aplikacji trwało, aby odpowiedzieć na żądanie, a także żądany adres URL, przybliżoną lokalizację klienta i inne dane.
* Zagregowane dane obejmują liczbę zdarzeń na jednostkę czasu, dzięki czemu można porównać szybkość żądań z czasem odpowiedzi. Zawiera również średnie metryki, takie jak czas odpowiedzi żądania.

Główne kategorie danych to:

* Żądania do aplikacji (zwykle żądania HTTP), z danymi na adres URL, czas odpowiedzi i sukces lub niepowodzenie.
* Zależności — rest i SQL połączeń przez aplikację, również z identyfikatorem URI, czas odpowiedzi i sukces
* Wyjątki, w tym ślady stosu.
* Dane widoku strony, które pochodzą z przeglądarek użytkowników.
* Metryki, takie jak liczniki wydajności, a także metryki, które piszesz samodzielnie. 
* Zdarzenia niestandardowe, których można używać do śledzenia zdarzeń biznesowych
* Dziennik ślady używane do debugowania.

## <a name="case-study-real-madrid-fc"></a>Studium przypadku: Real Madryt F.C.
Serwis internetowy [Real Madrid Football Club](https://www.realmadrid.com/) obsługuje około 450 milionów fanów na całym świecie. Fani uzyskują do niego dostęp zarówno za pośrednictwem przeglądarek internetowych, jak i aplikacji mobilnych Klubu. Fani nie tylko rezerwują bilety, ale także uzyskują dostęp do informacji i klipów wideo o wynikach, graczach i nadchodzących grach. Mogą wyszukiwać za pomocą filtrów, takich jak liczba strzelonych bramek. Istnieją również linki do mediów społecznościowych. Środowisko użytkownika jest wysoce spersonalizowane i zostało zaprojektowane jako dwukierunkowa komunikacja, aby zaangażować fanów.

Rozwiązaniem [jest system usług i aplikacji na platformie Microsoft Azure.](https://www.microsoft.com/inculture/sports/real-madrid/) Skalowalność jest kluczowym wymogiem: ruch jest zmienny i może osiągnąć bardzo duże ilości podczas i wokół meczów.

Dla Realu Madryt ważne jest monitorowanie wydajności systemu. Usługa Azure Application Insights zapewnia kompleksowy widok w całym systemie, zapewniając niezawodny i wysoki poziom usług. 

Klub dostaje również dogłębne zrozumienie swoich fanów: gdzie są (tylko 3% są w Hiszpanii), jakie zainteresowanie mają w graczy, wyniki historyczne, i nadchodzących gier, i jak reagują na wyniki meczu.

Większość tych danych telemetrycznych jest automatycznie zbierana bez dodatkowego kodu, co uprościło rozwiązanie i zmniejszyło złożoność operacyjną.  W przypadku Realu Madryt usługa Application Insights zajmuje się 3,8 miliarda punktów telemetrycznych miesięcznie.

Real Madryt używa modułu usługi Power BI do wyświetlania ich danych telemetrycznych.

![Widok usługi Power BI danych telemetrycznych usługi Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Inteligentne wykrywanie
[Proaktywna diagnostyka](../../azure-monitor/app/proactive-diagnostics.md) jest najnowszą funkcją. Bez żadnej specjalnej konfiguracji przez ciebie usługa Application Insights automatycznie wykrywa i ostrzega o nietypowych wzrostach wskaźników awarii w aplikacji. Jest wystarczająco inteligentny, aby zignorować tło okazjonalnych awarii, a także wzrosty, które są po prostu proporcjonalne do wzrostu liczby żądań. Na przykład, jeśli wystąpi błąd w jednej z usług, od których zależy, lub jeśli nowa kompilacja, którą właśnie wdrożyłeś, nie działa tak dobrze, dowiesz się o tym, gdy tylko spojrzysz na swój adres e-mail. (I są webhooks, dzięki czemu można wywołać inne aplikacje.)

Inny aspekt tej funkcji wykonuje codzienną dogłębną analizę danych telemetrycznych, szukając nietypowych wzorców wydajności, które są trudne do wykrycia. Na przykład można znaleźć powolną wydajność skojarzoną z określonym obszarem geograficznym lub z określoną wersją przeglądarki.

W obu przypadkach alert nie tylko informuje o wykrytych objawach, ale także udostępnia dane potrzebne do zdiagnozowania problemu, takie jak odpowiednie raporty wyjątków.

![Wiadomości e-mail z proaktywnej diagnostyki](./media/devops/030.png)

Klient Samtec powiedział: "Podczas ostatniej przecięcia funkcji znaleźliśmy niedostatekową bazę danych, która osiągała limity zasobów i powodowała przesuń limity czasu. Proaktywne alerty wykrywania przyszedł dosłownie jak byliśmy triaging problem, bardzo blisko w czasie rzeczywistym, jak reklamowane. Ten alert w połączeniu z alertami platformy Azure pomógł nam niemal natychmiast rozwiązać problem. Całkowity czas przestoju < 10 minut".

## <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo
Wdrażanie najnowszej kompilacji może być niespokojnym doświadczeniem. Jeśli są jakieś problemy, chcesz wiedzieć o nich od razu, dzięki czemu można wycofać się w razie potrzeby. Live Metrics Stream zapewnia kluczowe metryki z opóźnieniem około jednej sekundy.

![Dane na żywo](./media/devops/0040.png)

I pozwala natychmiast sprawdzić próbkę wszelkich błędów lub wyjątków.

![Zdarzenia awarii na żywo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikacji
Mapa aplikacji automatycznie odnajduje topologię aplikacji, kładąc na niej informacje o wydajności, aby umożliwić łatwą identyfikację wąskich gardeł wydajności i problematycznych przepływów w rozproszonym środowisku. Umożliwia odnajdywanie zależności aplikacji w usługach platformy Azure. Można klasyfikować problem, rozumiejąc, czy jest związany z kodem lub zależności związane i z jednego miejsca drąży do powiązanych środowiska diagnostycznego. Na przykład aplikacja może być niepowodzeniem z powodu pogorszenia wydajności w warstwie SQL. Dzięki mapie aplikacji można ją natychmiast wyświetlić i przejść do szczegółów w programie SQL Index Advisor lub w aplikacji Query Insights.

![Mapa aplikacji](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Analiza wglądu w aplikacje
Za pomocą analytics można pisać dowolne zapytania w zaawansowanym języku sql.With [Analytics](../../azure-monitor/app/analytics.md), you can write arbitrary queries in a powerful SQL-like language.  Diagnozowanie całego stosu aplikacji staje się łatwe, ponieważ różne perspektywy są połączone i można zadać odpowiednie pytania, aby skorelować wydajność usługi z metrykami biznesowymi i obsługą klienta. 

Można zbadać wszystkie wystąpienia telemetrii i metryki nieprzetworzonych danych przechowywanych w portalu. Język zawiera filtr, sprzężenie, agregację i inne operacje. Można obliczać pola i przeprowadzać analizę statystyczną. Istnieją zarówno tabelaryczne, jak i graficzne wizualizacje.

![Wykres zapytań i wyników analizy](./media/devops/0025.png)

Na przykład, łatwo jest:

* Segment danych wydajności żądania aplikacji według warstw klientów, aby zrozumieć ich środowisko.
* Wyszukaj określone kody błędów lub niestandardowe nazwy zdarzeń podczas badania witryny na żywo.
* Przejdź do szczegółów użycia aplikacji określonych klientów, aby zrozumieć, w jaki sposób funkcje są nabywane i wdrażane.
* Śledź sesje i czasy odpowiedzi dla określonych użytkowników, aby umożliwić zespołom pomocy technicznej i operacyjnemu zapewnienie natychmiastowej obsługi klienta.
* Określ często używane funkcje aplikacji, aby odpowiedzieć na pytania dotyczące priorytetów funkcji.

Klient DNN powiedział: "Usługa Application Insights dostarczyła nam brakującą część równania za możliwość łączenia, sortowania, wykonywania zapytań i filtrowania danych w razie potrzeby. Umożliwienie naszemu zespołowi korzystania z własnej pomysłowości i doświadczenia w znalezieniu danych za pomocą potężnego języka zapytań pozwoliło nam znaleźć spostrzeżenia i rozwiązać problemy, o których nawet nie wiedzieliśmy. Wiele ciekawych odpowiedzi pochodzi z pytań zaczyna nych od *"Zastanawiam się, czy...".*

## <a name="development-tools-integration"></a>Integracja narzędzi programistycznych
### <a name="configuring-application-insights"></a>Konfigurowanie statystyk aplikacji
Visual Studio i Eclipse mają narzędzia do konfigurowania poprawnych pakietów zestawu SDK dla projektu, który tworzysz. Istnieje polecenie menu, aby dodać usługi Application Insights.

Jeśli zdarzy ci się używać struktury rejestrowania śledzenia, takiej jak Log4N, NLog lub System.Diagnostics.Trace, pojawi się opcja wysyłania dzienników do usługi Application Insights wraz z innymi telemetriami, dzięki czemu można łatwo skorelować ślady z żądaniami, zależnością połączeń i wyjątków.

### <a name="search-telemetry-in-visual-studio"></a>Wyszukiwanie danych telemetrycznych w programie Visual Studio
Podczas tworzenia i debugowania funkcji, można wyświetlić i przeszukiwać dane telemetryczne bezpośrednio w programie Visual Studio, przy użyciu tych samych funkcji wyszukiwania, jak w portalu sieci web.

A gdy usługa Application Insights rejestruje wyjątek, można wyświetlić punkt danych w programie Visual Studio i przejść bezpośrednio do odpowiedniego kodu.

![Wyszukiwanie w programie Visual Studio](./media/devops/060.png)

Podczas debugowania, masz możliwość zachowania danych telemetrycznych na komputerze deweloperskim, wyświetlanie go w programie Visual Studio, ale bez wysyłania go do portalu. Ta opcja lokalna pozwala uniknąć mieszania debugowania z danemi telemetrycznymi produkcji.

### <a name="work-items"></a>Elementy pracy
Po wywołaniu alertu usługa Application Insights może automatycznie utworzyć element roboczy w systemie śledzenia pracy.

## <a name="but-what-about"></a>Ale co...?
* [Prywatność i pamięć masowa](../../azure-monitor/app/data-retention-privacy.md) — dane telemetryczne są przechowywane na bezpiecznych serwerach platformy Azure.
* Wydajność - wpływ jest bardzo niski. Dane telemetryczne są wsadowe.
* [Ceny](../../azure-monitor/app/pricing.md) — możesz rozpocząć pracę za darmo, a to trwa, gdy jesteś w niskiej objętości.


## <a name="video"></a>Film wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
Rozpoczęcie pracy z usługą Application Insights jest łatwe. Główne opcje to:

* [serwery usług IIS](../../azure-monitor/app/monitor-performance-live-website-now.md), a także dla [usługi Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrument projektu podczas rozwoju. Można to zrobić dla [aplikacji ASP.NET](../../azure-monitor/app/asp-net.md) lub [Java,](../../azure-monitor/app/java-get-started.md) a także [Node.js](../../azure-monitor/app/nodejs.md) i wielu [innych typów](../../azure-monitor/app/platforms.md). 
* Instrument [każdej strony internetowej](../../azure-monitor/app/javascript.md) przez dodanie krótkiego fragmentu kodu.

