---
title: Anomalie wykrywania inteligentnego, w Application Insights | Microsoft Docs
description: Ostrzega o nietypowych zmianach w przypadku żądań zakończonych niepowodzeniem w aplikacji sieci Web i umożliwia analizę diagnostyki. Nie jest wymagana żadna konfiguracja.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820591"
---
# <a name="smart-detection---failure-anomalies"></a>Wykrywanie inteligentne — anomalie błędów
[Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie powiadamia o niemal rzeczywistym czasie, jeśli aplikacja sieci Web napotka nietypowy wzrost liczby żądań zakończonych niepowodzeniem. Wykrywa nietypowy wzrost liczby żądań HTTP lub wywołań zależności zgłaszanych jako zakończone niepowodzeniem. W przypadku żądań żądania zakończone niepowodzeniem są zwykle takie same jak kody odpowiedzi o wartości 400 lub wyższej. Aby ułatwić Klasyfikacja i zdiagnozowanie problemu, analiza charakterystyk błędów i powiązane dane telemetryczne są dostępne w powiadomieniu. Istnieją także linki do portalu Application Insights w celu przeprowadzenia dalszej diagnostyki. Ta funkcja nie wymaga konfigurowania ani konfiguracji, ponieważ używa algorytmów uczenia maszynowego do przewidywania normalnego współczynnika niepowodzeń.

Ta funkcja działa w przypadku dowolnej aplikacji sieci Web, hostowanej w chmurze lub na własnych serwerach, która generuje żądanie lub dane telemetryczne zależności — na przykład jeśli masz rolę procesu roboczego, która wywołuje [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) lub [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Po skonfigurowaniu [Application Insights dla projektu](../../azure-monitor/app/app-insights-overview.md), gdy aplikacja generuje pewną minimalną ilość danych telemetrycznych, Inteligentne wykrywanie anomalii awarii trwa 24 godziny, aby poznać normalne zachowanie aplikacji, zanim zostanie ona przełączona i może wysłać alerty.

Oto przykładowy alert.

![Przykładowy alert wykrywania inteligentnego pokazujący niepowodzenie analizy klastra](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Domyślnie otrzymujesz krótszy format wiadomości e-mail niż ten przykład. Można jednak [przejść do tego formatu szczegółowego](#configure-alerts).
>
>

Zwróć uwagę, że:

* Współczynnik błędów w porównaniu z normalnym zachowaniem aplikacji.
* Liczba użytkowników, których to dotyczy, aby wiedzieć, jak się martwić.
* Wzorzec charakterystyczny skojarzony z błędami. W tym przykładzie istnieje konkretny kod odpowiedzi, nazwa żądania (operacja) i wersja aplikacji. To natychmiast informuje o tym, gdzie rozpocząć wyszukiwanie kodu. Inne możliwości mogą być konkretnymi przeglądarkami lub systemami operacyjnymi klienta.
* Wyjątek, ślady dziennika i awaria zależności (bazy danych lub inne składniki zewnętrzne), które są wyświetlane do skojarzenia z błędami.
* Linki bezpośrednio do odpowiednich wyszukiwań w danych telemetrycznych w Application Insights.

## <a name="failure-anomalies-v2"></a>Anomalie niepowodzenia w wersji 2
Jest teraz dostępna nowa wersja reguły alertu dotyczącego anomalii z błędami. Ta nowa wersja jest uruchomiona na nowej platformie Azure Alerting i wprowadza różne ulepszenia w porównaniu do istniejącej wersji.

### <a name="whats-new-in-this-version"></a>Co nowego w tej wersji?
- Szybsze wykrywanie problemów
- Bogatszy zestaw akcji — reguła alertu jest tworzona za pomocą skojarzonej [grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) o nazwie "Application Insights Smart Detection", która zawiera akcje poczty e-mail i elementu webhook, i można ją rozszerzyć, aby wyzwolić dodatkowe akcje po uruchomieniu alertu.
- Więcej ukierunkowanych powiadomień — powiadomienia E-mail wysyłane z tej reguły alertów są teraz domyślnie wysyłane do użytkowników skojarzonych z czytnikiem monitorowania subskrypcji i monitorowaniem ról współautor. Więcej informacji na ten temat można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- Łatwiejsza konfiguracja za pomocą szablonów ARM — Zobacz przykład [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Obsługa typowych schematów alertów — powiadomienia wysyłane z tej reguły alertu są zgodne ze [wspólnym schematem alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Ujednolicony szablon wiadomości e-mail — powiadomienia E-mail od tej reguły alertu mają spójny wygląd & działania z innymi typami alertów. Po tej zmianie opcja uzyskiwania alertów o anomalii błędów ze szczegółowymi informacjami diagnostycznymi nie jest już dostępna.

### <a name="how-do-i-get-the-new-version"></a>Jak mogę pobrać nowej wersji?
- Nowo utworzone zasoby Application Insights są teraz obsługiwane z nową wersją reguły alertu dotyczącego anomalii awarii.
- Istniejące zasoby Application Insights w klasycznej wersji reguły alertów o niepowodzeniach niepowodzeń otrzymają nową wersję, gdy ich subskrypcja hostingu zostanie zmigrowana do nowej platformy alertów w ramach procesu wycofywania [alertów klasycznych](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> Nowa wersja reguły alertu dotyczącego anomalii awarii pozostaje bezpłatna. Ponadto akcje poczty e-mail i elementu webhook wyzwalane przez skojarzoną grupę akcji "Application Insights inteligentnego wykrywania" również są bezpłatne.
> 
> 

## <a name="benefits-of-smart-detection"></a>Zalety wykrywania inteligentnego
Zwykłe [alerty dotyczące metryk](../../azure-monitor/app/alerts.md) informują o problemie. Jednak funkcja inteligentnego wykrywania uruchamia pracę diagnostykę, wykonując wiele analiz, które w przeciwnym razie trzeba wykonać samodzielnie. Wyniki są zapakowane w sposób uporządkowany, co pomaga szybko uzyskać dostęp do katalogu głównego problemu.

## <a name="how-it-works"></a>Zasady działania
Inteligentne wykrywanie monitoruje dane telemetryczne odebrane z Twojej aplikacji, a w szczególności współczynnik awarii. Ta zasada zlicza liczbę żądań, dla których właściwość `Successful request` ma wartość false, oraz liczbę wywołań zależności, dla których właściwość `Successful call` ma wartość false. W przypadku żądań domyślnie `Successful request == (resultCode < 400)` (chyba że kod niestandardowy nie został zapisany do [filtrowania](../../azure-monitor/app/api-filtering-sampling.md#filtering) lub generowania własnych wywołań [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ). 

Wydajność aplikacji ma typowy wzorzec zachowania. Niektóre żądania lub wywołania zależności będą bardziej podatne na awarie niż inne; a ogólna częstotliwość niepowodzeń może być większa w miarę wzrostu obciążenia. Funkcja inteligentnego wykrywania używa uczenia maszynowego, aby znaleźć te anomalie.

Funkcja inteligentnego wykrywania porównuje bieżące zachowanie z wzorcami widocznymi w ciągu ostatnich kilku dni jako dane telemetryczne w Application Insights z aplikacji sieci Web. W przypadku nienormalnego wzrostu współczynnika błędów w porównaniu z poprzednią wydajnością jest wyzwalana analiza.

Po wyzwoleniu analizy usługa wykonuje analizę klastra dla żądania zakończonego niepowodzeniem, aby spróbować zidentyfikować wzorzec wartości, które opisują błędy. W powyższym przykładzie analiza wykryła, że większość błędów dotyczy określonego kodu wynikowego, nazwy żądania, hosta adresu URL serwera i wystąpienia roli. Z drugiej strony analiza wykryła, że właściwość systemu operacyjnego klienta jest dystrybuowana na wiele wartości i dlatego nie jest wyświetlana na liście.

Gdy usługa jest Instrumentacją tych wywołań telemetrycznych, Analizator szuka wyjątku i awarii zależności, które są skojarzone z żądaniami w klastrze, który zidentyfikował, wraz z przykładem wszystkich dzienników śledzenia skojarzonych z tymi żądaniami.

Wyniki analizy są wysyłane do Ciebie jako alert, chyba że skonfigurowano go w taki sposób, aby nie został skonfigurowany.

Podobnie jak w [przypadku alertów ustawionych ręcznie](../../azure-monitor/app/alerts.md), można sprawdzić stan alertu i skonfigurować go w bloku alerty zasobu Application Insights. Ale w przeciwieństwie do innych alertów nie trzeba konfigurować ani konfigurować inteligentnego wykrywania. Jeśli chcesz, możesz ją wyłączyć lub zmienić docelowe adresy e-mail.

### <a name="alert-logic-details"></a>Szczegóły logiki alertu

Alerty są wyzwalane przez swój własny algorytm uczenia maszynowego, dlatego nie można udostępnić dokładnych szczegółów implementacji. Dzięki temu wiemy, że czasami trzeba dowiedzieć się więcej o tym, jak działa podstawowa logika. Podstawowe czynniki, które są oceniane, aby określić, czy alert powinien być wyzwalany: 

* Analiza procentu niepowodzenia żądań/zależności w przedziale czasu na 20 minut.
* Porównanie procentu niepowodzenia z ostatnich 20 minut z szybkością w ciągu ostatnich 40 minut i ostatnich siedmiu dni i wyszukiwanie znaczących odchyleń, które przekraczają X-Times w przypadku odchylenia standardowego.
* Użycie limitu adaptacyjnego dla procentu minimalnej awarii, który różni się w zależności od liczby żądań/zależności aplikacji.

## <a name="configure-alerts"></a>Konfigurowanie alertów
Można wyłączyć wykrywanie inteligentne, zmienić adresatów wiadomości e-mail, utworzyć element webhook lub wyrazić zgodę na bardziej szczegółowe komunikaty alertów.

Otwórz stronę alerty. Anomalie błędów są uwzględniane wraz z alertami, które zostały ustawione ręcznie, i można sprawdzić, czy jest ona aktualnie w stanie alertu.

![Na stronie Przegląd kliknij kafelek alerty. Lub na dowolnej stronie metryk kliknij przycisk alerty.](./media/proactive-failure-diagnostics/021.png)

Kliknij alert, aby go skonfigurować.

![Konfigurowanie](./media/proactive-failure-diagnostics/032.png)

Należy zauważyć, że można wyłączyć wykrywanie inteligentne, ale nie można go usunąć (lub utworzyć innego).

#### <a name="detailed-alerts"></a>Szczegółowe alerty
W przypadku wybrania opcji "Uzyskaj bardziej szczegółową diagnostykę" wiadomość e-mail będzie zawierać więcej informacji diagnostycznych. Czasami będzie można zdiagnozować problem bezpośrednio z danych w wiadomości e-mail.

Istnieje niewielkie ryzyko, że bardziej szczegółowy alert może zawierać informacje poufne, ponieważ zawiera komunikaty o wyjątkach i śledzeniu. Jednak może się to zdarzyć tylko wtedy, gdy kod zezwolił na dostęp do tych komunikatów poufnych informacji.

## <a name="triaging-and-diagnosing-an-alert"></a>Segregowania i diagnozowanie alertu
Alert wskazuje, że wykryto nietypowy wzrost liczby żądań zakończonych niepowodzeniem. Prawdopodobnie wystąpił problem z aplikacją lub jej środowiskiem.

Od procentu żądań i liczby użytkowników, których to dotyczy, możesz zdecydować, jak pilnuje problem. W powyższym przykładzie Współczynnik awaryjności wynoszący 22,5% jest porównywany z normalną szybkością 1%, co oznacza, że coś się nie dzieje. Z drugiej strony dotyczyły tylko 11 użytkowników. Jeśli aplikacja została zadana, możesz ocenić, jak poważny jest.

W wielu przypadkach będzie możliwe szybkie zdiagnozowanie problemu przy użyciu nazwy żądania, wyjątku, błędu zależności i danych śledzenia.

Istnieją pewne inne wskazówki. Na przykład współczynnik błędów zależności w tym przykładzie jest taki sam jak współczynnik wyjątku (89,3%). Sugeruje to, że wyjątek występuje bezpośrednio z powodu błędu zależności — w celu uzyskania jasnego pomysłu, gdzie rozpocząć wyszukiwanie w kodzie.

Aby dokładniej zbadać, linki w poszczególnych sekcjach przeprowadzą Cię bezpośrednio do [strony wyszukiwania](../../azure-monitor/app/diagnostic-search.md) odfiltrowanej do odpowiednich żądań, wyjątków, zależności lub śladów. Lub możesz otworzyć [Azure Portal](https://portal.azure.com), przejdź do zasobu Application Insights dla aplikacji, a następnie otwórz blok błędy.

W tym przykładzie kliknięcie linku "Wyświetl szczegóły błędów zależności" spowoduje otwarcie bloku wyszukiwania Application Insights. Pokazuje instrukcję SQL, która ma przykład głównej przyczyny: wartości NULL zostały podane w obowiązkowych polach i nie przeszły walidacji podczas operacji zapisywania.

![Wyszukiwanie diagnostyczne](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Przejrzyj Ostatnie alerty

Kliknij pozycję **Inteligentne wykrywanie** , aby przejść do najnowszego alertu:

![Podsumowanie alertów](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Jaka jest różnica...
Inteligentne wykrywanie anomalii o awarii uzupełnia inne podobne, ale odrębne funkcje Application Insights.

* [Alerty metryk](../../azure-monitor/app/alerts.md) są ustawiane przez użytkownika i mogą monitorować szeroką gamę metryk, takich jak użycie procesora CPU, stawki żądań, czasy ładowania stron itd. Można z nich skorzystać, aby ostrzec użytkownika, na przykład jeśli trzeba dodać więcej zasobów. Z kolei Inteligentne wykrywanie anomalii dotyczących awarii obejmuje niewielki zakres metryk krytycznych (obecnie jest to liczba żądań zakończonych niepowodzeniem), które są przeznaczone do powiadamiania niemal w czasie rzeczywistym, gdy wskaźnik niepomyślnych żądań aplikacji sieci Web znacznie się zwiększy w porównaniu z aplikacją sieci Web normalne zachowanie.

    Inteligentne wykrywanie automatycznie dostosowuje swój próg w reakcji na warunki panujące.

    Funkcja inteligentnego wykrywania uruchamia pracę diagnostyki.
* [Inteligentne wykrywanie anomalii wydajności](proactive-performance-diagnostics.md) korzysta również z analizy maszynowej w celu odnajdywania nietypowych wzorców w metrykach i nie wymaga konfiguracji. Jednak w przeciwieństwie do inteligentnego wykrywania anomalii związanych z awariami, cel inteligentnego wykrywania anomalii wydajności polega na znalezieniu segmentów przełożenia użycia, które mogą zostać nieprawidłowo obsłużone — na przykład przez określone strony w określonym typie przeglądarki. Analiza jest wykonywana codziennie i w przypadku znalezienia dowolnego wyniku prawdopodobnie będzie znacznie mniej pilna niż alert. Z drugiej strony analiza anomalii związanych z awariami jest wykonywana w sposób ciągły na przychodzącej telemetrii, a użytkownik zostanie powiadomiony w ciągu kilku minut, jeśli stawki za awarie serwera są większe niż oczekiwano.

## <a name="if-you-receive-a-smart-detection-alert"></a>Jeśli otrzymasz alert Smart Detection
*Dlaczego otrzymuję ten alert?*

* Wykryliśmy nietypowy wzrost liczby żądań zakończonych niepowodzeniem w porównaniu z normalną linią bazową poprzedniego okresu. Po analizie błędów i powiązanej telemetrii sądzimy, że występuje problem, którego należy szukać.

*Czy powiadomienie oznacza, że występuje problem?*

* Spróbujemy ostrzec o zakłóceniach aplikacji lub obniżeniu wydajności, ale tylko ty możesz zrozumieć semantykę i wpływ na aplikację lub użytkowników.

*Więc złoczyńców Przyjrzyj się moje dane?*

* Nie. Usługa jest całkowicie automatyczna. Tylko otrzymujesz powiadomienia. Twoje dane są [prywatne](../../azure-monitor/app/data-retention-privacy.md).

*Czy muszę subskrybować ten alert?*

* Nie. Każda aplikacja, która wysyła dane telemetryczne żądania, ma regułę alertu inteligentnego wykrywania.

*Czy mogę anulować subskrypcję lub otrzymywać powiadomienia wysyłane do moich współpracowników?*

* Tak, w regułach alertów kliknij regułę wykrywania inteligentnego, aby ją skonfigurować. Można wyłączyć alert lub zmienić odbiorców alertu.

*Wiadomość e-mail została utracona. Gdzie mogę znaleźć powiadomienia w portalu?*

* W dziennikach aktywności. Na platformie Azure Otwórz zasób Application Insights dla aplikacji, a następnie wybierz pozycję Dzienniki aktywności.

*Niektóre alerty dotyczą znanych problemów i nie chcę ich otrzymywać.*

* Mamy pomijanie alertów w zaległych zaległościach.

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają inspekcję danych telemetrycznych z aplikacji:

* [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Inteligentne wykrywanie są całkowicie automatyczne. Ale być może chcesz skonfigurować więcej alertów?

* [Ręcznie skonfigurowane alerty metryk](../../azure-monitor/app/alerts.md)
* [Testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)
