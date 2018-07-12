---
title: Alerty dzienników w usłudze Azure Monitor
description: Wyzwalanie wiadomości e-mail, powiadomień, wywołaj adresów URL witryn sieci Web (elementy webhook), oraz automatyzacją po spełnieniu warunki zapytania analityczne, które określisz dla alertów platformy Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f36f05789424cfd3213525dd501333f852a0d9c2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971724"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Alerty dzienników w usłudze Azure Monitor — alerty 
Ten artykuł zawiera szczegółowe informacje o alertów dzienników to jeden z typów alertów, które obsługują nowe [Azure Alerts](monitoring-overview-unified-alerts.md) i zezwolić użytkownikom na stosowanie platforma analiz platformy Azure jako podstawa dla alertów... Szczegóły alertów metryk, przy użyciu dzienników, można znaleźć [niemal metryki alerty w czasie rzeczywistym](monitoring-near-real-time-metric-alerts.md)


Alert dziennika składa się z wyszukiwania w dziennikach reguł utworzonych dla [usługi Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Wyszukiwania reguł alertów dzienników — definicja i typów

Reguły wyszukiwania dzienników są tworzone dzięki alertom w usłudze Azure do automatycznego uruchamiania zapytania określonego dziennika w regularnych odstępach czasu.  Jeśli wyniki zapytania dzienników pasują do określonych kryteriów, zostaje utworzony rekord alertu. Reguła następnie może automatycznie uruchomić jedną lub więcej akcji przy użyciu [grup akcji](monitoring-action-groups.md). 

Dziennik wyszukiwania reguł są definiowane przez następujące informacje:
- **Zaloguj się zapytania**.  Zapytanie, które jest uruchamiane za każdym razem, gdy reguła alertu jest uruchamiana.  Rekordów zwróconych przez tę kwerendę są używane do określenia, czy alert jest tworzony. *Usługa Azure Application Insights* zapytanie może również obejmować [wywołań między platformami](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), o ile użytkownik ma wystarczające uprawnienia dostępu do aplikacji zewnętrznych. 

    > [!IMPORTANT]
    > Suppport z [krzyżowego zapytania aplikacji dla usługi Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) jest w wersji zapoznawczej — funkcje są ograniczone do korzystania z aplikacji co najmniej 2 i środowisko użytkownika może ulec zmianie. Użycie [krzyżowego zapytania obszaru roboczego](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) i [zapytania obejmujące wiele zasobów usługi Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) jest obecnie **nieobsługiwane** w alertach Azure.

- **Przedział czasu**.  Określa zakres czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (np. temu) używanych w zapytaniu dziennika. <br>*Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu jest zwracana do wykonania zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia, takie jak temu teraz (7 dni), zapytania dotyczącego dziennika zostanie uruchomione tylko dla danych zakresu od 12:15:00 do 13:15:00 - tak, jakby istnieją dane dla ostatnich 60 minut. A nie przez siedem dni, danych, jak to określono w dzienniku zapytań.*
- **Częstotliwość**.  Określa, jak często należy uruchamiać zapytanie. Może to być wartość z zakresu od 5 minut do 24 godzin. Powinna być równa lub mniejsza niż okres czasu.  Jeśli wartość jest większa niż okres czasu, istnieje ryzyko rekordów jest pominięte.<br>*Na przykład należy wziąć pod uwagę okres 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchomione 1:00, zwraca rekordy od 12:30 do 1:00 PM.  Przy następnym uruchomić zapytanie jest 2:00, jeśli zwróci rekordów między 1:30 – 2:00.  Nigdy nie oceniono rekordy między 1:00 i 1:30.*
- **Próg**.  Wyniki przeszukiwania dzienników są oceniane w celu ustalenia, czy należy utworzyć alert.  Próg różni się dla różnych typów reguł alertów wyszukiwania dziennika.

Reguły wyszukiwania dziennika można je [usługi Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), mogą być dwojakiego rodzaju. Każdy z tych typów opisano szczegółowo w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku przekracza określoną liczbę.
- **[Pomiar metryki](#metric-measurement-alert-rules)**.  Alert został utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami, które przekracza określoną wartość progową.

Dostępne są następujące różnice między typami reguły alertu.

- *Liczba wyników* reguł alertów zawsze tworzy chwilę alertów, pojedynczy *pomiar metryki* alertu zasada tworzy alert dla każdego obiektu, który przekracza wartość progową.
- *Liczba wyników* reguł alertów Tworzenie alertu, po przekroczeniu progu jeden raz. *Pomiar metryki* reguł alertów można utworzyć alertu, po przekroczeniu progu pewną liczbę uruchomień w określonym interwale.

### <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów
**Liczba wyników** reguł alertów Utwórz pojedynczy alert, gdy liczba rekordów zwróconych przez zapytanie wyszukiwania przekracza określoną wartość progową. Ten typ alertu jest idealny dla pracy z wydarzeniami takimi jak Windows, dzienniki zdarzeń, usługi Syslog, odpowiedzi aplikacji sieci Web i dzienników niestandardowych.  Można utworzyć alert, gdy tworzony zdarzeń określony błąd lub wielu zdarzeń błędu są tworzone w określonym przedziale czasu.

**Próg**: próg liczby wyników reguły alertów jest większa lub mniejsza od określonej wartości.  Jeśli liczba rekordów zwracanych przez wyszukiwanie w dzienniku odpowiadają te kryteria, jest tworzony alert.

Aby otrzymywać alerty na pojedyncze zdarzenie, ustaw liczbę wyników na wartość większą niż 0, a następnie wyszukaj wystąpienie pojedyncze zdarzenie, który został utworzony od czasu ostatniego uruchomienia zapytania. Niektóre aplikacje mogą rejestrować okazjonalne błąd, który nie należy koniecznie zgłoszenie alertu.  Na przykład aplikacja może ponów próbę wykonania procesu, który utworzył zdarzenie błędu i kończyło się powodzeniem następnym razem.  W tym przypadku nie można utworzyć alert, chyba że wiele zdarzeń zostaje utworzonych w określonym przedziale czasu.  

W niektórych przypadkach warto utworzyć alert w przypadku braku zdarzenia.  Na przykład procesu mogą rejestrować regularne zdarzenia w celu wskazania, że działa prawidłowo.  Jeżeli nie jego wylogować jedno z tych zdarzeń w danym okresie, powinien zostać utworzony alert.  W takim przypadku należy ustawić wartość progową **mniej niż 1**.

#### <a name="example"></a>Przykład
Rozważmy scenariusz, w którym chcesz wiedzieć, kiedy aplikacji opartych na sieci web zapewnia odpowiedzi dla użytkowników z kodem 500 (to znaczy) wewnętrzny błąd serwera. Należy utworzyć regułę alertu z następującymi szczegółami:  
- **Zapytanie:** żądań | gdzie resultCode == "500"<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość progowa:** większy niż 0<br>

Alert będzie uruchomi zapytanie co 5 minut, 30 minut danych — do wyszukiwania dowolnego rekordu, gdzie kod wyniku: 500. Jeśli jeszcze jeden taki rekord zostanie znaleziony, wyzwala alert i wyzwala akcję skonfigurowane.

### <a name="metric-measurement-alert-rules"></a>Reguły alertu pomiaru metryki

- **Pomiar metryki** reguł alertów Utwórz alert dla każdego obiektu w zapytaniu o wartości, który przekroczy określony próg.  Mają one różne następujące różnice względem **liczba wyników** reguły alertów.
- **Funkcję agregacji**: Określa, obliczenia, które jest wykonywane i potencjalnie liczbową pola do zagregowania.  Na przykład **count()** zwraca liczbę rekordów w zapytaniu **avg(CounterValue)** zwraca średnią pola CounterValue dla interwału. Funkcja agregująca w zapytaniu musi być o nazwie wywołuje: elementy AggregatedValue i podaj wartość liczbową. 
- **Grupy pól**: rekord z wartością zagregowaną jest tworzony dla każdego wystąpienia to pole, a alert jest generowany dla każdego.  Na przykład, jeśli chcesz generować alert dla każdego komputera, można użyć **według komputera** 

    > [!NOTE]
    > Dla pomiar metryki reguł alertów, które są oparte na usłudze Application Insights można określić pole do grupowania danych. Aby to zrobić, należy użyć **agregowane na** opcja w definicji reguły.   
    
- **Interwał**: Określa przedział czasu, przez który dane są agregowane.  Na przykład, jeśli określono **pięć minut**, może zostać utworzony rekord dla każdego wystąpienia pola grupy agregowane w 5-minutowych interwałach w przedziale czasu określony dla alertu.

    > [!NOTE]
    > Funkcja bin musi używane w zapytaniu, aby określić interwał. Jako bin() może spowodować nierówne interwałami — Alert zostaną automatycznie przekonwertowane bin polecenia polecenie bin_at z odpowiednim czasie w czasie wykonywania, aby upewnić się, wyniki ze stałym punktem
    
- **Próg**: próg reguły alertu pomiaru metryki jest definiowany przez wartości zagregowanej i liczbę naruszeń.  Dowolny punkt danych podczas wyszukiwania dziennika przekracza tę wartość, jest uznawane za naruszenia.  Jeśli liczba naruszeń w dla dowolnego obiektu w wynikach przekracza określoną wartość, alert zostanie utworzony dla tego obiektu.

#### <a name="example"></a>Przykład
Rozważmy scenariusz, w którym alert potrzebowała przekroczeniu użycie procesora przez 90% trzykrotnie ponad 30 minut, przez dowolny komputer.  Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** wydajności | gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" | summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 5 min), komputer<br>
- **Okres:** 30 minut<br>
- **Częstotliwość alertów:** pięć minut<br>
- **Wartość agregacji:** większy niż 90<br>
- **Wyzwalaj alert na podstawie:** łączna liczba naruszeń większej niż 2<br>

Zapytanie spowodowałoby średnią wartość dla każdego komputera w 5-minutowych odstępach czasu.  To zapytanie zostałoby uruchomione co 5 minut przez dane zebrane przez poprzednie 30 minut.  Poniżej przedstawiono przykładowe dane na trzech komputerach.

![Przykładowe wyniki zapytania](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

W tym przykładzie oddzielne alerty zostałyby utworzone srv02 i srv03, ponieważ ich naruszenia progu 90% 3 razy w okresie czasu.  Jeśli **Wyzwalaj alert na podstawie:** zostały zmienione na **kolejno** , a następnie będzie można utworzyć alertu tylko w przypadku srv03, ponieważ jego naruszenia progu dla trzech kolejnych próbek.


## <a name="log-search-alert-rule---creation-and-modification"></a>Wyszukiwania reguł alertów dzienników — tworzenie i modyfikowanie

Alert dziennika, a także jej consisting reguł alertów wyszukiwania dzienników mogą być wyświetlane, utworzone lub zmodyfikowane od:
- Azure Portal
- REST API (w tym za pomocą programu PowerShell)
- Szablony usługi Azure Resource Manager

### <a name="azure-portal"></a>Azure Portal
Od momentu wprowadzenia [nowych alertów platformy Azure](monitoring-overview-unified-alerts.md), teraz użytkownicy mogą zarządzać wszystkich typów alertów w witrynie Azure portal z jednej lokalizacji i podobne kroki. Dowiedz się więcej o [przy użyciu nowej usługi Azure Alerts](monitor-alerts-unified-usage.md).

Ponadto użytkownicy mogą doskonała ich zapytań w platforma analityczna wybranego na platformie Azure i następnie *zaimportować je do użytku w alertach, zapisując zapytanie*. Instrukcje:
- *Dla usługi Application Insights*: Przejdź do portalu usługi Analytics, sprawdź poprawność zapytania i jego wyniki. Następnie zapisz o unikatowej nazwie w *zapytania udostępnione*.
- *Dla usługi Log Analytics*: Przejdź do przeszukiwania dzienników, sprawdź zapytania i jego wyniki. Następnie użyj Zapisz o unikatowej nazwie w dowolnej kategorii.

A następnie po [Tworzenie alertu dziennika w alertach ](monitor-alerts-unified-usage.md), zobacz zapisanego zapytania, które są wyświetlane jako typ sygnału **dziennika (zapisane zapytanie)**; jak pokazano w poniższym przykładzie: ![zapisane zapytanie, które są importowane z alertami](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Za pomocą **dziennika (zapisane zapytanie)** skutkuje importu do alertów. Dlatego wszelkie zmiany wprowadzone po w usłudze Analytics nie będzie odbijającą reguł alertów wyszukiwania dziennika i na odwrót.

### <a name="rest-apis"></a>Interfejsy API REST
Interfejsy API są dostarczane dla dziennika alertów są zgodne ze specyfikacją REST i możliwy za pośrednictwem interfejsu REST API usługi Azure Resource Manager. Dlatego jest dostępna za pośrednictwem programu PowerShell, a także inne opcje, aby skorzystać z interfejsów API.

Aby uzyskać szczegółowe informacje, a także przykłady dotyczące używania interfejsu API REST, zawiera, zobacz:
- [Interfejs API REST alertów analizy dziennika](../log-analytics/log-analytics-api-alerts.md) — do tworzenia i zarządzania reguł alertów wyszukiwania dziennika dla usługi Azure Log Analytics
- [Usługa Azure Monitor zaplanowane zapytania zasady interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) — do tworzenia i zarządzania reguł alertów wyszukiwania dziennika dla usługi Azure Application Insights

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Użytkownicy mogą również używać elastyczności, jaką [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do tworzenia i aktualizowania zasobów — do tworzenia lub aktualizowania alertów dzienników.

Aby uzyskać szczegółowe informacje, a także przykłady dotyczące używania szablonów usługi Resource Manager zawiera, zobacz:
- [Zapisane wyszukiwania i alerty zarządzania](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) dla dziennika alertów w oparciu o usługi Azure Log Analytics
- [Zaplanowane reguły zapytania](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) dla dziennika alertów w oparciu o usługi Azure Application Insights
 

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [alerty dzienników w usłudze Azure](monitor-alerts-unified-log-webhook.md).
* Dowiedz się więcej o nowym [Azure Alerts](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md).
* Dowiedz się więcej o [usługi Log Analytics](../log-analytics/log-analytics-overview.md).    
