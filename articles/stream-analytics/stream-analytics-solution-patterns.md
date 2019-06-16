---
title: Wzorców rozwiązań w usłudze Azure Stream Analytics
description: Informacje na temat wzorców rozwiązań usługi Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761992"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Wzorców rozwiązań w usłudze Azure Stream Analytics

Podobnie jak wielu innych usług na platformie Azure Stream Analytics najlepiej służy z innymi usługami do tworzenia większych rozwiązania end-to-end. W tym artykule omówiono prostych rozwiązań usługi Azure Stream Analytics i różnych wzorców architektury. Można tworzyć na tych wzorców w celu tworzenia bardziej złożonych rozwiązań. Wzorce opisane w tym artykule może służyć w wielu różnych scenariuszach. Przykłady wzorców specyficzne dla scenariusza są dostępne na [architektury rozwiązań platformy Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Tworzenie zadania usługi Stream Analytics z pulpitem nawigacyjnym w czasie rzeczywistym

Za pomocą usługi Azure Stream Analytics łatwość użycia możesz szybko uruchomić alerty i pulpity nawigacyjne w czasie rzeczywistym. Proste rozwiązanie pozyskuje zdarzenia z usługi Event Hubs lub usługi IoT Hub i [źródeł danych pulpitu nawigacyjnego usługi Power BI przy użyciu zestawu danych przesyłania strumieniowego](/power-bi/service-real-time-streaming). Aby uzyskać więcej informacji, zobacz szczegółowy samouczek [Analizuj dane połączeń telefonicznych za pomocą usługi Stream Analytics i Wizualizuj wyniki na pulpicie nawigacyjnym usługi Power BI](stream-analytics-manage-job.md).

![Pulpit nawigacyjny usługi Power BI ASA](media/stream-analytics-solution-patterns/pbidashboard.png)

To rozwiązanie może być skompilowany w zaledwie kilka minut w witrynie Azure portal. Istnieje bez kodowania rozbudowane zaangażowane i języka SQL umożliwia wyrażanie logiki biznesowej.

Ten pulpit nawigacyjny w czasie rzeczywistym rozwiązanie wzorzec oferuje najniższe opóźnienie ze źródła zdarzeń do pulpitu nawigacyjnego usługi Power BI w przeglądarce. Usługa Azure Stream Analytics jest tylko usługa dzięki tej możliwości wbudowanych.

## <a name="use-sql-for-dashboard"></a>Korzystanie z SQL do pulpitu nawigacyjnego

Pulpit nawigacyjny usługi Power BI zapewnia małe opóźnienia, ale nie można użyć do utworzenia pełnej użytecznym raportów usługi Power BI. Typowy wzorzec raportowania jest najpierw dane wyjściowe dane do usługi SQL database. Następnie należy używać łącznika SQL usługi Power BI do wykonywania zapytań SQL najnowsze dane.

![Pulpit nawigacyjny ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

Przy użyciu programu SQL database zapewnia większą elastyczność kosztem większe opóźnienia. To rozwiązanie jest optymalna dla zadań wymagania dotyczące opóźnień większa niż 1 sekundy. Przy użyciu tej metody można zmaksymalizować narzędzie usługi Power BI w celu dalszego wycinka i wykorzystuj dane do raportów. Możesz także elastyczność przy użyciu innych rozwiązań pulpitu nawigacyjnego, takich jak Tableau.

SQL nie jest do przechowywania danych o wysokiej przepływności, a maksymalna przepływność do usługi SQL database z usługi Azure Stream Analytics to 24 MB/s. Źródła zdarzeń w rozwiązaniu do wygenerowania danych wyższa stawka, należy użyć logiki przetwarzania w usłudze Stream Analytics można zmniejszyć częstotliwość danych wyjściowych do bazy danych SQL. Dopasowanie za pomocą sprzężeń danych czasowych wzorca za pomocą technik, takich jak filtrowanie, agregacji w trybie okna, a funkcje analityczne mogą być używane. Współczynnik danych wyjściowych do bazy danych SQL można dodatkowo zoptymalizować za pomocą metod opisanych w [dane wyjściowe usługi Azure Stream Analytics, do usługi Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Zawierały informacje w czasie rzeczywistym do aplikacji z obsługą komunikatów zdarzeń

Drugi najbardziej popularnych korzystanie z usługi Stream Analytics jest do generowania alertów w czasie rzeczywistym. W tym wzorcu rozwiązania logikę biznesową w usłudze Stream Analytics może służyć do wykrywania [wzorców danych czasowych i przestrzennych](stream-analytics-geospatial-functions.md) lub [anomalie](stream-analytics-machine-learning-anomaly-detection.md), a następnie wygenerować alerty sygnałów. Jednak w przeciwieństwie do rozwiązań pulpitu nawigacyjnego, gdzie usługi Stream Analytics korzysta z usługi Power BI jako preferowany punkt końcowy, liczba ujść danych pośrednich można. Te obiekty sink obejmują usługi Event Hubs, Usługa Service Bus i Azure Functions. Jako Konstruktor aplikacji, musisz zdecydować, które ujście danych jest najlepsza dla danego scenariusza.

Logika odbiorcy zdarzenia podrzędne muszą być zaimplementowane do generowania alertów w istniejący przepływ pracy firmy. Ponieważ można zaimplementować logikę niestandardową w usłudze Azure Functions, Functions to najszybszy sposób, można wykonać tej integracji. Samouczek dotyczący korzystania z funkcji platformy Azure, jak dane wyjściowe zadania usługi Stream Analytics można znaleźć w [uruchamiania usługi Azure Functions z zadań usługi Azure Stream Analytics](stream-analytics-with-azure-functions.md). Usługa Azure Functions obsługuje również różne typy powiadomień, łącznie z tekstem i wiadomości e-mail. Aplikacja logiki może również służyć do takiego integracji z usługą Event Hubs między usługi Stream Analytics i aplikacji logiki.

![Zdarzenie ASA aplikację do obsługi wiadomości](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Usługa Event Hubs, z drugiej strony, oferuje najbardziej elastyczny punkt integracji. Wiele innych usług, takich jak Eksplorator danych platformy Azure i Time Series Insights — mogą wykorzystywać zdarzeń z centrów zdarzeń. Usługi mogą zostać połączone bezpośrednio do ujścia usługi Event Hubs z usługi Azure Stream Analytics, aby ukończyć rozwiązania. Usługa Event Hubs jest również najwyższy przepływność komunikatów brokera dostępne na platformie Azure w przypadku takich scenariuszy integracji.

## <a name="dynamic-applications-and-websites"></a>Dynamicznych aplikacji i witryn sieci Web

Można utworzyć niestandardowych wizualizacji w czasie rzeczywistym, takich jak pulpitu nawigacyjnego lub wizualizacji, mapy, przy użyciu usługi Azure Stream Analytics i Azure SignalR Service. Za pomocą oprogramowania SignalR, klientów i sieci web mogą być aktualizowane Pokaż zawartości dynamicznej w czasie rzeczywistym.

![Aplikacja dynamiczna ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Zawierały informacje w czasie rzeczywistym do aplikacji przy użyciu magazynów danych

Większość usług sieci web i aplikacji sieci web obecnie za pomocą wzorca żądania/odpowiedzi do obsługi warstwy prezentacji. Wzorzec żądań/odpowiedzi jest proste tworzenie i może być łatwo skalowany z czasem odpowiedzi niski przy użyciu bezstanowej frontonu i skalowalnych sklepów, takich jak usługi Cosmos DB.

Dużego woluminu danych często tworzy wąskich gardeł wydajności w systemie opartych na podejściu CRUD. [Rozwiązaniu wzorzec określania źródła zdarzeń](/azure/architecture/patterns/event-sourcing) jest wykorzystywana do adresowania wąskich gardeł wydajności. Wzorce danych czasowych i szczegółowe informacje są również trudne i nieefektywna można wyodrębnić z magazynu danych tradycyjnych. Nowoczesne dużej liczby aplikacji opartych na danych często przyjęcie architektury opartej na przepływ danych. Usługi Azure Stream Analytics jako aparat obliczeń na danych w ruchu jest filarem w danej architekturze.

![Aplikacja określania źródła zdarzeń ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

W tym wzorcu rozwiązania zdarzenia są przetwarzane i agregowane w magazynach danych przez usługę Azure Stream Analytics. Warstwa aplikacji wchodzi w interakcję z magazynami danych przy użyciu wzorca tradycyjnych żądania/odpowiedzi. Ze względu na Stream Analytics może przetwarzać dużą liczbę zdarzeń w w czasie rzeczywistym, aplikacja jest wysoce skalowalna, bez konieczności zbiorczo się warstwy magazynu danych. Warstwa magazynu danych jest zasadniczo zmaterializowany widok w systemie. [Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB](stream-analytics-documentdb-output.md) w tym artykule opisano, jak usługi Cosmos DB jest używany jako dane wyjściowe usługi Stream Analytics.

W rzeczywistych aplikacjach gdzie logiki przetwarzania jest złożona i tam jest konieczność uaktualnienia niektórych części logiki niezależnie, wiele zadań usługi Stream Analytics może składać się wraz z usługi Event Hubs jako brokera zdarzeń pośrednie.

![Aplikacja określania źródła zdarzeń złożonych ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Ten wzorzec zwiększa elastyczność i możliwości systemu. Jednak mimo że usługi Stream Analytics gwarantuje dokładnie jednokrotne przetwarzanie, ma mały ryzyko, że zduplikowane zdarzenia może być przejście do pośredniczącej usługi Event Hubs. Jest ważne w przypadku deduplikacji zdarzeń za pomocą kluczy logiki w oknie lookback podrzędne zadania usługi Stream Analytics. Aby uzyskać więcej informacji na temat dostarczania zdarzeń, zobacz [gwarancją dostarczania zdarzeń](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) odwołania.

## <a name="use-reference-data-for-application-customization"></a>Korzystanie z danych referencyjnych do dostosowywania aplikacji

Funkcja danych odwołania usługi Azure Stream Analytics została zaprojektowana specjalnie dostosowywania przez użytkownika końcowego, takich jak alerty progu, przetwarzanie reguł, a [wirtualne ogrodzenia](geospatial-scenarios.md). Warstwa aplikacji można zaakceptować zmiany parametrów i przechowywać je w usłudze SQL database. Zadanie usługi Stream Analytics okresowo wysyła zapytanie dotyczące zmiany z bazy danych i sprawia, że parametry dostosowania dostępne za pośrednictwem sprzężenia dane odwołanie. Aby uzyskać więcej informacji na temat sposobu dostosowywania aplikacji na korzystanie z danych referencyjnych, zobacz [danych referencyjnych SQL](sql-reference-data.md) i [sprzężenia danych odwołania](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ten wzorzec może również zaimplementować aparat reguł gdzie wartości progowe reguł są zdefiniowane z danych referencyjnych. Aby uzyskać więcej informacji na temat zasad, zobacz [przetwarzania można skonfigurować zasady oparte na wartościach progowych w usłudze Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA odwołanie do danych aplikacji](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Dodawanie usługi Machine Learning do szczegółowych danych w czasie rzeczywistym

Wbudowane usługi Azure Stream Analytics [model wykrywania anomalii](stream-analytics-machine-learning-anomaly-detection.md) to wygodny sposób na wprowadzenie uczenia maszynowego do aplikacji w czasie rzeczywistym. Zobacz potrzeby szerszy zakres z uczenia maszynowego [usługi Azure Stream Analytics, który integruje się z usługą oceniania Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Dla zaawansowanych użytkowników, którzy chcą włączyć przeprowadza ocenianie i szkolenie online w tym samym potok usługi Stream Analytics, zobacz ten przykład, jak to zrobić za pomocą [regresji liniowej](stream-analytics-high-frequency-trading.md).

![Aplikacja usługi Machine Learning ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>W pobliżu magazynowania danych w czasie rzeczywistym

Innym typowym wzorcem jest danych w czasie rzeczywistym, magazynowanie, nazywane również magazynu danych przesyłania strumieniowego. Oprócz zdarzeń otrzymywanych z usługi Event Hubs i Centrum IoT Hub z aplikacji [systemem usługi IoT Edge usługi Azure Stream Analytics](stream-analytics-edge.md) może służyć do spełnienia czyszczenia danych, redukcja danych i magazynem danych i do przodu potrzeb. Stream Analytics, uruchomiony w usłudze IoT Edge mogą bezpiecznie obsługiwać ograniczenie przepustowości i problemy z łącznością w systemie. Adapter wyjścia SQL może służyć do danych wyjściowych do usługi SQL Data Warehouse; maksymalna przepływność jest jednak ograniczona do 10 MB/s.

![Magazynowanie danych ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Jednym ze sposobów w celu zwiększenia wydajności z zależnościami pewne opóźnienie jest archiwizowanie zdarzeń w usłudze Azure Blob storage, a następnie [zaimportować je do usługi SQL Data Warehouse przy użyciu technologii Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Użytkownik musi ręcznie łączyć dane wyjściowe z usługi Stream Analytics do magazynu obiektów blob i danych wejściowych z magazynu obiektów blob w usłudze SQL Data Warehouse, [archiwizowania danych według sygnatur czasowych](stream-analytics-custom-path-patterns-blob-storage-output.md) i importowanie okresowo.

W tym wzorcu użycia usługi Azure Stream Analytics jest używana jako niemal w czasie rzeczywistym aparat ETL. Nowo nadchodzących zdarzeń są stale przekształcone i zmagazynowane korzystania z usług analizy podrzędnego.

![Wysoka przepływność ASA, magazynowanie danych](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archiwizowanie danych w czasie rzeczywistym na potrzeby analiz

Większość danych do analizy i analizy działania wykonane w trybie offline. Dane można zarchiwizować przez usługę Azure Stream Analytics za pośrednictwem usługi Azure Data Lake Store Gen2 danych wyjściowych i Parquet formaty danych wyjściowych. Ta funkcja usuwa Ogranicz liczbę problemów, aby przekazywać dane bezpośrednio do usługi Azure Data Lake Analytics, usługi Azure Databricks i Azure HDInsight. Usługa Azure Stream Analytics jest używana jako niemal w czasie rzeczywistym aparat ETL, w tym rozwiązaniu. Możesz eksplorować archiwizowane dane w usłudze Data Lake za pomocą różnych aparatów obliczeń.

![Analiza w trybie offline ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Korzystanie z danych referencyjnych wzbogacania

Wzbogacanie danych często jest to wymagane dla aparatów ETL. Usługa Azure Stream Analytics obsługuje Wzbogacanie danych przy użyciu [dane referencyjne](stream-analytics-use-reference-data.md) zarówno z bazy danych SQL i usługi Azure Blob storage. Wzbogacanie danych jest możliwe dla danych kierowanych do usługi Azure Data Lake i SQL Data Warehouse.

![ASA analizy w trybie offline przy użyciu Wzbogacanie danych](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacjonalizowanie szczegółowych informacji z danych archiwalnych

Jeśli wzorzec analizy w trybie offline są łączone z niemal w czasie rzeczywistym aplikacji wzorca, można utworzyć pętlę informacji zwrotnych. Informacje zwrotne umożliwia aplikacji automatycznie Dostosuj dla zmieniających się wzorców w danych. Pętla sprzężenia może być tak prosta jak zmiana wartości progu alertów lub tak złożonego jak ponowne trenowanie modeli uczenia maszynowego. Taką samą architekturę rozwiązania mogą dotyczyć zarówno zadania usługi ASA uruchomioną w chmurze i w usłudze IoT Edge.

![Operacjonalizacja insights ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak monitorować zadania usługi ASA

Zadania usługi Azure Stream Analytics może działać 24/7 na potrzeby przetwarzania zdarzeń przychodzących stale w czasie rzeczywistym. Gwarancja bezawaryjnego działania jego ma podstawowe znaczenie dla kondycji ogólnej aplikacji. Gdy usługi Stream Analytics to jedyna usługa analizy przesyłania strumieniowego w branży, która oferuje [gwarancji dostępności na poziomie 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), nadal może spowodować naliczenie pewien stopień czas przestoju. W ciągu lat usługi Stream Analytics wprowadziła metryk, dzienników i stany zadań w celu odzwierciedlenia kondycji zadania. Wszystkie z nich są udostępniane za pośrednictwem usługi Azure Monitor, a dodatkowo można wyeksportować do pakietu OMS. Aby uzyskać więcej informacji, zobacz [zrozumieć usługi Stream Analytics zadania monitorowania i monitorowanie zapytań](stream-analytics-monitoring.md).

![Monitorowanie ASA](media/stream-analytics-solution-patterns/monitoring.png)

Istnieją dwie ważne informacje, które monitorowania:

- [Zadanie nie powiodło się stan](job-states.md)

    Najpierw należy się upewnić, że zadanie zostało uruchomione. Bez zadanie zacznie działać bez nowe metryki i dzienniki są generowane. Zadania można zmienić stanu nie powiodło się z różnych powodów, w tym o wysokim poziomie wykorzystania SU (czyli brakować zasobów).

- [Znak wodny opóźnienie metryki](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ta metryka odzwierciedla, jak daleko w tyle przetwarzanie potoku jest czas zegarowy (sekundy). Niektóre opóźnienie jest przypisane do logiki przetwarzania nieprzerwaną pracę. W rezultacie monitorowania trendów zwiększa znacznie bardziej ważne jest niż wartość bezwzględna monitorowania. Opóźnienie w stanie stabilności powinny być rozwiązane przez projekt aplikacji, nie monitorowania i alertów.

W przypadku awarii Dzienniki aktywności i [dzienniki diagnostyczne](stream-analytics-job-diagnostic-logs.md) są najlepsze miejsca, aby rozpocząć wyszukiwanie błędów.

## <a name="build-resilient-and-mission-critical-applications"></a>Kompilowanie odpornych na błędy i dla aplikacji o znaczeniu

Niezależnie od żadnej gwarancji umowy SLA usługi Azure Stream Analytics i jak uważnego możesz uruchomić aplikację end-to-end się zdarzyć, awarie. Jeśli aplikacja o kluczowym znaczeniu, należy przygotować do awarii, aby można było powrócić do poprawnego działania.

Alerty aplikacji, najważniejsze jest wykrywanie następny alert. Możesz ponownie uruchomić zadanie z bieżącego czasu w przypadku odzyskiwania, ignorowanie ostatnie alerty. Semantyka czas rozpoczęcia zadania są przy pierwszym uruchomieniu dane wyjściowe nie wejściowy po raz pierwszy. Wartość wejściowa jest wstecznie przewinięta odpowiednią ilość czasu, aby zagwarantować, że pierwsze dane wyjściowe w określonym czasie jest kompletny i poprawny. Nie otrzymujesz częściowej agregacji i wyzwalać alerty nieoczekiwanie w wyniku.

Można również uruchomić dane wyjściowe z pewien czas w przeszłości. Usługi Event Hubs i Centrum IoT Hub zasady przechowywania przechowują uzasadnione ilość danych, aby zezwolić na przetwarzanie z przeszłości. Kosztem jest, jak szybko możesz zapoznać się z bieżącym czasem i uruchomić do generowania nowych alertów w odpowiednim czasie. Dane traci jego wartość szybko wraz z upływem czasu, dlatego ważne jest, aby zapoznać się z bieżącym czasem szybko. Istnieją dwa sposoby, aby zapoznać się z nimi szybko:

- Podczas przechwytywania w, należy udostępnić więcej zasobów (SU).
- Uruchom ponownie od bieżącego czasu.

Ponownego uruchomienia od bieżącego czasu jest prosty sposób jego wadą jest pozostawienie przerwy podczas przetwarzania. Ponowne uruchomienie w ten sposób może być OK alertów scenariuszy, ale może być problematyczne dla scenariuszy pulpitu nawigacyjnego i jest inne niż początkowy archiwizowania i scenariuszy magazynowania danych.

Inicjowanie obsługi administracyjnej dodatkowych zasobów może przyspieszyć proces, ale o szybkości przetwarzania skoków powoduje złożone.

- Sprawdź, czy zadanie jest skalowalna do większej liczby usług SUs. Nie wszystkie zapytania są skalowalne. Należy upewnić się, że zapytanie jest [zrównoleglona](stream-analytics-parallelization.md).

- Upewnij się, że masz wystarczającą liczbę partycji nadrzędnej usługi Event Hubs lub usługi IoT Hub, które można dodać więcej jednostek przepływności (jednostek przepływności), aby przeskalować przepływność danych wejściowych. Należy pamiętać, że każdy TU centra zdarzeń wydłużyć szybkością 2 MB/s danych wyjściowych.

- Upewnij się, że dostarczonych za mało zasobów w ujść danych wyjściowych (np. bazy danych SQL Database, usługi Cosmos DB), więc nie mogą ograniczać skoków w danych wyjściowych, które czasami mogą spowodować zablokowanie w systemie.

Najważniejsze jest przewidywać zmiana szybkości przetwarzania, przetestować te scenariusze przed przejściem do środowiska produkcyjnego i gotowość do skalowania przetwarzania poprawnie w czasie awarii odzyskiwania.

W tym scenariuszu extreme, zdarzenia przychodzące są wszystkie opóźnione, [jest możliwe wszystkie opóźnione zdarzenia są porzucane](stream-analytics-time-handling.md) oknem nadchodzących opóźnione w przypadku zastosowania do zadania. Usuwanie zdarzeń może być wyświetlana tajemniczymi zachowanie na początku; jednak considering usługi Stream Analytics to aparat przetwarzania w czasie rzeczywistym, oczekuje, że zdarzenia przychodzące się blisko czas zegarowy. Musi on upuszczania, które naruszają te ograniczenia.

### <a name="backfilling-process"></a>Uzupełnianie procesu

Na szczęście poprzedni wzorzec archiwizowania danych może służyć do poprawnego działania przetwarzania tych opóźnione zdarzenia. Chodzi o to zadanie archiwizowania przetwarzanych przez zdarzenia przychodzące w czasie przybycia oraz archiwizuje zdarzenia w zasobniku chwila w Azure blob Storage lub Azure Data Lake Store za pomocą ich czas trwania zdarzenia. Nie ma znaczenia, jak późne nadejściu zdarzenie, nigdy nie zostaną usunięte. Zawsze przeniesie w zasobniku w odpowiednim czasie. W trakcie odzyskiwania jest możliwe ponowne przetwarzanie zdarzeń zarchiwizowanych i wypełniania wyniki wyborów w magazynie.

![Wypełniania ASA](media/stream-analytics-solution-patterns/backfill.png)

Proces wypełniania musi odbywać się przy użyciu usługi batch w trybie offline, przetwarzania systemu, które najprawdopodobniej ma modelu programowania innego niż Azure Stream Analytics. Oznacza to, że masz potrzebę ponownego zaimplementowania logika przetwarzania całego.

Uzupełnianie, nadal ważne jest przynajmniej tymczasowo aprowizację, które więcej zasobów do danych wyjściowych wychwytywanie wyższą przepływność niż w stanie stabilności przetwarzania musi obsłużyć.

|Scenariusze  |Uruchom ponownie teraz tylko od  |Uruchom ponownie z ostatniego czasu zatrzymania |Uruchom ponownie od teraz + wypełniania ze zdarzeniami zarchiwizowane|
|---------|---------|---------|---------|
|**Dashboarding**   |Tworzy lukę    |OK krótki awarii    |Na użytek długi Przestój |
|**Alerty**   |Zadowalające |OK krótki awarii    |Nie jest konieczna |
|**Aplikacja określania źródła zdarzeń** |Zadowalające |OK krótki awarii    |Na użytek długi Przestój |
|**Magazynowania danych**   |Utrata danych  |Zadowalające |Nie jest konieczna |
|**Analiza w trybie offline**  |Utrata danych  |Zadowalające |Nie jest konieczna|

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Nie jest trudne do Wyobraź sobie, że wszystkie wymienione powyżej wzorców rozwiązań można łączyć ze sobą w złożonego systemu end-to-end. Połączony system może zawierać pulpitów nawigacyjnych, zgłaszania alertów, aplikacji określania źródła zdarzeń, magazynowanie danych i możliwości analizy w trybie offline.

Klucz jest zaprojektowanie systemu we wzorcach konfigurowalna, więc może być kompilowana każdego podsystemu, przetestowane, uaktualnienia oraz niezależne odzyskiwanie.

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz już różnych wzorców rozwiązania za pomocą usługi Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).
