---
title: Opis alertów w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Alerty w analizy dzienników zidentyfikować ważne informacje zawarte w repozytorium OMS i aktywne powiadamia użytkownika o problemy lub akcji, aby je poprawić.  W tym artykule opisano różne rodzaje reguły alertów i jak są zdefiniowane.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: cf1842c6abbbfd767184d8f480a5f3a5fd654ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178674"
---
# <a name="understanding-alerts-in-log-analytics"></a>Opis alertów w analizy dzienników

> [!NOTE]
> Alerty w analizy dzienników są [rozszerzaną na platformie Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Informacje przedstawione w tym artykule nadal może służyć do definiowania szczegóły alertów w portalu Azure, używanego przez wyszukiwanie analizy dzienników.

Alerty w usłudze Log Analytics identyfikują ważne informacje w repozytorium usługi Log Analytics. W tym artykule omówiono niektóre decyzji projektowych, które należy podjąć takie decyzje na podstawie częstotliwości zbierania danych jest kwerendy, losowego opóźnienia z wprowadzanie danych prawdopodobnie spowodowane opóźnieniem sieci lub wydajności przetwarzania i zatwierdzania danych w Dzienniku Obszar roboczy analizy. Również szczegółowe reguły jak alertów w pracach analizy dzienników i opisano różnice między różnych typów reguł alertów.

W procesie tworzenia reguły alertów zobacz następujące artykuły:

- Tworzyć reguły alertów za pomocą [portalu Azure](log-analytics-alerts-creating.md)
- Tworzyć reguły alertów za pomocą [szablonu usługi Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Tworzyć reguły alertów za pomocą [interfejsu API REST](log-analytics-api-alerts.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Szczegółowe informacje o częstotliwości zbierania danych dla różnych rozwiązań i typu danych są dostępne w [szczegóły kolekcji danych](log-analytics-add-solutions.md#data-collection-details) artykułu Omówienie rozwiązania. Jak wspomniano w tym artykule, częstotliwość zbierania może być jako rzadkim jako co siedem dni do *na powiadomienie*. Należy zrozumieć i należy wziąć pod uwagę częstotliwość zbierania danych, przed rozpoczęciem konfigurowania alertów. 

- Częstotliwość zbierania Określa, jak często agent pakietu OMS wysyłania danych do analizy dzienników. Na przykład, jeśli częstotliwość kolekcji to 10 minut i nie bez innych opóźnień w systemie, następnie sygnatury czasowe przekazywanych danych może być dowolnym przedziału od 0 do 10 minut przed dodaniem go do repozytorium i jest wyszukiwanie w analizy dzienników.

- Przed wyzwoleniu alertu danych musi być przystosowana do repozytorium, aby była ona dostępna po otrzymaniu kwerendy. Z powodu opóźnienia opisane powyżej częstotliwość kolekcji nie jest taki sam jak czas, który dane są dostępne dla zapytań. Na przykład gdy dane mogą być zbierane dokładnie na 10 minut, dane są dostępne w repozytorium danych w nieregularnych odstępach czasu. Hipotetycznie danych zebranych w odstępach czasu zero, 10 i 20 minut mogą być dostępne wyszukiwania 25, 28 i 35 minut odpowiednio lub w nieregularnych odstępach zależeć od wprowadzanie opóźnienia. Najgorszy te opóźnienia jest udokumentowany w [umowy SLA dla analizy dzienników](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), nie obejmuje opóźnienia wynikające z kolekcji częstotliwość lub sieci opóźnienia między komputerem a usługi analizy dzienników.


## <a name="alert-rules"></a>Reguły alertów

Alerty są tworzone przez reguły alertów, które automatycznie uruchamiają przeszukiwanie dzienników w regularnych odstępach czasu. Jeśli wyniki wyszukiwania dziennika spełniających kryteria określonego tworzony jest rekord alertu. Następnie reguła może automatycznie uruchomić jedną lub kilka akcji, aby aktywnie powiadomić użytkownika o alercie lub wywołać inny proces. Różnych typów reguł alertów używać różnych logikę do wykonywania tej analizy.

![Alerty usługi Log Analytics](media/log-analytics-alerts/overview.png)

Ponieważ ma oczekiwanego opóźnienia z wprowadzanie danych dziennika, bezwzględnego czasu między indeksowanie danych i gdy jest ona dostępna do wyszukiwania może być nieprzewidywalne. Dostępność w pobliżu czasie rzeczywistym danych zebranych powinien brane pod uwagę podczas definiowania reguł alertów.

Istnieje zależność między niezawodności alertów i elastyczność alertów. Możesz skonfigurować alert parametry, aby zminimalizować fałszywe alerty i Brak alertów lub możesz wybrać alert parametry, aby szybko reagować na warunki, które są monitorowane, ale czasami generuje alerty FAŁSZ lub pominięta.

Reguły alertów są określone przez następujące informacje:

- **Dziennik wyszukiwania**. Zapytanie, do którego jest uruchamiany za każdym razem, gdy generowane reguły alertów. Rekordów zwróconych przez to zapytanie są używane do określenia, czy alert jest tworzony.
- **Przedział czasu**. Określa przedział czasu dla zapytania. Zapytanie zwraca tylko rekordy utworzone w tym zakresie czasu bieżącego. Może to być dowolna wartość od 5 minut do 24 godzin. Zakres musi być dostatecznie szerokie, aby pomieścić uzasadnione opóźnienia w wprowadzanie. Przedział czasu musi mieć dwa razy długość najdłuższego opóźnienia, które mają być w stanie obsłużyć.<br> Na przykład jeśli chcesz otrzymywać alerty są wiarygodne opóźnienia 30 minut, następnie zakres musi być jedną godzinę.

    Istnieją dwa objawy, które mogą wystąpić, jeśli przedział czasu jest za mały.

    - **Brak alertów**. Przykładowa opóźnienie wprowadzanie jest czasami 60 minut, ale w większości przypadków jest 15 minut. Jeśli przedział czasu jest ustawiona na 30 minut, następnie go chybień alert, gdy wartość opóźnienia wynosi 60 minut, ponieważ danych nie jest dostępna dla wyszukiwania, gdy jest wykonywana kwerenda alertu. 
   
        >[!NOTE]
        >Sprawdź, dlaczego alert zostało pominięte w trakcie jest niemożliwe. Na przykład w przypadku powyżej, dane są zapisywane w repozytorium 60 minut po wykonaniu kwerendy alertów. Jeśli alert została pominięta, czy następnego dnia zapytanie jest wykonywane dla interwału czasu poprawne jest zauważeniu następnego dnia, kryteria wyszukiwania dziennika umożliwi dopasowanie wynik. Wydaje się, że alert powinien mieć zostało wyzwolone. W rzeczywistości alert nie został uruchomiony, ponieważ dane nie jest jeszcze dostępna po alertu zapytanie zostało wykonane. 
        >
 
    - **Alerty false**. Czasami alertu zapytania są przeznaczone do identyfikowania braku zdarzeń. Przykładem jest wykrywanie, gdy maszyna wirtualna jest offline przez wyszukiwanie brakujących interwałów pulsu. Jako powyżej, jeśli pulsu nie jest dostępna dla wyszukiwania w oknie alertu następnie alert jest generowany, ponieważ dane pulsu nie została jeszcze można wyszukiwać i w związku z tym nie istnieje. Jest to samo, jakby został legalnie offline maszyny Wirtualnej i nie było żadnych danych pulsu generowane przez nią. Wykonywania zapytania w następnym dniu nad oknem prawidłowego czasu pokazuje, że wystąpiły pulsów i tworzenia alertu nie powiodło się. W rzeczywistości impulsy nie były jeszcze dostępne dla wyszukiwania ponieważ przedział czasu alertów została ustawiona za mały.

- **Częstotliwość**.  Określa, jak często zapytanie powinna być uruchamiana i można wprowadzić bardziej odpowiednie w przypadku normalnych alerty. Wartość może należeć do zakresu od 5 minut do 24 godzin i powinna być równa lub mniejsza niż okno czasu alertu.  Jeśli wartość jest większa niż przedział czasu, istnieje ryzyko rekordów jest pominięte.<br>Jeśli celem jest wiarygodne dla opóźnienia do 30 minut i normalne opóźnienie to 10 minut, przedział czasu musi być jedną godzinę i wartość częstotliwości powinna być 10 minut. To spowoduje wywołanie alertu z danymi, które ma z opóźnieniem 10 minut wprowadzanie od 10 do 20-minut, gdy dane alertu został wygenerowany.<br>Aby uniknąć tworzenia wielu alertów dla tych samych danych, ponieważ przedział czasu jest zbyt długa, opcja pomijania alertów może służyć do pomijania alertów dla co najmniej tak długo, jak przedział czasu.
  
- **Próg**. Wyniki wyszukiwania dziennika są oszacowywane, aby określić, czy można utworzyć alertu. Próg jest różne dla różnych typów reguł alertów.

Każdej reguły alertu w analizy dzienników jest jednym z dwóch typów. Każdy z tych typów jest szczegółowo opisane w kolejnych sekcjach.

- **[Liczba wyników](#number-of-results-alert-rules)**. Pojedynczy alert utworzony, jeśli liczba rekordów zwróconych przez wyszukiwanie dziennika przekracza podanej liczby.
- **[Metryki pomiaru](#metric-measurement-alert-rules)**. Alert utworzony dla każdego obiektu w wynikach wyszukiwania dziennika z wartościami, które wykraczają poza określoną wartość progową.

Dostępne są następujące różnice między typami reguły alertów.

- **Liczba wyników** reguły alertu Zawsze twórz pojedynczy alert chwilę **metryki pomiaru** alertu zasada tworzy alert dla każdego obiektu, który przekracza wartość progową.
- **Liczba wyników** reguły alertów tworzą alert po przekroczeniu progu jeden raz. **Metryki pomiaru** reguły alertów można utworzyć alertu, po przekroczeniu progu określona liczba razy w określonym interwale.

## <a name="number-of-results-alert-rules"></a>Liczba wyników reguły alertów
**Liczba wyników** reguły alertów Utwórz pojedynczy alert, kiedy to liczba rekordów zwróconych przez zapytanie wyszukiwania przekraczają określony próg.

### <a name="threshold"></a>Próg
Próg **liczba wyników** reguły alertu jest większa lub mniejsza od określonej wartości. Jeśli liczba rekordów zwróconych przez wyszukiwanie dziennika zgodna te kryteria, tworzona jest alert.

### <a name="scenarios"></a>Scenariusze

#### <a name="events"></a>Zdarzenia
Ten typ alertu jest idealne rozwiązanie w przypadku pracy z zdarzenia, takie jak dzienniki zdarzeń systemu Windows, Syslog, i niestandardowe dzienniki. Możesz utworzyć alert podczas tworzenia pobiera zdarzeń określony błąd, lub wielu zdarzeń błędu są tworzone w ramach okna określony czas.

Aby alert po wystąpieniu pojedyncze zdarzenie, ustaw liczbę wyników na wartość większą niż 0 i częstotliwość i przedział czasu na 5 minut. Uruchamiające kwerendę co 5 minut i sprawdź, czy wystąpienie jednego zdarzenia, który został utworzony od czasu ostatniego uruchomienia zapytania. Częstotliwość dłużej może być opóźniona czas między zdarzenia są zbierane i tworzony alert.

Niektóre aplikacje mogą rejestrować okazjonalne błąd, który nie należy koniecznie zgłosi alert. Na przykład aplikacja może ponów próbę wykonania procesu, który utworzył zdarzenie błędu i powiodło się podczas następnego. W takim przypadku nie możesz utworzyć alert, jeśli wiele zdarzeń są tworzone w ramach okna określonym czasie.

W niektórych przypadkach warto utworzyć alert w przypadku braku zdarzeń. Na przykład procesu mogą rejestrować regularnego zdarzenia w celu wskazania, że działa prawidłowo. Jeżeli nie rejestrować jedno z tych zdarzeń w oknie określonym czasie, powinien zostać utworzony alert. W takim przypadku należy ustawić wartość progową **mniej niż 1**.

#### <a name="performance-alerts"></a>Alerty wydajności
[Dane dotyczące wydajności](log-analytics-data-sources-performance-counters.md) jest przechowywana jako rekordów w repozytorium OMS podobne do zdarzenia. Jeśli chcesz alert, gdy licznik wydajności przekracza próg określonego progu powinien uwzględnione w zapytaniu.

Na przykład, jeśli chcesz alertów po uruchomieniu procesor ponad 90%, można skorzystać podobnie do następującej kwerendy z progiem dla reguły alertu **większa niż 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

Jeśli chcesz alert, gdy procesor średnio ponad 90% okna określony czas, należy użyć zapytania za pomocą `measure` polecenia podobnie do następującej z progiem dla reguły alertu **większa niż 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90



## <a name="metric-measurement-alert-rules"></a>Metryki pomiaru reguły alertów

>[!NOTE]
> Reguły alertów metryki pomiaru są obecnie w wersji zapoznawczej.

**Metryki pomiaru** reguły alertów tworzyć alert dla każdego obiektu w zapytaniu z wartością, która przekracza określoną wartość progową. Mają one następujące różnice różne **liczba wyników** reguły alertów.

#### <a name="log-search"></a>Wyszukiwanie w dzienniku
Za pomocą dowolnego zapytania dla **liczba wyników** reguły alertu, istnieją wymagania zapytania dla metryki pomiaru reguły alertu.  Musi on zawierać `measure` polecenia do grupowania wyników w określonym polu. To polecenie musi zawierać następujące elementy.


- **Funkcję agregacji**.  Określa obliczeń, który jest wykonywane i potencjalnie liczbową pole do zagregowania. Na przykład **count()** zwraca liczbę rekordów w zapytaniu, **avg(CounterValue)** zwraca średnią pole równowartości dla interwału.
- **Pole grupy**.  Zostaje utworzony rekord z zagregowane wartości dla poszczególnych wystąpień tego pola, a alert jest generowany dla każdego. Na przykład, jeśli chcesz generować alert dla każdego komputera, możesz użyć **przez komputer**.   
- **Interwał**.  Określa przedział czasu, w którym są agregowane.  Na przykład, jeśli określono **5 minut**, może zostać utworzony rekord dla każdego wystąpienia pola grupy przez przedział czasu określony dla alertu agregowana co 5 minut.

#### <a name="threshold"></a>Próg
Próg metryki pomiaru reguły alertów jest definiowany przez wartości zagregowanej i liczbę naruszeń. Dowolnego punktu danych w dzienniku wyszukiwania przekracza tę wartość, jest uznawane za naruszenia. Jeśli liczba naruszeń w dla dowolnego obiektu w wynikach przekracza określoną wartość, alert zostanie utworzony dla tego obiektu.

#### <a name="example"></a>Przykład
Rozważmy scenariusz, w którym chce alert dowolnego komputera przekroczeniu użycie procesora przez 90% trzy razy ponad 30 minut. Należy utworzyć regułę alertu z poniższymi szczegółami.

**Zapytanie:** typu = wydajności ObjectName = CounterName procesora = "% czasu procesora" | miar avg(CounterValue) interwał komputera 5 minut<br>
**Przedział czasu:** 30 minut<br>
**Alert częstotliwości:** 5 minut<br>
**Wartość agregacji:** większa niż 90<br>
**Na podstawie wyzwalania alertu:** naruszeń łączna liczba jest większa niż 5<br>

Zapytanie spowodowałoby średnią wartość dla każdego komputera co 5 minut. To zapytanie może działać co 5 minut przez dane zbierane przez poprzednie 30 minut. Poniżej przedstawiono przykładowe dane na trzech komputerach.

![Przykładowe wyniki zapytania](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

W tym przykładzie oddzielne alerty zostałyby utworzone dla srv02 i srv03, ponieważ ich naruszenia progu 90% 3 razy za pośrednictwem przedział czasu. Jeśli **na podstawie wyzwalania alertu:** zostały zmienione na **kolejno** , a następnie alert będzie można utworzyć tylko dla srv03, ponieważ jego naruszenia progu dla kolejnych próbek 3.

## <a name="alert-records"></a>Rejestruje alertu
Rekordy alertu przez reguły alertów w analizy dzienników mają **typu** z **Alert** i **SourceSystem** z **OMS**. Mają one właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Alert* |
| SourceSystem |*OMS* |
| *Obiekt*  | [Alerty metryki pomiaru](#metric-measurement-alert-rules) ma właściwość dla pola grupy. Na przykład jeśli wyszukiwanie dziennika grupy na komputerze, alertów rekord z ma pole komputera o nazwie komputera jako wartość.
| AlertName |Nazwa alertu. |
| AlertSeverity |Poziom ważności alertu. |
| LinkToSearchResults |Link do wyszukiwania dziennika analizy dzienników, która zwraca odpowiednie rekordy z kwerendy utworzony alert. |
| Zapytanie |Tekst zapytania, które zostało uruchomione. |
| QueryExecutionEndTime |Koniec przedziału czasu dla zapytania. |
| QueryExecutionStartTime |Początek przedziału czasu dla zapytania. |
| ThresholdOperator | Operator, który został użyty przez regułę alertu. |
| ThresholdValue | Wartość, która była używana przez regułę alertu. |
| TimeGenerated |Data i godzina utworzenia alertu. |

Istnieją inne rodzaje rekordy alertu przez [rozwiązania do zarządzania Alert](log-analytics-solution-alert-management.md) oraz [eksportuje usługi Power BI](log-analytics-powerbi.md).  Te wszystkie mają **typu** z **alertu** , ale można rozróżnić za ich **SourceSystem**.


## <a name="next-steps"></a>Kolejne kroki
* Zainstaluj [rozwiązania zarządzania alertami](log-analytics-solution-alert-management.md) do analizowania alertach tworzonych w analizy dzienników oraz alertów zebranych z programu System Center Operations Manager.
* Przeczytaj więcej na temat [dziennika wyszukiwania](log-analytics-log-searches.md) który generowania alertów.
* Zakończenie wskazówki dla [Konfigurowanie webook](log-analytics-alerts-webhooks.md) z reguły alertu.
* Dowiedz się, jak napisać [elementy runbook automatyzacji Azure](https://azure.microsoft.com/documentation/services/automation) skorygować problemy zidentyfikowane przez alertów.
