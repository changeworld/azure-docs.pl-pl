---
title: Alerty dzienników w usłudze Azure Monitor
description: Wyzwalanie wiadomości e-mail, powiadomień, wywołaj adresów URL witryn sieci Web (elementy webhook), oraz automatyzacją po spełnieniu warunki zapytania analityczne, które określisz dla alertów platformy Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427418"
---
# <a name="log-alerts-in-azure-monitor"></a>Alerty dzienników w usłudze Azure Monitor

Ten artykuł zawiera szczegółowe informacje o alertów dzienników są jednym z typów alertów, które obsługują [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) i zezwolić użytkownikom na stosowanie platforma analiz platformy Azure jako podstawa dla alertów.

Alert dziennika składa się z wyszukiwania w dziennikach reguł utworzonych dla [dzienników monitora platformy Azure](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Aby dowiedzieć się więcej na temat jej użycia, zobacz [Tworzenie alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Dane dzienników popularnych z [dzienników monitora platformy Azure](../../azure-monitor/learn/tutorial-viewdata.md) jest obecnie również dostępna na platformie metryki w usłudze Azure Monitor. Dla widoku szczegółów [alertu metryki dla dzienników](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Wyszukiwania reguł alertów dzienników — definicja i typów

Reguły przechowywania dzienników są tworzone przez usługę Azure Alerts w celu automatycznego wykonywania określonych zapytań dotyczących dzienników w regularnych odstępach czasu.  Jeśli wyniki zapytania pasują do określonych kryteriów, jest tworzony rekord alertu. Reguła może wtedy automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md). [Współautor monitorowania platformy Azure](../../azure-monitor/platform/roles-permissions-security.md) roli do tworzenia, modyfikowania i aktualizowania alertów dzienników mogą być wymagane; wraz z dostępem i zapytania praw wykonywania dla cele analytics reguły alertu lub zapytanie alertu. W przypadku, gdy tworzenie użytkownika nie ma dostępu do wszystkich cele analytics reguły alertu lub zapytanie alertu — tworzenie reguł może zakończyć się niepowodzeniem lub reguł alertów dzienników będą wykonywane przy użyciu wyniki częściowe.

Dziennik wyszukiwania reguł są definiowane przez następujące informacje:

- **Zaloguj się zapytania**.  Zapytanie, które jest uruchamiane za każdym razem, gdy reguła alertu jest uruchamiana.  Rekordów zwróconych przez tę kwerendę są używane do określenia, czy alert jest wyzwalany. Zapytania usługi Analytics może być określony obszar roboczy usługi Log Analytics lub aplikacji usługa Application Insights i nawet rozciągać się na [wiele zasobów usługi Log Analytics i usługi Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) podany użytkownik ma dostęp, a także prawa do wszystkich zapytań zasoby. 
    > [!IMPORTANT]
    > [zapytania obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) pomocy technicznej w alertów dzienników dla usługi Application Insights i log alertów dotyczących [usługi Log Analytics skonfigurowane za pomocą interfejsu API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) tylko.

    Niektóre polecenia analitycznych i kombinacji są niezgodne z użycia w alertów dzienników; Aby wyświetlić więcej szczegółów, [rejestrowania alertów zapytań w usłudze Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Przedział czasu**.  Określa zakres czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (np. temu) używanych w zapytaniu dziennika. <br>*Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu jest zwracana do wykonania zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia, takie jak temu teraz (7 dni), zapytania dotyczącego dziennika zostanie uruchomione tylko dla danych zakresu od 12:15:00 do 13:15:00 - tak, jakby istnieją dane dla ostatnich 60 minut. A nie przez siedem dni, danych, jak to określono w dzienniku zapytań.*

- **Częstotliwość**.  Określa, jak często należy uruchamiać zapytanie. Może to być wartość z zakresu od 5 minut do 24 godzin. Powinna być równa lub mniejsza niż okres czasu.  Jeśli wartość jest większa niż okres czasu, istnieje ryzyko rekordów jest pominięte.<br>*Na przykład należy wziąć pod uwagę okres 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchomione 1:00, zwraca rekordy od 12:30 do 1:00 PM.  Przy następnym uruchomić zapytanie jest 2:00, jeśli zwróci rekordów między 1:30 – 2:00.  Nigdy nie oceniono rekordy między 1:00 i 1:30.*

- **Próg**.  Wyniki przeszukiwania dzienników są oceniane w celu ustalenia, czy należy utworzyć alert.  Próg różni się dla różnych typów reguł alertów wyszukiwania dziennika.

Reguły wyszukiwania dziennika można je [dzienników monitora platformy Azure](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), mogą być dwojakiego rodzaju. Każdy z tych typów opisano szczegółowo w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)** . Pojedynczy alert utworzony, jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku przekracza określoną liczbę.
- **[Pomiar metryki](#metric-measurement-alert-rules)** .  Alert został utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami, które przekracza określoną wartość progową.

Dostępne są następujące różnice między typami reguły alertu.

- *Liczba wyników* reguł alertów zawsze tworzy chwilę alertów, pojedynczy *pomiar metryki* alertu zasada tworzy alert dla każdego obiektu, który przekracza wartość progową.
- *Liczba wyników* reguł alertów Tworzenie alertu, po przekroczeniu progu jeden raz. *Pomiar metryki* reguł alertów można utworzyć alertu, po przekroczeniu progu pewną liczbę uruchomień w określonym interwale.

### <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów

**Liczba wyników** reguł alertów Utwórz pojedynczy alert, gdy liczba rekordów zwróconych przez zapytanie wyszukiwania przekracza określoną wartość progową. Ten typ alertu jest idealny dla pracy z wydarzeniami takimi jak Windows, dzienniki zdarzeń, usługi Syslog, odpowiedzi aplikacji sieci Web i dzienników niestandardowych.  Można utworzyć alert, gdy tworzony zdarzeń określony błąd lub wielu zdarzeń błędu są tworzone w określonym przedziale czasu.

**Próg**: Próg liczby reguł alertów wyników jest większa lub mniejsza od określonej wartości.  Jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku odpowiadają te kryteria, jest tworzony alert.

Aby otrzymywać alerty na pojedyncze zdarzenie, ustaw liczbę wyników na wartość większą niż 0, a następnie wyszukaj wystąpienie pojedyncze zdarzenie, który został utworzony od czasu ostatniego uruchomienia zapytania. Niektóre aplikacje mogą rejestrować okazjonalne błąd, który nie należy koniecznie zgłoszenie alertu.  Na przykład aplikacja może ponów próbę wykonania procesu, który utworzył zdarzenie błędu i kończyło się powodzeniem następnym razem.  W tym przypadku nie można utworzyć alert, chyba że wiele zdarzeń zostaje utworzonych w określonym przedziale czasu.  

W niektórych przypadkach warto utworzyć alert w przypadku braku zdarzenia.  Na przykład procesu mogą rejestrować regularne zdarzenia w celu wskazania, że działa prawidłowo.  Jeżeli nie jego wylogować jedno z tych zdarzeń w danym okresie, powinien zostać utworzony alert.  W takim przypadku należy ustawić wartość progową **mniej niż 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Przykład alertu dziennika typu Liczba rekordów

Rozważmy scenariusz, w którym chcesz wiedzieć, kiedy aplikacji opartych na sieci web zapewnia odpowiedzi dla użytkowników z kodem 500 (to znaczy) wewnętrzny błąd serwera. Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** żądań | gdzie resultCode == "500"<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość progowa:** Większa niż 0<br>

Alert będzie uruchomi zapytanie co 5 minut, 30 minut danych — do wyszukiwania dowolnego rekordu, gdzie kod wyniku: 500. Jeśli jeszcze jeden taki rekord zostanie znaleziony, wyzwala alert i wyzwala akcję skonfigurowane.

### <a name="metric-measurement-alert-rules"></a>Reguły alertu pomiaru metryki

**Pomiar metryki** reguł alertów Utwórz alert dla każdego obiektu w zapytaniu o wartości, które przekracza określoną wartość progową, a określony warunek wyzwalacza. W odróżnieniu od **liczba wyników** reguły, alertów **pomiar metryki** reguł alertów działają, gdy wynik analizy udostępnia szeregów czasowych. Mają one różne następujące różnice względem **liczba wyników** reguły alertów.

- **Funkcję agregacji**: Określa, obliczenia, które jest wykonywane i potencjalnie liczbową pola do zagregowania.  Na przykład **count()** zwraca liczbę rekordów w zapytaniu **avg(CounterValue)** zwraca średnią pola CounterValue dla interwału. Funkcja agregująca w zapytaniu muszą być o nazwie wywoływana: Elementy AggregatedValue i podaj wartość liczbową. 

- **Grupy pól**: Rekord z wartością zagregowaną jest tworzony dla każdego wystąpienia to pole, a alert jest generowany dla każdego.  Na przykład, jeśli chcesz generować alert dla każdego komputera, można użyć **przez komputer**. W przypadku, gdy, istnieje wiele grupy pól określonych w kwerendzie alertu, użytkownik może określić pole, które ma być używany, aby posortować wyniki za pomocą **agregacji na** parametru (metricColumn)

    > [!NOTE]
    > *Agregacji na* (metricColumn) opcja jest dostępna dla alertów dzienników typu pomiar metryki dla usługi Application Insights i log alertów dotyczących [usługi Log Analytics skonfigurowane za pomocą interfejsu API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) tylko.

- **Interwał**:  Definiuje przedział czasu, przez który dane są agregowane.  Na przykład, jeśli określono **pięć minut**, może zostać utworzony rekord dla każdego wystąpienia pola grupy agregowane w 5-minutowych interwałach w przedziale czasu określony dla alertu.

    > [!NOTE]
    > Funkcja bin musi używane w zapytaniu, aby określić interwał. Jako bin() może spowodować nierówne interwałami — Alert zostaną automatycznie przekonwertowane bin polecenia polecenie bin_at z odpowiednim czasie w czasie wykonywania, aby upewnić się, wyniki ze stałym punktem. Typ metryki pomiaru alertu dziennika jest przeznaczona do pracy z zapytaniami o maksymalnie trzech wystąpień bin() polecenia
    
- **Próg**: Próg reguły alertu pomiaru metryki jest definiowany przez wartości zagregowanej i liczbę naruszeń.  Dowolny punkt danych podczas wyszukiwania dziennika przekracza tę wartość, jest uznawane za naruszenia.  Jeśli liczba naruszeń w dla dowolnego obiektu w wynikach przekracza określoną wartość, alert zostanie utworzony dla tego obiektu.

Błędną konfiguracją *agregacji na* lub *metricColumn* opcji może spowodować, że przerw w zapłonie reguły alertów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z podczas regułę alertu pomiaru metryki jest niepoprawna](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Przykład alertu dziennika typu pomiar metryki

Rozważmy scenariusz, w którym alert potrzebowała przekroczeniu użycie procesora przez 90% trzykrotnie ponad 30 minut, przez dowolny komputer.  Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** Wydajności | Gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" | summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 5 min), komputer<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Alert Logic - próg & warunku:** Większa niż 90<br>
- **Pole grupy (agregacji on):** Computer (Komputer)
- **Wyzwalaj alert na podstawie:** Łączna liczba naruszeń większej niż 2<br>

Zapytanie spowodowałoby średnią wartość dla każdego komputera w 5-minutowych odstępach czasu.  To zapytanie zostałoby uruchomione co 5 minut przez dane zebrane przez poprzednie 30 minut. Ponieważ pola grupy (agregacji jednokrotnego) wybrane jest kolumnowych "Computer" — elementy AggregatedValue jest dzielony na różne wartości "Computer", a średnie użycie procesorów dla każdego komputera jest określana dla pojemnika czasu wynoszącą 5 minut.  Przykładowe wyniki zapytania (powiedzieć) trzy komputery będą poniżej.


|TimeGenerated [UTC] |Computer (Komputer)  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|Przyciski ...     |   Przyciski ...      |    Przyciski ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Jeśli wynik kwerendy do rysowania, pojawią się jako.

![Przykładowe wyniki zapytania](media/alerts-unified-log/metrics-measurement-sample-graph.png)

W tym przykładzie widzimy w pojemnikach 5 minut dla każdej z trzech komputerów — średnie użycie procesorów jako obliczane 5 minut. Próg 90 naruszane przez tylko jeden raz srv01 godzinie 1:25 bin. W odróżnieniu od srv02 przekracza próg 90 na 1:10, 1:15 i 1:25 pojemniki; gdy srv03 przekracza próg 90 na 1:10, 1:15, 1:20 i 1:30.
Ponieważ alert jest skonfigurowany do wyzwalacza opartego na łączna liczba naruszeń są więcej niż dwa, widzimy, że srv02 i srv03 tylko spełniają kryteria. Dlatego oddzielne alerty zostałyby utworzone srv02 i srv03, ponieważ ich naruszenia 90% progu dwa razy między wieloma pojemniki czasu.  Jeśli *Wyzwalaj alert na podstawie:* parametru zamiast tego została skonfigurowana dla *ciągłe naruszeń* opcji, a następnie alert będzie uruchamiany **tylko** dla srv03 od momentu jego naruszone próg dla trzech pojemników kolejnych czasu z 1:10 do 1:20. I **nie** dla srv02, ponieważ naruszeniem próg dla dwóch pojemniki kolejnych czasu z 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Wyszukiwania reguł alertów dzienników — uruchomieniu którego i stanu

Reguły alertów wyszukiwania dziennika działa na logice uzależniona przez użytkownika, zgodnie z konfiguracji i zapytania analizy niestandardowych używane. Ponieważ logikę monitorowania tym dokładny stan lub powód, dlaczego reguły alertów wyzwalanych jest hermetyzowany zapytania usługi analytics — które mogą się różnić w poszczególnych reguł alertów dzienników. Alerty platformy Azure ma deficytowe informacje o określonej podstawowej przyczyny (lub) scenariusza podczas warunkiem progu reguły alertów wyszukiwania dziennika jest osiągnięciu lub przekroczeniu obliczania. Ten sposób alertów dzienników są określane jako bez stanu. I reguł alertów dzienników będą nadal wyzwalania, tak długo, jak warunek alertu jest spełnione dzięki wynik zapytania niestandardowe analizy. Bez alert co wprowadzenie uda się rozwiązać, ponieważ logika dokładne przyczyny niepowodzenia monitorowania są wyświetlane jako znaki wewnątrz zapytania usługi analytics, dostarczone przez użytkownika. Nie, obecnie jest żaden mechanizm służący do usługi Azure Monitor Alerts ostatecznie ustalenie przyczyny są rozwiązane.

Pozwala nam Zobacz taki sam jak praktyczny przykład. Przyjęto założenie, mamy reguł alertów dzienników o nazwie *Alert w przypadku dziennika Contoso*, ponieważ na konfiguracji w [przykładu przewidzianego dla alertu dziennika typu Liczba wyników](#example-of-number-of-records-type-log-alert) — gdy niestandardowe zapytanie alertu jest przeznaczona do wyszukania 500 Kod wyniku w dziennikach.

- O 13:05 po Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika zwróciło zero rekordów z kodem wynik o 500. Ponieważ zero jest poniżej progu, a alert nie jest uruchamiany.
- W następnej iteracji o 13:10 podczas Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika, należy podać pięć rekordów z kodem wynik jako 500. Ponieważ pięciu przekracza wartość progową, a alert jest wyzwalany przy użyciu skojarzonych z nimi działań wyzwalane.
- O 13:15 po Contoso dziennika alertów została wykonana przez alertów platformy Azure wyniki wyszukiwania dziennika podać dwa rekordy z kodem 500 wynik. Ponieważ dwie przekracza wartość progową, a alert jest wyzwalany przy użyciu skojarzonych z nimi działań wyzwalane.
- Teraz w następnej iteracji o 13:20 po Contoso-dziennika — Alert został wykonany przez alert dotyczący platformy Azure, wyniki wyszukiwania dziennika udostępniany ponownie zero rekordów z kodem 500 wynik. Ponieważ zero jest poniżej progu, a alert nie jest uruchamiany.

Jednak w przypadku wymienionych powyżej o 13:15 - alertów platformy Azure nie można określić czy podstawowych problemów, które wystąpienie 1:10 zostaną zachowane, a jeśli jest netto nowe błędy. Kwerendy dostarczone przez użytkownika może biorąc pod uwagę wcześniej rekordów - alertów platformy Azure mogą być się. Od logiki alertu jest hermetyzowany w zapytanie alertu — więc dwa rekordy z kodem 500 wynik widoczne o 13:15 może lub nie może być już widoczne o 13:10. Dlatego aby err boku ostrzeżenie, gdy Contoso-dziennika — Alert jest wykonywana o 13:15, skonfigurowanych akcji jest wyzwalane ponownie. Teraz o 13:20 po zero rekordy są widoczne kod wyniku 500 - alertów platformy Azure nie może mieć pewności, że przyczyną kod wyniku 500, które wystąpienie 1:10 PM i 13:15 czasu teraz został rozwiązany i alertów monitora platformy Azure bez obaw można wywnioskować problemów 500 Błąd nie występuje z tego samego powodu ponownie s. Dlatego Contoso-dziennika — Alert będzie nie zmieni się na rozwiązany w pulpit nawigacyjny alertów platformy Azure i/lub powiadomienia wysłane z informacją, rozpoznawanie alertu. Zamiast tego użytkownika, który rozumie dokładnie warunku lub Przyczyna logiki osadzone w zapytania usługi analytics można [Oznacz alert jako zamknięty](alerts-managing-alert-states.md) zgodnie z potrzebami.

## <a name="pricing-and-billing-of-log-alerts"></a>Cennik i rozliczenia alertów dziennika

Ceny dla dziennika alertów jest podany w [cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) strony. W platformie Azure są naliczane, alertów dzienników są reprezentowane jako typ `microsoft.insights/scheduledqueryrules` za pomocą:

- Alerty dzienników w usłudze Application Insights wyświetlane z dokładną nazwę alertu wraz z grupy zasobów i właściwości alertu
- Alerty dzienników w wyświetlane z dokładną nazwę alertu wraz z grupy zasobów i właściwości alertu; w usłudze Log Analytics Podczas tworzenia przy użyciu [scheduledQueryRules interfejsu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

[Starszej wersji interfejsu API programu Log Analytics](../../azure-monitor/platform/api-alerts.md) ma akcje alertów i harmonogramów w ramach zapisane wyszukiwania usługi Log Analytics i nie odpowiednie [zasobów platformy Azure](../../azure-resource-manager/resource-group-overview.md). Dlatego włączenia opcji naliczania opłat dla tych alertów dzienników starszych utworzonego dla usługi Log Analytics przy użyciu witryny Azure Portal **bez** [przełączenie do nowego interfejsu API](../../azure-monitor/platform/alerts-log-api-switch.md) lub za pośrednictwem [starszej wersji interfejsu API programu Log Analytics](../../azure-monitor/platform/api-alerts.md) - reguły alertów pseudo ukryte są tworzone na `microsoft.insights/scheduledqueryrules` rozliczeń na platformie Azure. Ukryte pseudo reguły alertów utworzone dla rozliczeń na `microsoft.insights/scheduledqueryrules` przedstawiony jako `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` wraz z grupy zasobów i właściwości alertu.

> [!NOTE]
> Jeśli występują nieprawidłowe znaki takie jak `<, >, %, &, \, ?, /` są obecne, zostaną one zastąpione przy użyciu `_` w regule alertu ukryte pseudo nazwę, a więc również na platformie Azure są naliczane.

Aby usunąć zasoby ukryte scheduleQueryRules utworzone dla Naliczanie opłat za reguły alertów za pomocą [starszej wersji interfejsu API programu Log Analytics](api-alerts.md), użytkownik może zrobić w dowolnej z następujących czynności:

- Albo użytkownik może [Przełącz preferencji interfejsu API za reguły alertów w obszarze roboczym usługi Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) i bez utraty ich reguł alertów i monitorowania przeniesienia do usługi Azure Resource Manager zgodne [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Dzięki temu wyeliminować pamięciach pseudo ukryte reguł alertów do rozliczeń.
- Lub jeśli użytkownik nie chce przełączyć preferencji interfejsu API, użytkownik będzie musiał **Usuń** oryginalnego harmonogramu i przy użyciu akcji alertu [starszej wersji interfejsu API programu Log Analytics](api-alerts.md) lub usunąć w [witryny Azure portal oryginalny reguł alertów dzienników](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Dodatkowo dla zasobów ukryte scheduleQueryRules utworzonych dla Naliczanie opłat za reguły alertów za pomocą [starszej wersji interfejsu API programu Log Analytics](api-alerts.md), wszelkie operacja modyfikacji, takie jak PUT zakończy się niepowodzeniem. Jako `microsoft.insights/scheduledqueryrules` typu pseudo reguły mają zastosowanie do celu rozliczeń reguły alertów utworzone za pomocą [starszej wersji interfejsu API programu Log Analytics](api-alerts.md). Powinna być podejmowana z każdej modyfikacji reguły alertu przy użyciu [starszej wersji interfejsu API programu Log Analytics](api-alerts.md) (lub) użytkownik może [Przełącz preferencji interfejsu API dla reguł alertów](../../azure-monitor/platform/alerts-log-api-switch.md) używać [scheduledQueryRules interfejsu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Zamiast tego.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Tworzenie alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md).
* Zrozumienie [elementami webhook w dzienniku alertów na platformie Azure](alerts-log-webhook.md).
* Dowiedz się więcej o [alertów platformy Azure](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md).
* Dowiedz się więcej o [usługi Log Analytics](../../azure-monitor/log-query/log-query-overview.md).