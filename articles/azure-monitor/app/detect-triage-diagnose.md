---
title: Omówienie usługi Azure Application Insights dla metodyki DevOps | Microsoft Docs
description: Dowiedz się, jak korzystać z usługi Application Insights w środowisku DevOps.
author: mrbullwinkle
services: application-insights
documentationcenter: ''
manager: carmonm
ms.assetid: 6ccab5d4-34c4-4303-9d3b-a0f1b11e6651
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.custom: mvc
ms.topic: overview
ms.date: 09/06/2018
ms.author: mbullwin
ms.openlocfilehash: 45824ba93e86622b1bbd92aae01f18f89bee6adf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795349"
---
# <a name="overview-of-application-insights-for-devops"></a>Omówienie usługi Azure Application Insights dla metodyki DevOps

Dzięki usłudze [Application Insights](../../azure-monitor/app/app-insights-overview.md) możesz szybko dowiedzieć się, jaka jest wydajność aktywnej aplikacji i jak jest używana. Jeśli wystąpi problem, usługa powiadomi Cię o tym, pomoże ocenić skutki problemu i ustalić jego przyczynę.

Oto historia zespołu, który zajmuje się opracowywaniem aplikacji internetowych:

* *„Kilka dni temu wdrożyliśmy »drobną« poprawkę. Nie przeprowadziliśmy szerokiego testu i niestety w ładunku została scalona nieoczekiwana zmiana, co doprowadziło do niezgodności pomiędzy frontonem a zapleczem. Natychmiast lawinowo wystąpiły wyjątki serwera, został uruchomiony alert, a my zostaliśmy powiadomieni o sytuacji. Wystarczyło kilka kliknięć w portalu Application Insights, aby uzyskać ze stosu wywołań wyjątków informacje wystarczające do identyfikacji problemu. Niezwłocznie wycofaliśmy poprawkę i ograniczyliśmy szkody. Usługa Application Insights znacznie ułatwiła tę część cyklu DevOps i zapewniła nam swobodę działania”.*

W tym artykule poznamy zespół banku Fabrikam Bank, który opracowuje system bankowości internetowej, i dowiemy się, w jaki sposób korzysta on z usługi Application Insights, aby szybko reagować na potrzeby klientów i tworzyć aktualizacje.  

Zespół działa według cyklu DevOps przedstawionego na poniższej ilustracji:

![Cykl DevOps](./media/detect-triage-diagnose/00-devcycle.png)

Wymagania zostają wprowadzone na listę prac programistycznych (listę zadań). Członkowie zespołu pracują w krótkich przebiegach, często dostarczając działające oprogramowanie — zwykle w formie poprawek i rozszerzeń istniejącej aplikacji. Działająca aplikacja jest często aktualizowana w celu dodania nowych funkcji. Gdy działa, zespół monitoruje jej wydajność i użycie za pomocą usługi Application Insights. Te dane APM są ponownie wprowadzane na listę prac programistycznych.

Zespół korzysta z usługi Application Insights, aby uważnie monitorować aktywną aplikację internetową pod kątem następujących elementów:

* Wydajność. Chcą wiedzieć, jak różnią się czasy odpowiedzi w zależności od liczby żądań; w jakim stopniu wykorzystywany jest procesor, dysk i inne zasoby; który kod aplikacji zmniejsza wydajność; gdzie znajdują się wąskie gardła.
* Błędy. Jeśli pojawią się wyjątki lub żądania zakończone niepowodzeniem lub jeśli licznik wydajności wykroczy poza bezpieczny zakres, zespół musi się o tym dowiedzieć jak najszybciej, aby podjąć odpowiednie działania.
* Użycie. Po udostępnieniu nowych funkcji zespół chce wiedzieć, w jakim stopniu są używane i czy użytkownicy mają trudności z ich obsługą.

Skupmy się na części cyklu dotyczącej informacji zwrotnej:

![Wykrycie — klasyfikacja — diagnoza](./media/detect-triage-diagnose/01-pipe1.png)

## <a name="detect-poor-availability"></a>Wykrywanie niskiej dostępności
Marcela Markova jest starszym deweloperem w zespole ds. systemu bankowości internetowej i jest główną osobą monitorującą wydajność w trybie online. Skonfigurowała kilka [testów dostępności](../../azure-monitor/app/monitor-web-app-availability.md):

* Test pojedynczego adresu URL dla głównej strony docelowej aplikacji, http://fabrikambank.com/onlinebanking/. Ustawia kryteria kodu HTTP 200 i tekst „Witaj!”. Jeśli test zakończy się niepowodzeniem, powodem może być poważna awaria sieci lub serwerów albo problem z wdrożeniem. Ewentualnie ktoś mógł zmienić komunikat „Witaj!” na stronie bez jej wiedzy.
* Pogłębiony kilkustopniowy test, obejmujący zalogowanie i pobranie aktualnej listy kont oraz sprawdzenie określonych kluczowych szczegółów na każdej stronie. Ten test sprawdza, czy działa link do bazy danych kont. Marcela korzysta z fikcyjnego identyfikatora klienta: ma kilka do dyspozycji do celów testowych.

Po skonfigurowaniu tych testów Marcela jest pewna, że zespół szybko dowie się o ewentualnej awarii.  

Błędy wyświetlane są jako czerwone kropki na wykresie testu internetowego:

![Widok testów internetowych uruchomionych w okresie poprzedzającym](./media/detect-triage-diagnose/04-webtests.png)

Ale co ważniejsze, zespół deweloperów otrzymuje wiadomość e-mail z alertem dotyczącym wszelkich błędów. Dzięki temu wiedzą o nich wcześniej niż niemal wszyscy klienci.

## <a name="monitor-performance"></a>Monitorowanie wydajności
Na stronie Przegląd usługi Application Insights znajduje się wykres przedstawiający różne [kluczowe metryki](../../azure-monitor/app/web-monitor-performance.md).

![Zrzut ekranu przedstawiający wykresy wskaźników KPI w ramach przeglądu wydajności](./media/detect-triage-diagnose/overview-graphs.png)

Czas ładowania strony w przeglądarce jest uzyskiwany na podstawie danych telemetrycznych wysłanych bezpośrednio ze stron internetowych. Czas odpowiedzi serwera, liczba żądań serwera i liczba żądań zakończonych niepowodzeniem są mierzone na serwerze internetowym i wysyłane z niego do usługi Application Insights.

Marcela jest nieco zaniepokojona wykresem odpowiedzi serwera. Ten wykres pokazuje średni czas pomiędzy otrzymaniem przez serwer żądania HTTP z przeglądarki użytkownika a zwróceniem odpowiedzi. Zmienność na tym wykresie nie jest niczym niezwykłym, ponieważ obciążenie systemu może być różne. Jednak w tym przypadku wydaje się, że istnieje korelacja pomiędzy niewielkimi wzrostami liczby żądań a znacznym wydłużeniem czasu odpowiedzi. Może to wskazywać, że system niemal osiągnął swój limit wydajności.

Marcela otwiera wykresy serwerów:

![Różne metryki](./media/detect-triage-diagnose/002-servers.png)

Nic nie wskazuje, że występują ograniczenia zasobów, więc być może nagłe skoki na wykresach odpowiedzi serwera są przypadkowe.

## <a name="set-alerts-to-meet-goals"></a>Ustawianie alertów odpowiednio do celów
Niemniej jednak Marcela chce śledzić czas odpowiedzi. Jeśli będzie zbyt długi, chce natychmiast o tym wiedzieć.

Dlatego ustawia [alert](../../azure-monitor/app/metrics-explorer.md) dla czasów odpowiedzi, które są dłuższe niż typowy czas odpowiedzi. Dzięki temu ma pewność, że jeśli czasy odpowiedzi będą zbyt długie, dowie się o tym.

![Dodawanie bloku alertu](./media/detect-triage-diagnose/07-alerts.png)

Alerty można ustawiać na podstawie różnych innych metryk. Możesz na przykład otrzymać wiadomość e-mail, jeśli liczba wyjątków będzie zbyt duża, jeśli będzie zbyt mało wolnej pamięci lub jeśli nastąpi nagły wzrost liczby żądań klientów.

## <a name="stay-informed-with-smart-detection-alerts"></a>Bieżące informacje dzięki alertom inteligentnego wykrywania
Następnego dnia usługa Application Insights faktycznie wysyła wiadomość e-mail z alertem. Po otwarciu okazuje się jednak, że nie jest to alert czasu odpowiedzi, który ustawiła Marcela. Zamiast tego wiadomość informuje o nagłym wzroście liczby żądań zakończonych niepowodzeniem, czyli żądań, które zwróciły kody błędu 500 i wyższe.

Żądania zakończone niepowodzeniem to takie, w przypadku których użytkownicy zobaczyli błąd — zwykle w następstwie wyjątku zgłoszonego w kodzie. Mogą na przykład zobaczyć komunikat: „Niestety, nie można teraz zaktualizować danych”. Lub — jeszcze gorzej — na ekranie użytkownika może pojawić się zrzut stosu przekazany przez serwer.

Ten alert zaskakuje Marcelę, ponieważ gdy sprawdzała ostatnim razem, liczba żądań zakończonych niepowodzeniem była dobra, czyli niska. Na najbardziej aktywnych serwerach można spodziewać się pewnej niewielkiej liczby błędów.

Zaskoczyło ją to również dlatego, że nie musiała konfigurować tego alertu. Usługa Application Insights obejmuje również funkcję wykrywania inteligentnego. Automatycznie dostosowuje się ona do typowego wzorca błędów aplikacji i „przyzwyczaja się” do błędów na konkretnej stronie lub podczas dużego obciążenia lub w związku z innymi metrykami. Uruchamia alarm tylko wtedy, gdy nastąpi wzrost powyżej oczekiwanego poziomu.

![wiadomość e-mail zawierająca proaktywną diagnostykę](./media/detect-triage-diagnose/21.png)

Jest to bardzo przydatna wiadomość e-mail. Pełni nie tylko funkcję alarmową. Odpowiada również w znacznym stopniu za klasyfikację i diagnostykę.

Pokazuje, ilu klientów dotyczy problem i na jakich stronach internetowych lub podczas wykonywania jakich operacji występuje. Marcela może zdecydować, czy musi zebrać cały zespół pracujący nad projektem do gaszenia pożaru, czy też można odłożyć rozwiązanie problemu do kolejnego tygodnia.

Wiadomość e-mail pokazuje również, że wystąpił określony wyjątek, i co ciekawsze, że błąd jest związany z zakończonymi niepowodzeniem wywołaniami do określonej bazy danych. To wyjaśnia, dlaczego błąd wystąpił nagle, mimo że zespół Marceli nie wdrożył w ostatnim czasie żadnych aktualizacji.

Marcela po otrzymaniu tej wiadomości e-mail kontaktuje się z liderem zespołu ds. bazy danych. Dowiaduje się, że ten zespół w ciągu ostatnich 30 minut udostępnił poprawkę — i niestety mogła wystąpić niewielka zmiana schematu.

Zatem problem zaraz zostanie naprawiony, w ciągu 15 minut od jego wystąpienia, jeszcze przed sprawdzeniem dzienników. Marcela klika jednak link, aby otworzyć usługę Application Insights. Otwiera się od razu na żądaniu zakończonym niepowodzeniem i wyświetla zakończone niepowodzeniem wywołanie do bazy danych na skojarzonej liście wywołań zależności.

![żądanie zakończone niepowodzeniem](./media/detect-triage-diagnose/23.png)

## <a name="detect-exceptions"></a>Wykrywanie wyjątków
Po wykonaniu kilku prostych czynności konfiguracyjnych [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) są automatycznie zgłaszane do usługi Application Insights. Można je również rejestrować jawnie, wstawiając wywołania metody [TrackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) w kodzie:  

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


Zespół banku Fabrikam Bank wypracował zwyczaj wysyłania danych telemetrycznych dotyczących wszystkich wyjątków, o ile problem nie został na pewno rozwiązany.  

Tak naprawdę ta strategia ma jeszcze szerszy zakres: dane telemetryczne są wysyłane zawsze, gdy użytkownikowi nie udaje się zrobić tego, co zamierzał, bez względu na to, czy jest to związane z wyjątkiem w kodzie, czy nie. Na przykład jeśli zewnętrzny system przelewów międzybankowych zwróci komunikat „Nie można ukończyć tej transakcji” z jakichkolwiek przyczyn operacyjnych (nie z winy klienta), to zdarzenie jest śledzone.

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

Metoda TrackException jest używana do zgłaszania wyjątków, ponieważ wysyła kopię stosu. Funkcja TrackEvent jest używana do zgłaszania innych zdarzeń. Możesz dołączyć wszelkie właściwości, które mogą być przydatne w procesie diagnozowania.

Wyjątki i zdarzenia pojawiają się w bloku [Wyszukiwanie diagnostyczne](diagnostic-search.md). Możesz przejść do szczegółów, aby wyświetlić dodatkowe właściwości i ślad stosu.

![W wyszukiwaniu diagnostycznym za pomocą filtrów można wyświetlić określone typy danych.](./media/detect-triage-diagnose/appinsights-333facets.png)


## <a name="monitor-proactively"></a>Aktywne monitorowanie
Marcela nie chce tylko siedzieć bezczynnie i czekać na alerty. Wkrótce po każdym ponownym wdrożeniu zapoznaje się z [czasami odpowiedzi](../../azure-monitor/app/web-monitor-performance.md) — zarówno z ogólną wartością, jak i z tabelą najwolniejszych żądań i liczbą wyjątków.  

![Wykres czasu odpowiedzi i siatka czasu odpowiedzi serwera.](./media/detect-triage-diagnose/response-time.png)

Może ocenić wpływ każdego wdrożenia na wydajność, zwykle porównując dany tydzień z poprzednim. W przypadku nagłego pogorszenia wyników zgłasza to deweloperom.

## <a name="triage-issues"></a>Klasyfikacja problemów
Klasyfikacja — ocena ważności i zakresu problemu — to pierwszy etap postępowania po jego wykryciu. Czy powinniśmy zwołać zespół o północy? Czy może lepiej poczekać do kolejnej dogodnej przerwy na liście prac? Podczas klasyfikacji należy zadać kilka kluczowych pytań.

Jak często występuje problem? Wykresy w bloku Przegląd przedstawiają ogólną sytuację. Na przykład pewnej nocy aplikacja banku Fabrikam wygenerowała cztery alerty testu internetowego. Przyglądając się wykresowi następnego ranka, zespół mógł zaobserwować, że faktycznie wystąpiło kilka czerwonych kropek, ale większość testów miała wynik zielony. Po przejściu do szczegółów wykresu dostępności stało się jasne, że wszystkie sporadycznie występujące problemy pochodziły z jednej testowanej lokalizacji. Był to oczywiście problem z siecią, który miał wpływ tylko na jedną trasę i najprawdopodobniej rozwiązałby się sam.  

Z kolei poważny i stabilny wzrost na wykresie liczby wyjątków lub czasów odpowiedzi to oczywiście powód do większego niepokoju.

Przydatną taktyką klasyfikacji jest taktyka Spróbuj sam. Jeśli napotkasz ten sam problem, będzie wiadomo, że jest prawdziwy.

Jakiej części użytkowników dotyczy problem? Aby uzyskać przybliżoną odpowiedź, podziel liczbę błędów przez liczbę sesji.

Jeśli czas odpowiedzi jest długi, porównaj tabelę najwolniej odpowiadających żądań z częstotliwością korzystania z każdej strony.

Jak ważny jest zablokowany scenariusz? Jeśli jest to problem z funkcjonalnością, który blokuje konkretny scenariusz użytkownika, to czy ma to duże znaczenie? Jeśli klient nie może zapłacić rachunku, jest to poważne. Jeśli jednak nie może zmienić preferencji koloru ekranu, problem może poczekać. Szczegóły każdego zdarzenia lub wyjątku oraz tożsamość wolno działającej strony wskazują, gdzie klienci napotykają problemy.

## <a name="diagnose-issues"></a>Diagnozowanie problemów
Diagnostyka to nie to samo co debugowanie. Zanim zaczniesz przeglądać kod, musisz mieć ogólne pojęcie dlaczego, gdzie i kiedy pojawia się problem.

**Kiedy się to dzieje?** Widok historyczny zapewniany przez wykresy zdarzeń i metryk ułatwia zaobserwowanie korelacji pomiędzy skutkami a możliwymi przyczynami. Jeśli czas odpowiedzi lub liczba wyjątków sporadycznie nagle wzrasta, przyjrzyj się bliżej liczbie żądań: jeśli maksymalna wartość jest osiągana w tym samym momencie, to problem może być związany z zasobami. Czy trzeba przypisać więcej mocy procesora lub pamięci? Czy chodzi o to, że określona zależność nie jest w stanie zarządzać obciążeniem?

**Czy problem leży po naszej stronie?**  Jeśli zaobserwujesz nagły spadek wydajności danego typu żądania — na przykład gdy użytkownik chce uzyskać wyciąg z konta — istnieje możliwość, że źródłem problemu może być zewnętrzny podsystem, a nie Twoja aplikacja internetowa. W Eksploratorze metryk zaznacz wskaźniki błędów zależności oraz czasów trwania zależności i porównaj ich historie w ciągu kilku ostatnich godzin lub dni z wykrytym problemem. Jeśli zmiany są skorelowane, oznacza to, że winę może ponosić zewnętrzny podsystem.  


![Wykresy niepowodzenia zależności i czasu trwania wywołań do zależności](./media/detect-triage-diagnose/11-dependencies.png)

Wolne działanie zależności czasem jest związane z problemami geolokalizacji. Fabrikam Bank korzysta z maszyn wirtualnych platformy Azure i okazało się, że nieumyślnie zlokalizowano serwer internetowy oraz serwer konta w różnych krajach. Osiągnięto znaczną poprawę, migrując jeden z nich.

**Co zrobiliśmy?** Jeśli wydaje się, że problem nie jest kwestią zależności i nie zawsze występował, został prawdopodobnie wywołany przez niedawną zmianę. Perspektywa historyczna, którą zapewniają wykresy metryk i zdarzeń, ułatwia odnalezienie korelacji pomiędzy gwałtownymi zmianami a wdrożeniami. To zawęża obszar poszukiwań przyczyny problemu. Aby określić, które wiersze w kodzie aplikacji spowodowały spowolnienie działania, należy włączyć narzędzie Application Insights Profiler. Zapoznaj się z artykułem [Profiling live Azure web apps with Application Insights](./../../azure-monitor/app/profiler.md) (Profilowanie aktywnych aplikacji internetowych platformy Azure za pomocą usługi Application Insights). Po włączeniu narzędzia Profiler zostanie wyświetlony ślad podobny do widocznego poniżej. W tym przykładzie można łatwo zauważyć, że to metoda *GetStorageTableData* spowodowała problem.  

![Ślad narzędzia App Insights Profiler](./media/detect-triage-diagnose/AppInsightsProfiler.png)

**Co się dzieje?** Niektóre problemy występują rzadko i wykrycie ich podczas testów w trybie offline może być trudne. Można jedynie próbować wychwycić błąd, kiedy wystąpi w aktywnej aplikacji. Można sprawdzić zrzuty stosu w raportach dotyczących wyjątków. Ponadto można zapisać wywołania śledzenia przy użyciu preferowanej struktury rejestrowania albo metody TrackTrace() lub TrackEvent().  

Fabrikam Bank miał sporadyczne problemy z przelewami pomiędzy kontami, ale tylko w przypadku niektórych rodzajów kont. Aby lepiej zrozumieć, co się dzieje, wstawiono wywołania metody TrackTrace() w kluczowych punktach kodu, dołączając rodzaj konta jako właściwość do każdego wywołania. To ułatwiło odnalezienie tylko tych śladów w wyszukiwaniu diagnostycznym. Dołączono również wartości parametrów jako właściwości i miary do śledzenia wywołań.

## <a name="respond-to-discovered-issues"></a>Reagowanie na wykryte problemy
Po zdiagnozowaniu problemu możesz stworzyć plan rozwiązania go. Być może należy wycofać ostatnią zmianę — a może wystarczy ją poprawić. Po wprowadzeniu poprawki usługa Application Insights informuje, czy działania były skuteczne.  

Odkąd zespół deweloperów Fabrikam Bank korzysta z usługi Application Insights, ma bardziej ustrukturyzowane podejście do pomiaru wydajności.

* Na stronie Przegląd w usłudze Application Insights zespół skonfigurował cele wydajności przy użyciu określonych miar.
* Od początku tworzenia aplikacji umieszczane są w niej miary wydajności, takie jak metryki, które mierzą postęp użytkownika przy użyciu „lejków”.  

## <a name="monitor-user-activity"></a>Monitorowanie działań użytkowników
Jeśli czas odpowiedzi jest na ogół prawidłowy i występuje niewiele wyjątków, zespół deweloperów może przejść do kwestii użyteczności. Może zastanawiać się, w jaki sposób poprawić doświadczenia użytkowników i jak zachęcić większą liczbę użytkowników do osiągania zamierzonych celów.

Usługi Application Insights można również użyć, aby dowiedzieć się, co użytkownicy robią z aplikacją. Gdy aplikacja działa już bez problemów, zespół chciałby się dowiedzieć, jakie funkcje są najbardziej popularne, co podoba się użytkownikom, a z czym mają problemy, i jak często wracają. To pomoże ustalić priorytety podczas planowania dalszej pracy. Zespół planuje mierzyć popularność poszczególnych funkcji w ramach cyklu tworzenia oprogramowania.

Na przykład podróż typowego użytkownika przez witrynę internetową tworzy widoczny „lejek”. Wielu klientów zapoznaje się z kosztami różnych typów pożyczek. Mniejsza liczba zdecyduje się na wypełnienie formularza wyceny. Spośród tych, którzy otrzymają wycenę, kilku zdecyduje się na zaciągnięcie pożyczki.

![Liczba wyświetleń strony](./media/detect-triage-diagnose/funnel.png)

Dzięki określeniu, na którym etapie rezygnuje największa liczba klientów, firma może wypracować metody przyciągnięcia większej liczby użytkowników do końcowej części lejka. W niektórych przypadkach może być to kwestia złego środowiska użytkownika — na przykład jeśli trudno znaleźć przycisk „Dalej” lub instrukcje nie są czytelne. Jest również bardzo prawdopodobne, że klienci opuszczają stronę z przyczyn ściśle biznesowych: być może oprocentowanie pożyczek jest zbyt wysokie.

Bez względu na powód dane pomagają zespołowi dowiedzieć się, co robią użytkownicy. Aby uzyskać bardziej szczegółowe informacje, można wstawić więcej wywołań śledzenia. Można użyć metody TrackEvent(), aby policzyć dowolne działania użytkownika, od szczegółowych informacji dotyczących pojedynczych kliknięć, po znaczące osiągnięcia, takie jak spłacenie pożyczki.

Zespół przyzwyczaja się do posiadania informacji na temat aktywności użytkowników. Teraz, opracowując nowe funkcje, planują sposób zbierania informacji zwrotnych na temat korzystania z nich. Od samego początku tworzenia funkcji umieszczają w niej wywołania śledzenia. Korzystają z informacji zwrotnych, aby poprawić działanie funkcji w każdym cyklu tworzenia oprogramowania.

[Dowiedz się więcej na temat śledzenia użycia](../../azure-monitor/app/usage-overview.md).

## <a name="apply-the-devops-cycle"></a>Stosowanie cyklu DevOps
W ten oto sposób jeden zespół korzysta z usługi Application Insights, aby nie tylko naprawiać pojedyncze błędy, ale też doskonalić cały cykl tworzenia oprogramowania. Mam nadzieję, że udało mi się podsunąć Ci kilka pomysłów na skorzystanie z usługi Application Insights w celu lepszego zarządzania wydajnością aplikacji.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
Możesz rozpocząć pracę na kilka sposobów, w zależności od właściwości aplikacji. Wybierz interesujący Cię temat:

* [Aplikacja internetowa platformy ASP.NET](../../azure-monitor/app/asp-net.md)
* [Aplikacja internetowa Java](../../azure-monitor/app/java-get-started.md)
* [Aplikacja internetowa Node.js](../../azure-monitor/app/nodejs.md)
* Już wdrożone aplikacje hostowane w usługach [IIS](../../azure-monitor/app/monitor-web-app-availability.md)
* [Azure](../../azure-monitor/app/app-insights-overview.md).
* [Strony internetowe](../../azure-monitor/app/javascript.md) — aplikacja jednostronicowa lub zwykła strona internetowa — używane samodzielnie lub jako dodatek do dowolnych opcji serwerowych.
* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) do testowania aplikacji w publicznym Internecie.
