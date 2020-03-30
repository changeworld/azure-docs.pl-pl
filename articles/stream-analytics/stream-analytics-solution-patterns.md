---
title: Wzorce rozwiązań usługi Azure Stream Analytics
description: Dowiedz się więcej o typowych wzorcach rozwiązań dla usługi Azure Stream Analytics, takich jak pulpit nawigacyjny, wiadomości o zdarzeniach, magazyny danych, wzbogacanie danych referencyjnych i monitorowanie.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535786"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Wzorce rozwiązań usługi Azure Stream Analytics

Podobnie jak wiele innych usług na platformie Azure, usługa Stream Analytics jest najlepiej używana z innymi usługami w celu utworzenia większego kompleksowego rozwiązania. W tym artykule omówiono proste rozwiązania usługi Azure Stream Analytics i różne wzorce architektury. Można wykorzystać te wzorce, aby opracować bardziej złożone rozwiązania. Wzorce opisane w tym artykule mogą być używane w wielu różnych scenariuszach. Przykłady wzorców specyficznych dla scenariusza są dostępne w [architekturach rozwiązań platformy Azure.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Tworzenie zadania usługi Stream Analytics w celu zasilania środowiska pulpitu nawigacyjnego w czasie rzeczywistym

Dzięki usłudze Azure Stream Analytics możesz szybko wytrzymywają pulpity nawigacyjne i alerty w czasie rzeczywistym. Proste rozwiązanie pożywia zdarzenia z Centrów zdarzeń lub Centrum IoT i [przesyła pulpit nawigacyjny usługi Power BI za pomocą zestawu danych przesyłania strumieniowego.](/power-bi/service-real-time-streaming) Aby uzyskać więcej informacji, zobacz szczegółowy samouczek [Analizowanie danych połączeń telefonicznych za pomocą usługi Stream Analytics i wizualizacja wyników na pulpicie nawigacyjnym usługi Power BI](stream-analytics-manage-job.md).

![Pulpit nawigacyjny usługi ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

To rozwiązanie można sbudować w ciągu zaledwie kilku minut z witryny Azure portal. Nie ma obszerne kodowanie zaangażowanych i język SQL jest używany do wyrażania logiki biznesowej.

Ten wzorzec rozwiązania oferuje najniższe opóźnienie od źródła zdarzeń do pulpitu nawigacyjnego usługi Power BI w przeglądarce. Usługa Azure Stream Analytics jest jedyną usługą platformy Azure z tą wbudowaną funkcją.

## <a name="use-sql-for-dashboard"></a>Używanie języka SQL do korzystania z pulpitu nawigacyjnego

Pulpit nawigacyjny usługi Power BI oferuje małe opóźnienia, ale nie może być używany do tworzenia pełnoprawnych raportów usługi Power BI. Typowy wzorzec raportowania jest do produkcji danych do bazy danych SQL pierwszy. Następnie użyj łącznika SQL usługi Power BI, aby zapytać sql dla najnowszych danych.

![Pulpit nawigacyjny ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

Korzystanie z bazy danych SQL zapewnia większą elastyczność, ale kosztem nieco większe opóźnienie. To rozwiązanie jest optymalne dla zadań z wymaganiami opóźnienia większymi niż jedna sekunda. Dzięki tej metodzie można zmaksymalizować możliwości usługi Power BI, aby dalej wycinać i pokroić w kostkę dane dla raportów i znacznie więcej opcji wizualizacji. Można również uzyskać elastyczność korzystania z innych rozwiązań pulpitu nawigacyjnego, takich jak Tableau.

SQL nie jest magazynem danych o wysokiej przepływności. Maksymalna przepływność do bazy danych SQL z usługi Azure Stream Analytics wynosi obecnie około 24 MB/s. Jeśli źródła zdarzeń w rozwiązaniu produkują dane z wyższą szybkością, należy użyć logiki przetwarzania w usłudze Stream Analytics, aby zmniejszyć szybkość danych wyjściowych do języka SQL. Można użyć technik, takich jak filtrowanie, agregaty w oknie, dopasowywanie wzorców do sprzężenia czasowego i funkcje analityczne. Szybkość produkcji sql można dodatkowo zoptymalizować przy użyciu technik opisanych w [danych wyjściowych usługi Azure Stream Analytics do bazy danych SQL Azure.](stream-analytics-sql-output-perf.md)

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Uwzględnianie wglądu w czasie rzeczywistym w aplikację za pomocą obsługi wiadomości o zdarzeniach

Drugim najpopularniejszym zastosowaniem usługi Stream Analytics jest generowanie alertów w czasie rzeczywistym. W tym wzorcu rozwiązania logika biznesowa w usłudze Stream Analytics może służyć do [wykrywania wzorców czasowych i przestrzennych](stream-analytics-geospatial-functions.md) lub [anomalii,](stream-analytics-machine-learning-anomaly-detection.md)a następnie tworzenia sygnałów alarmowych. Jednak w przeciwieństwie do rozwiązania pulpitu nawigacyjnego, w którym usługa Stream Analytics używa usługi Power BI jako preferowanego punktu końcowego, można użyć wielu pośrednich pochłaniaczy danych. Te pochłaniacze obejmują centra zdarzeń, usługę Service Bus i usługi Azure Functions. Ty, jako konstruktor aplikacji, należy zdecydować, który zlew danych działa najlepiej dla twojego scenariusza.

Logika konsumenta zdarzeń podrzędnych musi zostać zaimplementowana w celu generowania alertów w istniejącym przepływie pracy biznesowej. Ponieważ można zaimplementować logikę niestandardową w usłudze Azure Functions, usługa Azure Functions jest najszybszym sposobem wykonywania tej integracji. Samouczek dotyczący korzystania z funkcji platformy Azure jako danych wyjściowych dla zadania usługi Stream Analytics można znaleźć w [zadaniach Uruchamianie usług Azure Functions z usługi Azure Stream Analytics.](stream-analytics-with-azure-functions.md) Usługa Azure Functions obsługuje również różne typy powiadomień, w tym tekst i adres e-mail. Aplikacja logiki może być również używana do takiej integracji z centrum zdarzeń między usługią Stream Analytics i aplikacją logiki.

![Aplikacja do obsługi wiadomości zdarzeń ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Centra zdarzeń, z drugiej strony, oferuje najbardziej elastyczny punkt integracji. Wiele innych usług, takich jak Usługa Azure Data Explorer i Usługa Time Series Insights, może korzystać ze zdarzeń z centrów zdarzeń. Usługi mogą być połączone bezpośrednio z usługi Event Hubs z usługi Azure Stream Analytics, aby ukończyć rozwiązanie. Usługa Event Hubs jest również brokerem obsługi obsługi obsługi przepływności najwyższej dostępnej na platformie Azure dla takich scenariuszy integracji.

## <a name="dynamic-applications-and-websites"></a>Dynamiczne aplikacje i strony internetowe

Można tworzyć niestandardowe wizualizacje w czasie rzeczywistym, takie jak pulpit nawigacyjny lub wizualizacja mapy, przy użyciu usługi Azure Stream Analytics i usługi Azure SignalR. Za pomocą SignalR klienci internetowi mogą być aktualizowani i wyświetlać zawartość dynamiczną w czasie rzeczywistym.

![Dynamiczna aplikacja ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Uwzględnianie wglądu w czasie rzeczywistym w aplikację za pośrednictwem magazynów danych

Większość usług sieci web i aplikacji sieci web obecnie użyć wzorzec żądania/odpowiedzi do obsługi warstwy prezentacji. Wzorzec żądania/odpowiedzi jest prosty w kompilacji i można łatwo skalować z niskim czasem odpowiedzi przy użyciu bezstanowego frontendu i skalowalnych magazynów, takich jak Cosmos DB.

Duża ilość danych często tworzy wąskie gardła wydajności w systemie opartym na CRUD. [Wzorzec rozwiązania pozyskiwania zdarzeń](/azure/architecture/patterns/event-sourcing) jest używany do rozwiązywania wąskich gardeł wydajności. Wzorce czasowe i szczegółowe informacje są również trudne i nieefektywne wyodrębnić z tradycyjnego magazynu danych. Nowoczesne aplikacje oparte na danych o dużej objętości często przyjmują architekturę opartą na przepływie danych. Usługa Azure Stream Analytics jako aparat obliczeniowy danych w ruchu jest linchpin w tej architekturze.

![Aplikacja do pozyskiwania zdarzeń ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

W tym wzorcu rozwiązania zdarzenia są przetwarzane i agregowane w magazynach danych przez usługę Azure Stream Analytics. Warstwa aplikacji współdziała z magazynami danych przy użyciu tradycyjnego wzorca żądania/odpowiedzi. Ze względu na możliwość przetwarzania dużej liczby zdarzeń w czasie rzeczywistym, aplikacja jest wysoce skalowalna bez konieczności zbiorczego warstwy magazynu danych. Warstwa magazynu danych jest zasadniczo zmaterializowany widok w systemie. [Usługa Azure Stream Analytics danych wyjściowych do usługi Azure Cosmos DB](stream-analytics-documentdb-output.md) opisuje, jak usługa Cosmos DB jest używany jako dane wyjściowe usługi Stream Analytics.

W rzeczywistych aplikacjach, w których logika przetwarzania jest złożona i istnieje potrzeba niezależnego uaktualnienia niektórych części logiki, wiele zadań usługi Stream Analytics może składać się razem z centrum zdarzeń jako broker zdarzeń pośredniczących.

![Aplikacja do pozyskiwania złożonych zdarzeń ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Ten wzorzec poprawia odporność i łatwość zarządzania systemu. Jednak mimo że usługa Stream Analytics gwarantuje dokładnie raz przetwarzania, istnieje niewielka szansa, że zduplikowane zdarzenia mogą wylądować w pośredniczących centrach zdarzeń. Ważne jest, aby zadanie usługi Dedupe usługi usługi edupe przy użyciu kluczy logiki w oknie lookback. Aby uzyskać więcej informacji na temat dostarczania zdarzeń, zobacz odwołanie [do gwarancji dostarczania zdarzeń.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Używanie danych referencyjnych do dostosowywania aplikacji

Funkcja danych referencyjnych usługi Azure Stream Analytics została zaprojektowana specjalnie do dostosowywania użytkowników końcowych, takich jak próg alertów, reguły przetwarzania i [geofences.](geospatial-scenarios.md) Warstwa aplikacji może akceptować zmiany parametrów i przechowywać je w bazie danych SQL. Zadanie usługi Stream Analytics okresowo wysyła zapytania o zmiany z bazy danych i udostępnia parametry dostosowywania za pośrednictwem sprzężenia danych referencyjnych. Aby uzyskać więcej informacji na temat używania danych referencyjnych do dostosowywania aplikacji, zobacz [Dane referencyjne SQL](sql-reference-data.md) i [dołączanie do danych referencyjnych](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ten wzorzec może również służyć do zaimplementowania aparatu reguł, gdzie progi reguł są zdefiniowane na podstawie danych referencyjnych. Aby uzyskać więcej informacji na temat reguł, zobacz [Przetwarzanie konfigurowalnych reguł opartych na progach w usłudze Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplikacja danych referencyjnych ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Dodawanie uczenia maszynowego do szczegółowych informacji w czasie rzeczywistym

Wbudowany [model wykrywania anomalii](stream-analytics-machine-learning-anomaly-detection.md) usługi Azure Stream Analytics to wygodny sposób wprowadzania uczenia maszynowego do aplikacji w czasie rzeczywistym. Aby uzyskać szerszy zakres potrzeb dotyczących uczenia maszynowego, zobacz [Usługa Azure Stream Analytics integruje się z usługą oceniania usługi Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

Dla zaawansowanych użytkowników, którzy chcą włączyć szkolenia online i punktacji w tym samym potoku usługi Stream Analytics, zobacz ten przykład, jak to zrobić z [regresji liniowej](stream-analytics-high-frequency-trading.md).

![Aplikacja ASA Machine Learning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Niemal magazynowanie danych w czasie rzeczywistym

Innym typowym wzorcem jest magazynowanie danych w czasie rzeczywistym, nazywane również magazynem danych przesyłania strumieniowego. Oprócz zdarzeń docierających do centrów zdarzeń i usługi IoT Hub z aplikacji usługa [Azure Stream Analytics działająca w usłudze IoT Edge](stream-analytics-edge.md) może służyć do wypełniania potrzeb dotyczących oczyszczania danych, ograniczania danych oraz przechowywania danych i przekazywania danych. Usługa Stream Analytics działająca w usłudze IoT Edge może bezpiecznie obsługiwać problemy z ograniczeniem przepustowości i łącznością w systemie. Karta wyjściowa SQL może służyć do wyprowadzania do magazynu danych SQL; jednak maksymalna przepustowość jest ograniczona do 10 MB/s.

![Magazynowanie danych ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Jednym ze sposobów zwiększenia przepływności za pomocą pewnego kompromisu opóźnienia jest archiwizowanie zdarzeń w magazynie obiektów Blob platformy Azure, a następnie [importowanie ich do magazynu danych SQL z polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Musisz ręcznie zszyć dane wyjściowe z usługi Stream Analytics do magazynu obiektów blob i danych wejściowych z magazynu obiektów blob do magazynu danych SQL przez [archiwizowanie danych przez sygnaturę czasową](stream-analytics-custom-path-patterns-blob-storage-output.md) i okresowe importowanie.

W tym wzorcu użycia usługa Azure Stream Analytics jest używana jako aparat ETL w czasie zbliżonym do rzeczywistego. Nowo przychodzące zdarzenia są stale przekształcane i przechowywane w celu uzyskania niższego rzędu zużycia usług analitycznych.

![Asa wysokiej przepustowości data warehousing](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archiwizacja danych w czasie rzeczywistym do analizy

Większość działań związanych z analityką i analizą danych nadal odbywa się w trybie offline. Dane mogą być archiwizowane przez usługę Azure Stream Analytics za pośrednictwem formatów wyjściowych usługi Azure Data Lake Store Gen2 i parquet. Ta funkcja usuwa tarcie do podawania danych bezpośrednio do usługi Azure Data Lake Analytics, Azure Databricks i Azure HDInsight. Usługa Azure Stream Analytics jest używana jako aparat ETL w czasie zbliżonym do rzeczywistego w tym rozwiązaniu. Można eksplorować zarchiwizowanych danych w umiaru danych przy użyciu różnych aparatów obliczeniowych.

![Analiza asa w trybie offline](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Użyj danych referencyjnych do wzbogacenia

Wzbogacanie danych jest często wymagane dla silników ETL. Usługa Azure Stream Analytics obsługuje wzbogacanie danych za pomocą [danych referencyjnych](stream-analytics-use-reference-data.md) z bazy danych SQL i magazynu obiektów Blob platformy Azure. Wzbogacanie danych można wykonać dla danych landing w usłudze Azure Data Lake i SQL Data Warehouse.

![Analiza offline ASA z wzbogacaniem danych](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacjonalizacja szczegółowych informacji z archiwalnych danych

Jeśli połączysz wzorzec analizy w trybie offline ze wzorcem aplikacji w czasie zbliżonym do rzeczywistego, możesz utworzyć pętlę sprzężenia zwrotnego. Pętla sprzężenia zwrotnego umożliwia aplikacji automatyczne dostosowanie do zmiany wzorców w danych. Ta pętla sprzężenia zwrotnego może być tak proste, jak zmiana wartości progowej dla alertów lub tak złożone, jak przekwalifikowanie modeli uczenia maszynowego. Tę samą architekturę rozwiązania można zastosować zarówno do zadań ASA uruchomionych w chmurze, jak i w usłudze IoT Edge.

![Operacjonalizacja analiz ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak monitorować oferty pracy ASA

Zadanie usługi Azure Stream Analytics można uruchomić 24 godziny na dobę, 7 dni w czasie rzeczywistym, aby przetwarzać zdarzenia przychodzące w sposób ciągły. Jego gwarancja czasu pracy jest kluczowa dla zdrowia całego zastosowania. Stream Analytics jest jedyną usługą analizy strumieniowej w branży, która oferuje [gwarancję dostępności na poziomie 99,9%,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)nadal możesz ponieść pewien czas przestoju. Z biegiem lat usługa Stream Analytics wprowadziła metryki, dzienniki i stany zadań, aby odzwierciedlić kondycję zadań. Wszystkie z nich są widoczne za pośrednictwem usługi Azure Monitor i mogą być dalej eksportowane do usługi OMS. Aby uzyskać więcej informacji, zobacz [Opis monitorowania zadań usługi Stream Analytics i sposobu monitorowania zapytań](stream-analytics-monitoring.md).

![Monitorowanie ASA](media/stream-analytics-solution-patterns/monitoring.png)

Istnieją dwie kluczowe rzeczy do monitorowania:

- [Stan zadania nie powiodło się](job-states.md)

    Przede wszystkim musisz upewnić się, że zadanie jest uruchomione. Bez zadania w stanie uruchomionym nie są generowane żadne nowe metryki lub dzienniki. Zadania mogą ulec zmianie na stan awarii z różnych powodów, w tym o wysoki poziom wykorzystania SU (tj. wyczerpanie zasobów).

- [Metryki opóźnienia znaku wodnego](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ta metryka odzwierciedla, jak daleko za potok przetwarzania jest w czasie zegara ściennego (sekundy). Niektóre opóźnienia jest przypisany do logiki przetwarzania nieodłączne. W rezultacie monitorowanie tendencji wzrostowej jest znacznie ważniejsze niż monitorowanie wartości bezwzględnej. Opóźnienie stanu ustalonego powinno być rozwiązywane przez projekt aplikacji, a nie przez monitorowanie lub alerty.

Po niepowodzeniu dzienniki aktywności i [dzienniki diagnostyczne](stream-analytics-job-diagnostic-logs.md) są najlepszymi miejscami do rozpoczęcia poszukiwania błędów.

## <a name="build-resilient-and-mission-critical-applications"></a>Tworzenie odpornych i krytycznych aplikacji

Niezależnie od gwarancji umowy SLA usługi Azure Stream Analytics i jak ostrożnie uruchomić aplikację end-to-end, awarie się zdarzają. Jeśli aplikacja jest krytyczna, należy być przygotowanym na awarie w celu odzyskania bezpiecznie.

W przypadku alertów aplikacji najważniejszą rzeczą jest wykrycie następnego alertu. Możesz ponownie uruchomić zadanie od bieżącej chwili podczas odzyskiwania, ignorując wcześniejsze alerty. Semantyka czasu rozpoczęcia zadania jest uruchamiana po raz pierwszy, a nie po raz pierwszy. Wejście jest przewijanie do tyłu odpowiednią ilość czasu, aby zagwarantować pierwsze wyjście w określonym czasie jest kompletna i poprawna. W rezultacie nie otrzymasz agregacji częściowych i alertów wyzwalania nieoczekiwanie w wyniku.

Można również rozpocząć dane wyjściowe z pewnej ilości czasu w przeszłości. Centra zdarzeń i zasady przechowywania usługi IoT Hub przechowują rozsądną ilość danych, aby umożliwić przetwarzanie z przeszłości. Kompromisem jest to, jak szybko można nadrobić zaległości do bieżącego czasu i zacząć generować nowe alerty w odpowiednim czasie. Dane szybko tracą swoją wartość w czasie, dlatego ważne jest, aby szybko nadrobić zaległości w bieżącym czasie. Istnieją dwa sposoby szybkiego nadrobienia zaległości:

- Aprowizować więcej zasobów (SU) podczas nadrabiania zaległości.
- Uruchom ponownie od bieżącego czasu.

Ponowne uruchomienie z bieżącego czasu jest proste do zrobienia, z kompromisem pozostawiając lukę podczas przetwarzania. Ponowne uruchomienie w ten sposób może być OK dla scenariuszy alertów, ale może być problematyczne dla scenariuszy pulpitu nawigacyjnego i jest non-starter dla scenariuszy archiwizacji i magazynowania danych.

Inicjowanie obsługi administracyjnej więcej zasobów może przyspieszyć proces, ale efekt wzrostu szybkości przetwarzania jest złożony.

- Sprawdź, czy zadanie jest skalowalne dla większej liczby sus. Nie wszystkie zapytania są skalowalne. Należy upewnić się, że zapytanie jest [równoległe](stream-analytics-parallelization.md).

- Upewnij się, że istnieje wystarczająca liczba partycji w nadrzędnych centrach zdarzeń lub centrum IoT Hub, które można dodać więcej jednostek przepływności (TUs), aby skalować przepływność wejściową. Pamiętaj, że każdy Event Hubs TU maksymatuje z szybkością wyjściową 2 MB/s.

- Upewnij się, że aprowizacji wystarczającej ilości zasobów w ujściach danych wyjściowych (tj. bazy danych SQL, usługi Cosmos DB), więc nie ograniczyć wzrost danych wyjściowych, co czasami może spowodować, że system do zablokowania.

Najważniejszą rzeczą jest przewidywanie zmiany szybkości przetwarzania, przetestowanie tych scenariuszy przed przejściem do produkcji i gotowość do poprawnego skalowania przetwarzania w czasie odzyskiwania awarii.

W skrajnym scenariuszu, że zdarzenia przychodzące są opóźnione, możliwe jest, że [wszystkie opóźnione zdarzenia zostaną usunięte,](stream-analytics-time-handling.md) jeśli zastosowano okno późnego przyjścia do zadania. Upuszczenie wydarzeń może wydawać się tajemniczym zachowaniem na początku; Jednak biorąc pod uwagę Stream Analytics jest aparat przetwarzania w czasie rzeczywistym, oczekuje, że przychodzące zdarzenia będą zbliżone do czasu zegara ściennego. Musi upuścić zdarzenia, które naruszają te ograniczenia.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Architektury lub proces wypełniania

Na szczęście poprzedni wzorzec archiwizacji danych może służyć do przetwarzania tych późnych zdarzeń bezpiecznie. Chodzi o to, że zadanie archiwizacji przetwarza przychodzące zdarzenia w czasie przybycia i archiwizuje zdarzenia w zasobniku czasu w usłudze Azure Blob lub Azure Data Lake Store z czasem zdarzenia. Nie ma znaczenia, jak późno nadejdzie wydarzenie, nigdy nie zostanie upuszczone. Zawsze wyląduje w odpowiednim czasie wiadro. Podczas odzyskiwania można ponownie przetworzyć zarchiwizowane zdarzenia i wypełnić wyniki do wybranego magazynu. Jest to podobne do sposobu implementowania wzorców lambda.

![Zasypka ASA](media/stream-analytics-solution-patterns/backfill.png)

Proces wypełniania musi odbywać się za pomocą systemu przetwarzania wsadowego w trybie offline, który najprawdopodobniej ma inny model programowania niż Usługa Azure Stream Analytics. Oznacza to, że trzeba ponownie zaimplementować całą logikę przetwarzania.

W przypadku wypełniania zapasowego nadal jest ważne, aby przynajmniej tymczasowo aprowizować więcej zasobów do pochłaniacze danych wyjściowych do obsługi wyższej przepływności niż potrzeby przetwarzania w stanie ustalonym.

|Scenariusze  |Uruchom ponownie tylko od teraz  |Uruchom ponownie od czasu ostatniego zatrzymania |Uruchom ponownie od teraz + zasypka z zarchiwizowanych zdarzeń|
|---------|---------|---------|---------|
|**Pulpit nawigacyjny**   |Tworzy przerwę    |OK na krótką awarię    |Używanie do długiej przerwy w dostawie prądu |
|**Alerty**   |Zadowalające |OK na krótką awarię    |Nie jest konieczne |
|**Aplikacja do pozyskiwania zdarzeń** |Zadowalające |OK na krótką awarię    |Używanie do długiej przerwy w dostawie prądu |
|**Magazynowanie danych**   |Utrata danych  |Zadowalające |Nie jest konieczne |
|**Analiza w trybie offline**  |Utrata danych  |Zadowalające |Nie jest konieczne|

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Nie trudno sobie wyobrazić, że wszystkie wzorce rozwiązań wymienione powyżej mogą być połączone ze sobą w złożonym systemie end-to-end. Połączony system może obejmować pulpity nawigacyjne, alerty, aplikacje do pozyskiwania zdarzeń, magazynowanie danych i funkcje analizy w trybie offline.

Kluczem jest zaprojektowanie systemu w szykalnych wzorcach, dzięki czemu każdy podsystem może być zbudowany, przetestowany, uaktualniony i odzyskany niezależnie.

## <a name="next-steps"></a>Następne kroki

Teraz masz różne wzorce rozwiązań przy użyciu usługi Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Utwórz zadanie usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).
