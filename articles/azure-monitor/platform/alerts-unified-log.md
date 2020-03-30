---
title: Rejestrowanie alertów w usłudze Azure Monitor
description: Wyzwalanie wiadomości e-mail, powiadomień, wywoływania adresów URL witryn sieci Web (zestawów webhook) lub automatyzacji, gdy określone warunki zapytania analitycznego są spełnione dla alertów platformy Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665225"
---
# <a name="log-alerts-in-azure-monitor"></a>Rejestrowanie alertów w usłudze Azure Monitor

Ten artykuł zawiera szczegółowe informacje alerty dziennika są jednym z typów alertów obsługiwanych w [ramach alertów platformy Azure](../../azure-monitor/platform/alerts-overview.md) i umożliwiają użytkownikom korzystanie z platformy analitycznej platformy Azure jako podstawy do alertów.

Alert dziennika składa się z reguł wyszukiwania dziennika utworzonych dla [dzienników monitora Azure](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Aby dowiedzieć się więcej o jego użyciu, zobacz [tworzenie alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Popularne dane dziennika z [dzienników usługi Azure Monitor są](../../azure-monitor/learn/tutorial-viewdata.md) teraz również dostępne na platformie metrycznej w usłudze Azure Monitor. Aby uzyskać widok szczegółów, [alert metryki dla dzienników](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Reguła alertu wyszukiwania dziennika — definicja i typy

Reguły przechowywania dzienników są tworzone przez usługę Azure Alerts w celu automatycznego wykonywania określonych zapytań dotyczących dzienników w regularnych odstępach czasu.  Jeśli wyniki zapytania pasują do określonych kryteriów, jest tworzony rekord alertu. Reguła może wtedy automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md). Może być wymagana rola [współautora monitorowania platformy Azure](../../azure-monitor/platform/roles-permissions-security.md) do tworzenia, modyfikowania i aktualizowania alertów dziennika; wraz z prawami do wykonywania & zapytania dostępu dla docelowych(ów) analizy w regułie alertów lub kwerendzie alertów. W przypadku, gdy użytkownik tworzący nie ma dostępu do wszystkich docelowych analytics w reguły alertu lub kwerendy alert - tworzenie reguły może zakończyć się niepowodzeniem lub reguła alertu dziennika zostanie wykonana z częściowymi wynikami.

Reguły wyszukiwania dzienników są definiowane przez następujące szczegóły:

- **Zapytanie dziennika**.  zapytanie uruchamiane po każdym wyzwoleniu reguły alertu.  Rekordy zwracane przez tę kwerendę są używane do określenia, czy alert ma być wyzwalany. Kwerenda analizy może być dla określonego obszaru roboczego usługi Log Analytics lub aplikacji usługi Application Insights, a nawet obejmować [wiele zasobów usługi Log Analytics i usługi Application Insights,](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) pod warunkiem, że użytkownik ma dostęp, a także prawa do zapytań do wszystkich zasobów. 
    > [!IMPORTANT]
    > obsługa [zapytań między zasobami](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) w alertach dziennika dla usługi Application Insights i alertów dziennika dla usługi Log Analytics skonfigurowanych przy użyciu tylko [interfejsu API scheduledQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Niektóre polecenia analityczne i kombinacje są niezgodne z użyciem w alertach dziennika; aby uzyskać więcej informacji, [Zaloguj kwerendy alertów w usłudze Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Okres czasu**.  Określa zakres czasu kwerendy. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Okres czasu ogranicza dane pobierane dla kwerendy dziennika, aby zapobiec nadużyciom i obchodzi polecenia czasu (jak temu) używane w kwerendzie dziennika. <br>*Na przykład Jeśli okres jest ustawiony na 60 minut, a kwerenda jest uruchamiana o godzinie 13:15, tylko rekordy utworzone między 12:15 PM i 13:15 PM jest zwracany do wykonywania kwerendy dziennika. Teraz, jeśli kwerenda dziennika używa polecenia czasu, jak temu (7d), kwerenda dziennika będzie uruchamiana tylko dla danych między 12:15 PM i 13:15 PM - tak, jakby dane istnieją tylko przez ostatnie 60 minut. I nie dla siedmiu dni danych, jak określono w zapytaniu dziennika.*

- **Częstotliwość**.  Określa, jak często kwerenda powinna być uruchamiana. Może mieć dowolną wartość od 5 minut do 24 godzin. Powinien być równy lub krótszy niż okres.  Jeśli wartość jest większa niż okres czasu, istnieje ryzyko, że rekordy zostaną pominięte.<br>*Rozważmy na przykład okres 30 minut i częstotliwość 60 minut.  Jeśli kwerenda jest uruchamiana o godzinie 1:00, zwraca rekordy między 12:30 a 13:00.  Następnym razem, gdy kwerenda zostanie uruchomiony jest 2:00, kiedy zwróci rekordy między 1:30 i 2:00.  Wszystkie rekordy utworzone między 1:00 a 1:30 nigdy nie będą oceniane.*

- **Próg**.  Wyniki wyszukiwania dziennika są oceniane w celu ustalenia, czy należy utworzyć alert.  Próg jest różny dla różnych typów reguł alertów wyszukiwania dziennika.

Reguły wyszukiwania dziennika, czy to dla [dzienników usługi Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights,](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)mogą być dwa typy. Każdy z tych typów jest szczegółowo opisany w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, gdy rekordy liczb zwracane przez wyszukiwanie dziennika przekraczają określoną liczbę.
- **[Pomiar metryczny](#metric-measurement-alert-rules)**.  Alert utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami przekraczającymi określony próg.

Różnice między typami reguł alertów są następujące.

- *Liczba reguł alertów wyników* zawsze tworzy pojedynczy alert, podczas gdy reguła alertu *pomiaru metryki* tworzy alert dla każdego obiektu, który przekracza próg.
- Liczba reguł *alertów wyników* tworzy alert, gdy próg zostanie przekroczony w jednym czasie. Reguły alertów *pomiaru metryki* można utworzyć alert, gdy próg zostanie przekroczony określoną liczbę razy w określonym przedziale czasu.

### <a name="number-of-results-alert-rules"></a>Liczba reguł alertów o wynikach

Liczba reguł **alertów wyników** tworzy pojedynczy alert, gdy liczba rekordów zwracanych przez kwerendę wyszukiwania przekracza określony próg. Ten typ reguły alertów jest idealny do pracy ze zdarzeniami, takimi jak dzienniki zdarzeń systemu Windows, Syslog, Odpowiedź webapp i dzienniki niestandardowe.  Można utworzyć alert, gdy zostanie utworzone zdarzenie określonego błędu lub gdy wiele zdarzeń błędu są tworzone w określonym okresie czasu.

**Próg**: Próg dla reguł alertu o wielu wynikach jest większy lub mniejszy niż określona wartość.  Jeśli liczba rekordów zwróconych przez wyszukiwanie dziennika spełnia te kryteria, tworzony jest alert.

Aby alerty na pojedyncze zdarzenie, należy ustawić liczbę wyników na większą niż 0 i sprawdzić wystąpienie pojedynczego zdarzenia, który został utworzony od czasu ostatniego uruchomienia kwerendy. Niektóre aplikacje mogą rejestrować sporadyczny błąd, który nie powinien koniecznie podnieść alertu.  Na przykład aplikacja może ponowić próbę procesu, który utworzył zdarzenie błędu, a następnie zakończyć się pomyślnie następnym razem.  W takim przypadku nie można utworzyć alertu, chyba że wiele zdarzeń są tworzone w określonym okresie czasu.  

W niektórych przypadkach można utworzyć alert w przypadku braku zdarzenia.  Na przykład proces może rejestrować regularne zdarzenia, aby wskazać, że działa poprawnie.  Jeśli nie rejestruje jednego z tych zdarzeń w określonym przedziale czasu, należy utworzyć alert.  W takim przypadku próg należy ustawić na **mniej niż 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Przykład alertu dziennika typu Liczba rekordów

Rozważ scenariusz, w którym chcesz wiedzieć, kiedy aplikacja oparta na sieci Web daje odpowiedź do użytkowników z kodem 500 (czyli) Błąd serwera wewnętrznego. Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** żądania | gdzie wynikKod == "500"<br>
- **Okres czasu:** 30 minut<br>
- **Częstotliwość alarmowa:** pięć minut<br>
- **Wartość progowa:** Większa niż 0<br>

Następnie alert będzie uruchamiać kwerendę co 5 minut, z 30 minut danych - szukać dowolnego rekordu, gdzie kod wyniku był 500. Jeśli zostanie znaleziony nawet jeden taki rekord, uruchamia alert i wyzwala skonfigurowane działanie.

### <a name="metric-measurement-alert-rules"></a>Reguły alertów pomiaru metryki

Reguły alertów **pomiaru metryki** tworzą alert dla każdego obiektu w kwerendzie o wartości przekraczającej określony próg i określony warunek wyzwalacza. W przeciwieństwie do liczby reguł **alertów wyników** reguły alertów **pomiaru metryki** działają, gdy wynik analizy zapewnia szeregi czasowe. Mają one następujące różnice od liczby reguł **alertów wyników.**

- **Funkcja agregująca**: Określa wykonywane obliczenia i potencjalnie pole liczbowe do zagregowania.  Na przykład **count()** zwraca liczbę rekordów w kwerendzie, **avg(CounterValue)** zwraca średnią pola CounterValue w interwale. Funkcja agregująca w kwerendzie musi mieć nazwę/wywoływana: AggregatedValue i podać wartość liczbową. 

- **Pole grupy:** Dla każdego wystąpienia tego pola tworzony jest rekord o wartości zagregowanego i dla każdego z nich może zostać wygenerowany alert.  Na przykład, jeśli chcesz wygenerować alert dla każdego komputera, należy użyć **przez komputer**. W przypadku, gdy istnieje wiele pól grupy określonych w kwerendzie alertów, użytkownik może określić, które pole ma być używane do sortowania wyników przy użyciu parametru **Agreguj na** (metricColumn)

    > [!NOTE]
    > *Opcja Agreguj włączone* (metricColumn) jest dostępna dla alertów dziennika metryki dla aplikacji i alertów dziennika dla usługi Log Analytics skonfigurowanych tylko [przy użyciu interfejsu API scheduledQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Interwał**: Definiuje przedział czasu, w którym dane są agregowane.  Na przykład, jeśli określono **pięć minut,** rekord zostanie utworzony dla każdego wystąpienia pola grupy zagregowanego w odstępach 5-minutowych w okresie określonym dla alertu.

    > [!NOTE]
    > Funkcja pojemnika musi być używana w kwerendzie w celu określenia interwału. Ponieważ bin() może spowodować nierówne odstępy czasu — alert automatycznie konwertuje polecenie bin na polecenie bin_at z odpowiednim czasem wykonywania, aby zapewnić wyniki ze stałym punktem. Metryczny typ pomiaru alertu dziennika jest przeznaczony do pracy z zapytaniami o maksymalnie trzech wystąpieniach polecenia bin()
    
- **Próg**: Próg dla reguł alertu pomiaru metryki jest definiowany przez wartość zagregowaną i liczbę naruszeń.  Jeśli dowolny punkt danych w wyszukiwaniu dziennika przekracza tę wartość, jest uważany za naruszenie.  Jeśli liczba naruszeń dla dowolnego obiektu w wynikach przekracza określoną wartość, zostanie utworzony alert dla tego obiektu.

Błędna konfiguracja opcji *Agreguj w* lub *metrykiKolumna* może spowodować niewypał reguł alertów. Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów, gdy reguła alertu pomiaru metryki jest nieprawidłowa](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Przykład alertu dziennika typu pomiaru metryki

Należy wziąć pod uwagę scenariusz, w którym chcesz alert, jeśli dowolny komputer przekroczył wykorzystanie procesora 90% trzy razy w ciągu 30 minut.  Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** Perf | gdzie ObjectName == "Procesor" i Nazwa licznik == "% czasu procesora" | podsumuj AggregatedValue = avg(CounterValue) przez bin(TimeGenerated, 5m), Komputer<br>
- **Okres czasu:** 30 minut<br>
- **Częstotliwość alarmowa:** pięć minut<br>
- **Logika alertu — próg & warunek:** Większa niż 90<br>
- **Pole grupy (agregacja):** Komputerze
- **Alert wyzwalacza oparty na:** Łączna liczba naruszeń większa niż 2<br>

Kwerenda utworzy średnią wartość dla każdego komputera w odstępach 5-minutowych.  Ta kwerenda będzie uruchamiana co 5 minut dla danych zebranych w ciągu poprzednich 30 minut. Ponieważ pole grupy (agregacja) wybrany jest kolumnowy "Komputer" - AggregatedValue jest dzielona dla różnych wartości "Komputer" i średnie wykorzystanie procesora dla każdego komputera jest określana dla pojemnika czasu 5 minut.  Przykładowy wynik kwerendy dla (powiedzmy) trzech komputerów, będzie jak poniżej.


|Czasgenerowany [UTC] |Computer (Komputer)  |Wartość zagregowana  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Jeśli wynik kwerendy miał być wykreślony, będzie wyświetlany jako.

![Przykładowe wyniki kwerendy](media/alerts-unified-log/metrics-measurement-sample-graph.png)

W tym przykładzie widzimy w pojemnikach po 5 minut dla każdego z trzech komputerów - średnie wykorzystanie procesora obliczone przez 5 minut. Próg 90 jest przekroczony przez srv01 tylko raz na 1:25 bin. Dla porównania, srv02 przekracza próg 90 przy koszach 1:10, 1:15 i 1:25; podczas gdy srv03 przekracza próg 90 o 1:10, 1:15, 1:20 i 1:30.
Ponieważ alert jest skonfigurowany do wyzwalania na podstawie wszystkich naruszeń są więcej niż dwa, widzimy, że srv02 i srv03 tylko spełniają kryteria. W związku z tym oddzielne alerty zostaną utworzone dla srv02 i srv03, ponieważ przekroczyły próg 90% dwa razy w wielu pojemnikach czasowych.  Jeśli *alert wyzwalacza na podstawie:* parametr został skonfigurowany dla *ciągłych naruszeń* opcji, a następnie alert zostanie uruchomiony **tylko** dla srv03, ponieważ przekroczył próg dla trzech kolejnych pojemników czasu od 1:10 do 1:20. I **nie** dla srv02, ponieważ przekroczył próg przez dwa kolejne kosze czasowe od 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Reguła alertu wyszukiwania dziennika — wypalanie i stan

Reguły alertów wyszukiwania dziennika działają tylko na logikę, którą tworzysz w kwerendzie. System alertów nie ma żadnego innego kontekstu stanu systemu, intencji lub głównej przyczyny implikowanej przez kwerendę. W związku z tym alerty dziennika są określane jako bez stanu. Warunki są oceniane jako "PRAWDA" lub "FAŁSZ" za każdym razem, gdy są uruchamiane.  Alert zostanie uruchamiany za każdym razem, gdy ocena stanu alertu jest "TRUE", niezależnie od tego, czy jest uruchamiany wcześniej.    

Zobaczmy to zachowanie w akcji z praktycznym przykładem. Załóżmy, że mamy regułę alertu dziennika o nazwie *Contoso-Log-Alert*, która jest skonfigurowana w sposób pokazany w [przykładzie podanym dla alertu dziennika dziennika typu Liczba wyników.](#example-of-number-of-records-type-log-alert) Warunek jest niestandardowe zapytanie alertu przeznaczone do wyszukiwania 500 kod wynik w dziennikach. Jeśli w dziennikach zostanie znaleziony kolejny 500 kodów wyników, stan alertu jest spełniony. 

W każdym interwale poniżej system alertów platformy Azure ocenia warunek *contoso-log-alert*.


| Time    | Liczba rekordów zwracanych przez zapytanie wyszukiwania dziennika | Unikuwarunki dziennika | Wynik 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 rekordów | 0 nie jest > 0 tak FALSE |  Alert nie uruchamia się. Nie wywoływano żadnych akcji.
| 13:10 | 2 rekordy | 2 > 0 tak PRAWDA  | Alarmy pożarów i grup akcji o nazwie. Stan alertu ACTIVE.
| 13:15 | 5 rekordów | 5 > 0 tak PRAWDA  | Alarmy pożarów i grup akcji o nazwie. Stan alertu ACTIVE.
| 13:20 | 0 rekordów | 0 nie jest > 0 tak FALSE |  Alert nie uruchamia się. Nie wywoływano żadnych akcji. Stan alertu w lewo ACTIVE.

Jako przykład użycia poprzedniego przypadku:

O godzinie 13:15 alerty platformy Azure nie mogą określić, czy podstawowe problemy widoczne w 1:10 utrzymują się i jeśli rekordy są nowymi awariami netto lub powtórzeniami starszych błędów o godzinie 13:10. Kwerenda dostarczona przez użytkownika może, ale nie musi uwzględniać wcześniejszych rekordów, a system nie wie. System alertów platformy Azure jest zbudowany, aby błądzić po stronie ostrożności i uruchamia alert i skojarzone akcje ponownie o godzinie 13:15. 

O godzinie 13:20, gdy rekordy zerowe są widoczne z kodem wyników 500, alerty platformy Azure nie mogą być pewni, że przyczyna kodu wyniku 500 widoczna o 13:10 i 13:15 jest teraz rozwiązana. Nie wie, czy 500 problemy z błędami zdarzają się z tych samych powodów ponownie. W związku z *tym Contoso-Log-Alert* nie zmienia się na **Rozwiązany** w panelu usługi Azure Alert i/lub powiadomienia nie są wysyłane z informacją, że alert został rozwiązany. Tylko Ty, który rozumie dokładny warunek lub przyczynę logiki osadzonej w zapytaniu analitycznym, możesz [oznaczyć alert jako zamknięty w](alerts-managing-alert-states.md) razie potrzeby.

## <a name="pricing-and-billing-of-log-alerts"></a>Wycena i rozliczanie alertów dzienników

Ceny mające zastosowanie do alertów dziennika są podane na stronie [cennik usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/) Na rachunkach platformy Azure alerty `microsoft.insights/scheduledqueryrules` dziennika są reprezentowane jako typ z:

- Alerty dziennika w usłudze Application Insights wyświetlane z dokładną nazwą alertu wraz z grupą zasobów i właściwościami alertów
- Alerty dziennika w usłudze Log Analytics wyświetlane z dokładną nazwą alertu wraz z grupą zasobów i właściwościami alertów; podczas tworzenia przy użyciu [interfejsu API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

[Starszy interfejs API usługi Log Analytics](../../azure-monitor/platform/api-alerts.md) zawiera akcje alertów i harmonogramy w ramach zapisanego wyszukiwania usługi Log Analytics, a nie odpowiednie zasoby platformy [Azure.](../../azure-resource-manager/management/overview.md) W związku z tym, aby włączyć rozliczenia dla takich starszych alertów dziennika utworzonych dla usługi Log Analytics przy użyciu witryny Azure portal **bez** [przełączania się do nowego interfejsu API](../../azure-monitor/platform/alerts-log-api-switch.md) lub za pośrednictwem [starszego interfejsu API usługi Log Analytics](../../azure-monitor/platform/api-alerts.md) — ukryte pseudo reguły alertów są tworzone w celu `microsoft.insights/scheduledqueryrules` rozliczania na platformie Azure. Ukryte reguły pseudo alertów `microsoft.insights/scheduledqueryrules` utworzone dla `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` rozliczeń, jak pokazano, jak wraz z grupy zasobów i właściwości alertów.

> [!NOTE]
> Jeśli istnieją nieprawidłowe znaki, takie `<, >, %, &, \, ?, /` jak `_` są obecne, zostaną one zastąpione w ukrytej nazwy reguły pseudo alertów, a zatem również na rachunku platformy Azure.

Aby usunąć ukryte zasoby scheduleQueryRules utworzone do rozliczania reguł alertów przy użyciu [starszego interfejsu API usługi Log Analytics,](api-alerts.md)użytkownik może wykonać dowolną z następujących czynności:

- Każdy użytkownik może [przełączać preferencje interfejsu API dla reguł alertów w obszarze roboczym usługi Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) i bez utraty ich reguł alertów lub monitorowania przenieść do interfejsu [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)zgodny z usługą Azure Resource Manager. W ten sposób wyeliminować potrzebę pseudo ukryte reguły alertów dla rozliczeń.
- Lub jeśli użytkownik nie chce przełączać preferencji interfejsu API, użytkownik będzie musiał **usunąć** oryginalny harmonogram i akcję alertu przy użyciu [starszego interfejsu API usługi Log Analytics](api-alerts.md) lub usunąć w [witrynie Azure portal oryginalną regułę alertu dziennika](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Ponadto dla ukrytego harmonogramuQueryrules zasobów utworzonych do rozliczeń reguł alertów przy użyciu [starszego interfejsu API usługi Log Analytics,](api-alerts.md)każda operacja modyfikacji, taka jak PUT, zakończy się niepowodzeniem. Jako `microsoft.insights/scheduledqueryrules` typ pseudo reguły są na potrzeby rozliczeń reguły alertów utworzone przy użyciu [starszego interfejsu API usługi Log Analytics](api-alerts.md). Wszelkie modyfikacje reguły alertu powinny być wykonywane przy użyciu [starszego interfejsu API usługi Log Analytics](api-alerts.md) (lub) użytkownik może [przełączyć preferencje interfejsu API dla reguł alertów,](../../azure-monitor/platform/alerts-log-api-switch.md) aby zamiast tego używać [scheduledQueryRules API.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tworzeniu alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-log.md).
* Poznaj [element webhook w alertach dziennika na platformie Azure.](alerts-log-webhook.md)
* Dowiedz się więcej o [alertach platformy Azure](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej o [usłudze Application Insights](../../azure-monitor/app/analytics.md).
* Dowiedz się więcej o [usłudze Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
