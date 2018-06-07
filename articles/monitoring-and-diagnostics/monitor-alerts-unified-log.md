---
title: Rejestrowania alertów w monitorze Azure - alerty | Dokumentacja firmy Microsoft
description: Wyzwalanie wiadomości e-mail, powiadomienia, należy wywołać adresów URL witryny sieci Web (elementy webhook) lub automatyzacji, po spełnieniu warunków zapytania analityczne, które określisz Azure alertów.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 175e512d0bdaa84d5251f4bbdb09aed3aed436f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638725"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Alerty dziennika w monitorze Azure - alertów 
Ten artykuł zawiera szczegółowe informacje o alertach dziennika są jednego z typów obsługiwanych w ciągu nowe alerty [alerty Azure](monitoring-overview-unified-alerts.md) i zezwalają na używanie platforma do analiz platformy Azure jako podstawa alertów... Aby uzyskać więcej informacji o alertach Metryka przy użyciu dzienników, zapoznaj się [niemal alerty metryki czasu rzeczywistego](monitoring-near-real-time-metric-alerts.md)


Dziennik Alert składa się z dziennika wyszukiwania reguł utworzonych dla [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Dziennik wyszukiwania reguły alertu - definicji i typów

Dziennik wyszukiwania reguły są tworzone przez Azure alerty do automatycznego uruchamiania kwerend w określonym dzienniku w regularnych odstępach czasu.  Jeśli wyniki zapytania dziennika spełniających kryteria określonego, tworzony jest rekord alertu. Reguła następnie automatycznie uruchomić co najmniej jednej akcji przy użyciu [grupy akcji](monitoring-action-groups.md). 

Dziennik wyszukiwania reguł są określone przez następujące informacje:
- **Zaloguj się zapytania**.  Zapytanie, do którego jest uruchamiany za każdym razem, gdy generowane reguły alertów.  Rekordów zwróconych przez to zapytanie są używane do określenia, czy alert jest tworzony. *Azure Application Insights* zapytania mogą również obejmować [wywołania międzyaplikacyjnej](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), o ile użytkownik ma uprawnienia dostępu do aplikacji zewnętrznych. 

    > [!IMPORTANT]
    > Obsługuje z [między zapytania aplikacji dla usługi Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) jest w wersji zapoznawczej — maksymalnie 2 lub więcej aplikacji za pomocą funkcji i środowisko użytkownika mogą ulec zmianie. Użycie [między zapytania obszaru roboczego](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) i [zasobów między zapytanie dla analizy dzienników](../log-analytics/log-analytics-cross-workspace-search.md) jest obecnie **nieobsługiwane** Azure alertów.

- **Okres czasu**.  Określa przedział czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Okres ogranicza dane pobierane dla zapytania dziennika uniemożliwić nadużycia oraz prowadzenia dowolnego polecenia na czas (takich jak temu) używanych w zapytaniu dziennika. <br>*Na przykład jeśli okres czasu jest ustawiona na 60 minut, a uruchomieniu kwerendy, godzina 13:15 będzie, tylko rekordy między 12:15:00 a 13:15 będzie zwracany jest można wykonać kwerendy dziennika. Teraz, jeśli zapytanie dziennika używa czasu polecenia, takich jak temu 7d, zapytania dziennika zostanie uruchomione tylko dla danych między 12:15:00 a 13:15:00 - tak, jakby istnieją dane dla ostatnich 60 minut. A nie na siedem dni danych określone w zapytaniu dziennika.*
- **Częstotliwość**.  Określa, jak często mają być uruchamiane zapytania. Może być dowolną wartość z zakresu od 5 minut do 24 godzin. Powinna być równa lub mniejsza niż okres czasu.  Jeśli wartość jest większa niż okres czasu, istnieje ryzyko rekordów jest pominięte.<br>*Rozważmy na przykład okres 30 minut i częstotliwość 60 minut.  Jeśli zapytanie jest uruchomione 1:00, zwraca rekordów między 12:30 i 1:00 PM.  Przy następnym uruchom zapytanie to 2:00 po zwróci rekordów między 1:30 i 2:00.  Nigdy nie będzie można obliczyć wszystkie rekordy między 1:00 i 1:30.*
- **Próg**.  Wyniki wyszukiwania dziennika są oszacowywane, aby określić, czy można utworzyć alertu.  Próg jest różne dla różnych typów reguł alertów dziennik wyszukiwania.

Dziennik wyszukiwania reguł można go dla [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), mogą być dwa typy. Każdy z tych typów jest szczegółowo opisane w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, jeśli liczba rekordów zwróconych przez wyszukiwanie dziennika przekracza podanej liczby.
- **[Metryki pomiaru](#metric-measurement-alert-rules)**.  Alert utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami, które wykraczają poza określoną wartość progową.

Dostępne są następujące różnice między typami reguły alertów.

- *Liczba wyników* reguły alertów zawsze tworzy pojedynczy podczas alertu, *metryki pomiaru* alertu zasada tworzy alert dla każdego obiektu, który przekracza wartość progową.
- *Liczba wyników* reguły alertów tworzą alert po przekroczeniu progu jeden raz. *Metryki pomiaru* reguły alertów można utworzyć alertu, po przekroczeniu progu określona liczba razy w określonym interwale.

### <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów
**Liczba wyników** reguły alertów Utwórz pojedynczy alert, kiedy to liczba rekordów zwróconych przez zapytanie wyszukiwania przekraczają określony próg. Ten typ alertu jest idealne rozwiązanie w przypadku pracy z zdarzenia, takie jak dzienniki zdarzeń systemu Windows, Syslog odpowiedzi aplikacji sieci Web i niestandardowe dzienniki.  Można utworzyć alertu, podczas tworzenia pobiera zdarzeń określony błąd, lub wielu zdarzeń błędu są tworzone w określonym przedziale czasu.

**Próg**: próg liczby reguł alertów wyników jest większa lub mniejsza od określonej wartości.  Jeśli liczba rekordów zwróconych przez wyszukiwanie dziennika zgodna te kryteria, tworzona jest alert.

Aby alertów na pojedyncze zdarzenie, ustaw liczbę wyników na wartość większą niż 0 i sprawdź, czy wystąpienie jednego zdarzenia, który został utworzony od czasu ostatniego uruchomienia zapytania. Niektóre aplikacje mogą rejestrować okazjonalne błąd, który nie należy koniecznie zgłosi alert.  Na przykład aplikacja może ponów próbę wykonania procesu, który utworzył zdarzenie błędu i powiodło się podczas następnego.  W takim przypadku nie można utworzyć alertu, chyba że wiele zdarzeń są tworzone w określonym przedziale czasu.  

W niektórych przypadkach warto utworzyć alert w przypadku braku zdarzeń.  Na przykład procesu mogą rejestrować regularnego zdarzenia w celu wskazania, że działa prawidłowo.  Jeśli go nie rejestrować jedno z tych zdarzeń w danym okresie, powinny być tworzone alertu.  W takim przypadku należy ustawić wartość progową **mniej niż 1**.

#### <a name="example"></a>Przykład
Rozważmy scenariusz, w którym chcesz wiedzieć, kiedy aplikacji opartych na sieci web zapewnia odpowiedzi dla użytkowników z kodem 500 (czyli) wewnętrzny błąd serwera. Należy utworzyć regułę alertu z następującymi szczegółami:  
- **Zapytanie:** żądań | gdzie resultCode == "500"<br>
- **Okres czasu:** 30 minut<br>
- **Alert częstotliwości:** pięć minut<br>
- **Wartość progowa:** dużą niż 0<br>

Następnie alert może uruchomić kwerendę co 5 minut z 30 minut dane — do wyszukania wszystkich rekordów gdzie kod wyniku: 500. Jeśli zostanie znaleziony nawet jednego takiego zapisu, generowane alert i wyzwala Akcja skonfigurowana.

### <a name="metric-measurement-alert-rules"></a>Metryki pomiaru reguły alertów

- **Metryki pomiaru** reguły alertów tworzyć alert dla każdego obiektu w zapytaniu z wartością, która przekracza określoną wartość progową.  Mają one następujące różnice różne **liczba wyników** reguły alertów.
- **Funkcję agregacji**: Określa obliczeń, który jest wykonywane i potencjalnie liczbową pole do zagregowania.  Na przykład **count()** zwraca liczbę rekordów w zapytaniu, **avg(CounterValue)** zwraca średnią pole równowartości dla interwału. Funkcji agregującej w zapytaniu muszą być o nazwie wywołuje: AggregatedValue i podaj wartość liczbową. 
- **Pole grupy**: zostaje utworzony rekord z zagregowane wartości dla poszczególnych wystąpień tego pola, a alert jest generowany dla każdego.  Na przykład, jeśli chcesz generować alert dla każdego komputera, możesz użyć **przez komputer** 

    > [!NOTE]
    > Dla metryki pomiaru reguły alertów, które są oparte na usługi Application Insights można określić pole do grupowania danych. Aby to zrobić, użyj **agregacji na** opcja w definicji reguły.   
    
- **Interwał**: Określa przedział czasu, w którym są agregowane.  Na przykład, jeśli określono **pięć minut**, może zostać utworzony rekord dla każdego wystąpienia pola grupy agregowane co 5 minut w przedziale czasu określony dla alertu.

    > [!NOTE]
    > Funkcja Kosza musi używany w zapytaniu, aby określić interwał. Jako bin() może spowodować przedziały czasu nierówne — Alert automatycznie przekonwertuje bin polecenia polecenia bin_at o odpowiedni czas w czasie wykonywania, zapewnić wyniki ze stałym punktem
    
- **Próg**: próg metryki pomiaru reguły alertów jest definiowana za pomocą wartości zagregowanej i liczbę naruszeń.  Dowolnego punktu danych w dzienniku wyszukiwania przekracza tę wartość, jest uznawane za naruszenia.  Jeśli liczba naruszeń w dla dowolnego obiektu w wynikach przekracza określoną wartość, alert zostanie utworzony dla tego obiektu.

#### <a name="example"></a>Przykład
Rozważmy scenariusz, w którym chce alert dowolnego komputera przekroczeniu użycie procesora przez 90% trzy razy ponad 30 minut.  Należy utworzyć regułę alertu z następującymi szczegółami:  

- **Zapytanie:** wydajności | gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" | Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 5 m), komputer<br>
- **Okres czasu:** 30 minut<br>
- **Alert częstotliwości:** pięć minut<br>
- **Wartość agregacji:** dużą niż 90<br>
- **Na podstawie wyzwalania alertu:** łączna liczba naruszeń większej niż 2<br>

Zapytanie spowodowałoby średnią wartość dla każdego komputera co 5 minut.  To zapytanie może działać co 5 minut przez dane zbierane przez poprzednie 30 minut.  Poniżej przedstawiono przykładowe dane na trzech komputerach.

![Przykładowe wyniki zapytania](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

W tym przykładzie oddzielne alerty zostałyby utworzone dla srv02 i srv03, ponieważ ich naruszenia progu 90% 3 razy w okresie czasu.  Jeśli **na podstawie wyzwalania alertu:** zostały zmienione na **kolejno** , a następnie alert będzie można utworzyć tylko w przypadku srv03, ponieważ jego naruszenia progu dla trzech kolejnych próbek.


## <a name="log-search-alert-rule---creation-and-modification"></a>Dziennik wyszukiwania reguły alertu — tworzenie i modyfikowanie

Alert dziennika, a także jej consisting reguły alertu wyszukiwania dziennika można przeglądać, utworzony lub zmodyfikowane od:
- Azure Portal
- REST API (w tym za pomocą programu PowerShell)
- Szablony usługi Azure Resource Manager

### <a name="azure-portal"></a>Azure Portal
Począwszy od systemu [nowe alerty Azure](monitoring-overview-unified-alerts.md), teraz użytkownicy mogą zarządzać wszystkie typy alertów w portalu Azure z pojedynczej lokalizacji i podobne kroki. Dowiedz się więcej o [przy użyciu nowych alertów Azure](monitor-alerts-unified-usage.md).

Ponadto użytkownicy mogą doskonała ich zapytania w platformie analizy wyboru na platformie Azure, a następnie *zaimportować je do użycia w alerty po zapisaniu zapytania*. Kroki do wykonania:
- *Dla usługi Application Insights*: portal analityka przejdź do weryfikacji zapytań i ich wyników. Następnie zapisz o unikatowej nazwie w *udostępnione zapytania*.
- *W celu wykonania analizy dziennika*: Przejdź do wyszukiwania dziennika weryfikacji zapytań i ich wyników. Następnie użyj zapisanie o unikatowej nazwie w każdej kategorii.

Następnie po [tworzenia alertu dziennika w alertach ](monitor-alerts-unified-usage.md), zobacz zapisanego zapytania, które są wyświetlane jako typ sygnału **dziennika (zapisane zapytanie)**; jak pokazano w poniższym przykładzie: ![zapisanego zapytania, które są importowane do alertów](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Przy użyciu **dziennika (zapisane zapytanie)** wynikiem importowania do alertów. Dlatego wszelkie zmiany dokonywane po w module analiz nie będzie odbicia w dzienniku Szukaj reguł alertów i na odwrót.

### <a name="rest-apis"></a>Interfejsy API REST
Interfejsy API są dostępne dla dziennika alerty są RESTful i jest dostępny za pośrednictwem interfejsu REST API usługi Azure Resource Manager. Dlatego jest dostępna za pośrednictwem programu PowerShell, a także inne opcje korzystają z interfejsów API.

Aby uzyskać szczegółowe informacje, a także przykłady przy użyciu interfejsu API REST, sprawdź, zobacz:
- [Logowania interfejsu API REST Alert Analytics](../log-analytics/log-analytics-api-alerts.md) — do tworzenia i zarządzania regułami alertów wyszukiwania dziennika dla usługi Analiza dzienników Azure
- [Azure Monitor zaplanowane zapytania reguł interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) — do tworzenia i zarządzania regułami alertów wyszukiwania dziennika dla usługi Azure Application Insights

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Użytkownicy mogą również używać elastyczności przez [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do tworzenia i aktualizacji zasobów — do tworzenia lub aktualizowania alerty dziennika.

Aby uzyskać szczegółowe informacje, a także przykłady przy użyciu szablonów usługi Resource Manager, sprawdź, zobacz:
- [Zapisane wyszukiwania i alerty zarządzania](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) dla dziennika alerty oparte na Analiza dzienników Azure
- [Zaplanowane regułę zapytania](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) dla dziennika alerty oparte na usłudze Azure Application Insights
 

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [rejestrowania alertów w usłudze Azure](monitor-alerts-unified-log-webhook.md).
* Dowiedz się więcej o nowe [alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md).
* Dowiedz się więcej o [analizy dzienników](../log-analytics/log-analytics-overview.md).    
