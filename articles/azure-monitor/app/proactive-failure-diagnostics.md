---
title: Wykrywanie inteligentne — anomalie, w usłudze Application Insights | Dokumentacja firmy Microsoft
description: Ostrzega o nietypowej zmiany kursów żądania zakończone niepowodzeniem w aplikacji sieci web i umożliwia analizowanie diagnostycznych. Konfiguracja nie jest potrzebna.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: cfa00504cd2a05985fde2af3357418eac8baceeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299109"
---
# <a name="smart-detection---failure-anomalies"></a>Wykrywanie inteligentne — anomalie w zakresie błędów
[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie powiadamia, w czasie zbliżonym do rzeczywistego Jeśli nietypowy wzrost liczba nieudanych żądań aplikacji sieci web. Wykrywa nietypowy wzrost żądań HTTP lub wywołania zależności, które ma być zgłaszane nie powiodło się. Dla żądań żądań zakończonych niepowodzeniem są zwykle te kody odpowiedź 400 lub nowszej. Ułatwiające klasyfikowanie i diagnozowanie problemu, analizy właściwości błędów i powiązane dane telemetryczne jest podawany jako powiadomienia. Dostępne są także łącza do portalu usługi Application Insights w celu przeprowadzenia dalszej diagnostyki. Funkcja musi mieć nie konfiguracji ani konfiguracji, ponieważ używa ona algorytmów uczenia maszynowego do przewidywania zwykły współczynnik błędów.

Ta funkcja działa w przypadku platformy ASP.NET oraz Java aplikacji sieci web hostowanych w chmurze lub na własnych serwerach. Działa także dla dowolnej aplikacji, która generuje dane telemetryczne żądania lub zależność — na przykład w przypadku roli procesu roboczego, który wywołuje [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) lub [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Po skonfigurowaniu [usługi Application Insights w projekcie](../../azure-monitor/app/app-insights-overview.md), a podana aplikacja generuje określony minimalny telemetrii, Inteligentne wykrywanie anomalie trwa 24 godziny, aby dowiedzieć się więcej normalnego zachowania aplikacji, zanim zostanie włączone i wysyłanie alertów.

Poniżej przedstawiono przykładowy alert.

![Przykładowy alert wykrywania inteligentnego, przedstawiający klaster analizy dotyczące awarii](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Domyślnie otrzymujesz krótszy poczty format niż w tym przykładzie. Ale możesz [przełączyć się do tego formatu szczegółowego](#configure-alerts).
>
>

Zwróć uwagę, że informuje:

* Współczynnik błędów w porównaniu do zachowania normalna aplikacja.
* Ilu użytkowników dotyczy problem — aby wiedzieć, ile się martwić.
* Wzorzec cech skojarzone z błędami. W tym przykładzie ma kod odpowiedzi określonego, Nazwa żądania (operacji) i wersji aplikacji. Które natychmiast informuje, gdzie rozpocząć wyszukiwanie w kodzie. Inne możliwości może być określonym systemem operacyjnym przeglądarkę lub klienta.
* Wyjątek, danych dziennika śledzenia i błąd zależności (baz danych lub innymi składnikami zewnętrznymi), wydaje się skojarzona z błędami scharakteryzowany.
* Łącza do odpowiednich wyszukiwania na telemetrii w usłudze Application Insights.

## <a name="benefits-of-smart-detection"></a>Zalety wykrywania inteligentnego
Zwykłe [alertów dotyczących metryk](../../azure-monitor/app/alerts.md) informujące o tym, być może wystąpił problem. Jednak wykrywanie inteligentne uruchamia diagnostykę dla Ciebie wykonywanie partii analizy, w przeciwnym razie byłoby trzeba zrobić samodzielnie. Uzyskanie wyników starannego zapakowane, pomaga szybko głównego problemu.

## <a name="how-it-works"></a>Jak to działa
Wykrywanie inteligentne monitoruje telemetrii odebrane z aplikacji, a w szczególności współczynniki błędów. Ta reguła zlicza liczbę żądań, dla którego `Successful request` właściwość ma wartość false, a liczba zależności wywołań, dla którego `Successful call` właściwość ma wartość false. W przypadku żądań domyślnie `Successful request == (resultCode < 400)` (chyba że zostały napisane kod niestandardowy do [filtru](../../azure-monitor/app/api-filtering-sampling.md#filtering) lub wygenerowanie własnego [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) wywołania). 

Wydajność aplikacji jest typowy wzorzec zachowania. Niektóre żądania lub wywołania zależności będą bardziej podatne na niepowodzenia niż inne osoby; i ogólny współczynnik błędów może przejść w wraz ze wzrostem obciążenia. Wykrywanie inteligentne korzysta z uczenia maszynowego w celu znalezienia tych nieprawidłowości.

Jak telemetria pochodzi do usługi Application Insights z aplikacji sieci web, Inteligentne wykrywanie porównuje bieżące zachowanie wzorami zaobserwowane w ciągu ostatnich kilku dni. W przypadku nietypowym wzroście wskaźnika niepowodzeń stwierdzamy w porównaniu z poprzednim wydajności, analiza zostanie wywołany.

Po wyzwoleniu analiza usługi wykonuje analizy klastra na żądanie nie powiodło się, spróbuj zidentyfikować wzorzec wartości, które charakteryzuje się błędy. W powyższym przykładzie analizy wykrył, że większość awarii są o kod wyniku określone, Nazwa żądania, host adresu URL serwera i wystąpienia roli. Z drugiej strony Analiza wykrył, że właściwości systemu operacyjnego klienta jest rozdystrybuowany na wiele wartości, a więc nie ma na liście.

Gdy usługa jest wyposażone w tych wywołań telemetrii, analizator szuka wyjątków i błędów zależności, które są skojarzone z żądaniami w klastrze, który zidentyfikowała, wraz z przykładem żadnych dzienników śledzenia skojarzony z tymi żądaniami.

Wynikowe analizy do Ciebie wysłana jako alert, jeśli nie skonfigurowano jej nie.

Podobnie jak [alerty, możesz ręcznie ustawić](../../azure-monitor/app/alerts.md), można sprawdzić stan alertu i skonfigurowanie go w bloku alertów zasobu usługi Application Insights. Jednak w przeciwieństwie do innych alertów, nie trzeba Konfigurowanie wykrywania inteligentnego. Jeśli chcesz, możesz go wyłączyć, lub zmienić jego docelowych adresów e-mail.

### <a name="alert-logic-details"></a>Szczegóły alertu logiki

Alerty są wyzwalane przez naszą maszynę własnościowego algorytmu uczenia, dzięki czemu firma Microsoft nie może udostępniać szczegóły dokładna implementację. Dzięki temu powiedział rozumiemy, że czasami musisz dowiedzieć się więcej na temat działania podstawowej logiki. Podstawowe czynniki, które są obliczane, aby określić, jeśli wywołany alert są: 

* Analiza niepowodzenia odsetek żądań/zależności w stopniowe przedział czasu 20 minut.
* Porównanie procent niepowodzenia ostatnich 20 minut do szybkości w ostatniej 40 minut i ostatnich siedmiu dni, a wyszukiwanie znaczących odchylenia, przekraczających X razy tego odchylenia standardowego.
* Za pomocą adaptacyjne limit wartości procentowej minimalną niepowodzenie, który waha się oparte na wielkości żądań/zależności aplikacji.

## <a name="configure-alerts"></a>Konfigurowanie alertów
Możesz wyłączyć wykrywanie inteligentne, zmieniać adresatów wiadomości e-mail, utworzyć element webhook lub zgadzaj się na bardziej szczegółowe komunikaty alertów.

Otwórz na stronie alertów. Anomalie w zakresie błędów jest dołączany wraz z żadnych alertów, które możesz ręcznie ustawić i zobaczyć, czy jest ona obecnie w stanie alertu.

![Na stronie Przegląd kliknij Kafelek alerty. Lub na dowolnej stronie metryki, kliknij przycisk alertów.](./media/proactive-failure-diagnostics/021.png)

Kliknij alert, aby go skonfigurować.

![Konfigurowanie](./media/proactive-failure-diagnostics/032.png)

Zwróć uwagę, że można wyłączyć wykrywanie inteligentne, ale nie można go usunąć (lub utwórz inny).

#### <a name="detailed-alerts"></a>Szczegółowe alertów
Jeśli wybierzesz "Pobierz bardziej szczegółowe dane diagnostyczne" wiadomości e-mail będzie zawierać więcej informacji diagnostycznych. Czasami można zdiagnozować problem tylko na podstawie danych w wiadomości e-mail.

Istnieje bardzo małe ryzyko, że alert bardziej szczegółowe mogą zawierać poufne informacje, ponieważ zawiera ona komunikaty wyjątków i śledzenia. Jednak to będzie może mieć miejsce tylko jeśli Twój kod może pozwolić poufnych informacji do wiadomości.

## <a name="triaging-and-diagnosing-an-alert"></a>Sklasyfikowaniu i zdiagnozowaniu alertu
Alert oznacza, że wykryto nietypowy wzrost liczby żądań zakończonych niepowodzeniem. Istnieje prawdopodobieństwo, że występuje problem związany z aplikacji lub jego środowiska.

Z Procent żądań i liczbę użytkowników, których dotyczy problem można zdecydować, jak pilne jest problem. W powyższym przykładzie współczynnik awaryjności 22,5% wypada na tle normalna liczba % 1, wskazuje, czy zły coś. Z drugiej strony liczba objętych użytkowników tylko 11. W przypadku aplikacji, będzie można ocenić, jak poważny to.

W wielu przypadkach można zdiagnozować problem, szybko z wyjątek, Nazwa żądania, zależności awarii i śledzenie danych udostępnionych.

Istnieje kilka innych wskazówek. Na przykład współczynnik błędów zależności, w tym przykładzie jest taka sama jak częstotliwość występowania wyjątków (89.3%). Sugeruje to, że wyjątek pojawia się bezpośrednio z błędów zależności — umożliwiając dalsze gdzie rozpocząć wyszukiwanie informacji zawartych w kodzie.

Aby zbadać dokładnie należy łącza w każdej sekcji spowoduje przejście bezpośrednio do [strony wyszukiwania](../../azure-monitor/app/diagnostic-search.md) filtrowana w celu odpowiednich żądań, wyjątków, zależności lub śledzenia. Alternatywnie możesz otworzyć [witryny Azure portal](https://portal.azure.com), przejdź do zasobu usługi Application Insights dla aplikacji i otwórz blok błędów.

W tym przykładzie kliknięcie linku "Wyświetl szczegóły błędów zależności" spowoduje otwarcie bloku wyszukiwania usługi Application Insights. Pokazuje instrukcję SQL, która zawiera przykład głównej przyczyny: Wartości null zostały podane w obowiązkowe i nie przeszedł pomyślnie weryfikacji podczas zapisywania operacji.

![Wyszukiwanie diagnostyczne](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Najnowsze alerty możesz przejrzeć

Kliknij przycisk **wykrywania inteligentnego** można uzyskać dostęp do najnowszych alertów:

![Podsumowanie alertów](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Jaka jest różnica...
Inteligentne wykrywanie anomalie uzupełniają inne podobne ale różne funkcje usługi Application Insights.

* [Alerty metryki](../../azure-monitor/app/alerts.md) są ustawiane przez Ciebie i monitorować szeroką gamę metryk, takich jak zajętość procesora CPU, żądań, czasy ładowania stron i tak dalej. Można je ostrzega użytkownika, na przykład, jeśli potrzebujesz dodać więcej zasobów. Z drugiej strony inteligentne wykrywanie anomalie obejmuje niewielki zakres krytyczne metryki (obecnie tylko żądań zakończonych), zaprojektowane, aby otrzymywać powiadomienia, możesz w czasie rzeczywistym w sposób po aplikacji sieci web nie powiodło się żądanie kurs zwiększa się znacznie w porównaniu do aplikacji sieci web normalne zachowanie.

    Wykrywanie inteligentne automatycznie dostosowuje przekroczyła wartość progową w odpowiedzi na warunki panujące.

    Wykrywanie inteligentne uruchamia diagnostykę dla Ciebie.
* [Inteligentne wykrywanie anomalii wydajności](proactive-performance-diagnostics.md) również używa usługi machine analizy w celu Znajdź nietypowe wzorce metryk i nie jest wymagana żadna konfiguracja przez użytkownika. Jednak w przeciwieństwie do inteligentnego wykrywania anomalie, inteligentnego wykrywania anomalii wydajności ma na celu znaleźć segmentów usługi kolektora użycia, które może być nieprawidłowo w inny sposób udostępniane — na przykład określonych stron na określony typ przeglądarki. Analiza jest wykonywana raz dziennie, a jeśli dowolny wynik zostanie znaleziony, to może być znacznie mniej pilne niż alertu. Z drugiej strony Analiza anomalie odbywa się stale na przychodzących danych telemetrycznych i Powiadomimy Cię w ciągu kilku minut w przypadku serwera awariami większy, niż oczekiwano.

## <a name="if-you-receive-a-smart-detection-alert"></a>Jeśli zostanie wyświetlony alert wykrywania inteligentnego
*Dlaczego odebrały ten alert?*

* Wykryliśmy nietypowy wzrost współczynnik żądań zakończonych niepowodzeniem w porównaniu do linii bazowej normalne kropki. Po przeprowadzeniu analizy błędów i skojarzone dane telemetryczne uważamy, że występuje problem, który powinien wyglądać w.

*Powiadomienia oznacza, że problem występuje zdecydowanie?*

* Podejmowane są próby alert po wystąpieniu przerwy w działaniu aplikacji lub spadek jakości, ale tylko w pełni wyjaśnić, semantyka i ich wpływie na aplikację lub użytkowników.

*Dlatego guys przyjrzymy się moje dane?*

* Nie. Usługa jest całkowicie automatyczne. Tylko otrzymasz powiadomienia. Twoje dane są [prywatnej](../../azure-monitor/app/data-retention-privacy.md).

*Trzeba subskrybować ten alert?*

* Nie. Każda aplikacja wysyła dane telemetryczne dotyczące żądań ma reguły alertów wykrywania inteligentnego.

*Czy mogę anulować subskrypcję lub Otrzymuj powiadomienia, zamiast tego wysyłane do moich współpracowników?*

* Tak, reguły w alertów kliknij regułę wykrywania inteligentnego ją skonfigurować. Wyłączanie alertu lub zmieniać adresatów alertu.

*Czy mogę utracone wiadomości e-mail. Gdzie znaleźć powiadomienia w portalu*

* W dziennikach aktywności. Na platformie Azure otwórz zasób usługi Application Insights dla aplikacji, a następnie wybierz pozycję Dzienniki aktywności.

*Niektóre alerty są o znanych problemach, a nie chcę je odebrać.*

* Mamy pomijania alertów na naszej liście prac.

## <a name="next-steps"></a>Kolejne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych telemetrycznych z Twojej aplikacji:

* [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Wykrywanie inteligentne są całkowicie automatyczny. A może chcesz skonfigurować niektóre alerty więcej?

* [Ręcznie skonfigurowane alertów dotyczących metryk](../../azure-monitor/app/alerts.md)
* [Testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)
