---
title: Zbieranie danych dziennika
titleSuffix: Azure Cognitive Search
description: Zbieraj i analizuj dane dziennika, włączając ustawienie diagnostyczne, a następnie użyj języka zapytań Kusto do eksplorowania wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462370"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Zbieranie i analizowanie danych dziennika dla usługi Azure Cognitive Search

Dzienniki diagnostyczne lub operacyjne zapewniają wgląd w szczegółowe operacje usługi Azure Cognitive Search i są przydatne do monitorowania procesów usługi i obciążenia. Wewnętrznie dzienniki istnieją na wewnętrznej bazy danych przez krótki okres czasu, wystarczające do badania i analizy, jeśli plik bilet pomocy technicznej. Jednak jeśli chcesz samokierunkowe nad danymi operacyjnymi, należy skonfigurować ustawienie diagnostyczne, aby określić, gdzie są zbierane informacje rejestrowania.

Konfigurowanie dzienników jest przydatne do diagnostyki i zachowania historii operacyjnej. Po włączeniu rejestrowania można uruchamiać kwerendy lub tworzyć raporty do analizy strukturalnej.

W poniższej tabeli wylicza się opcje zbierania i utrwalania danych.

| Zasób | Używana do |
|----------|----------|
| [Wyślij do obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Zdarzenia i metryki są wysyłane do obszaru roboczego usługi Log Analytics, które można wyszukiwać w portalu, aby zwrócić szczegółowe informacje. Aby uzyskać wprowadzenie, zobacz [Wprowadzenie do dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archiwum z pamięcią blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Zdarzenia i metryki są archiwizowane w kontenerze obiektów Blob i przechowywane w plikach JSON. Dzienniki mogą być dość szczegółowe (przez godzinę / minutę), przydatne do badania konkretnego incydentu, ale nie do otwartego dochodzenia. Za pomocą edytora JSON można wyświetlić nieprzetworzony plik dziennika lub usługi Power BI w celu agregowania i wizualizowania danych dziennika.|
| [Strumień do Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) | Zdarzenia i metryki są przesyłane strumieniowo do usługi Usługi Azure Event Hubs. Wybierz to jako alternatywną usługę zbierania danych dla bardzo dużych dzienników. |

Dzienniki usługi Azure Monitor i magazyn obiektów Blob są dostępne jako bezpłatna usługa, dzięki czemu można go wypróbować bezpłatnie przez cały okres istnienia subskrypcji platformy Azure. Usługa Application Insights może się zarejestrować i używać tak długo, jak rozmiar danych aplikacji jest w pewnych granicach (szczegółowe informacje można znaleźć na [stronie cennika).](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz usługi Log Analytics lub usługi Azure Storage, możesz utworzyć zasoby z wyprzedzeniem.

+ [Tworzenie obszaru roboczego analizy dzienników](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Włączanie zbierania danych

Ustawienia diagnostyczne określają sposób zbierania zarejestrowanych zdarzeń i metryk.

1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](./media/search-monitor-usage/diagnostic-settings.png "Ustawienia diagnostyczne")

1. Wybierz **+ Dodaj ustawienie diagnostyczne**

1. Sprawdź **analitykę dziennika,** wybierz obszar roboczy i wybierz **OperationLogs** i **AllMetrics**.

   ![Konfigurowanie zbierania danych](./media/search-monitor-usage/configure-storage.png "Konfigurowanie zbierania danych")

1. Zapisz ustawienie.

1. Po włączeniu rejestrowania użyj usługi wyszukiwania, aby rozpocząć generowanie dzienników i metryk. Zanim zarejestrowane zdarzenia i metryki staną się dostępne, zajmie trochę czasu.

W przypadku usługi Log Analytics będzie to kilka minut przed udostępnieniem danych, po czym można uruchomić zapytania Kusto w celu zwrócenia danych. Aby uzyskać więcej informacji, zobacz [Monitorowanie żądań kwerend](search-monitor-logs.md).

W przypadku magazynu obiektów Blob trwa godzinę, zanim kontenery pojawią się w magazynie obiektów Blob. Istnieje jeden obiekt blob, na godzinę, na kontener. Kontenery są tworzone tylko wtedy, gdy istnieje działanie do rejestrowania lub pomiaru. Gdy dane są kopiowane na konto magazynu, dane są formatowane jako JSON i umieszczane w dwóch kontenerach:

+ insights-logs-operationlogs: dla dzienników ruchu wyszukiwania
+ insights-metrics-pt1m: dla metryk

## <a name="query-log-information"></a>Informacje dziennika kwerend

W dziennikach diagnostycznych dwie tabele zawierają dzienniki i metryki dla usługi Azure Cognitive Search: **AzureDiagnostics** i **AzureMetrics**.

1. W obszarze **Monitorowanie**wybierz **pozycję Dzienniki**.

1. Wprowadź **azuremetrics** w oknie kwerendy. Uruchom tę prostą kwerendę, aby zapoznać się z danymi zebranymi w tej tabeli. Przewiń tabelę, aby wyświetlić metryki i wartości. Zwróć uwagę na liczbę rekordów u góry, a jeśli usługa zbiera metryki od jakiegoś czasu, możesz dostosować przedział czasu, aby uzyskać zestaw danych, którymi można zarządzać.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Wprowadź następującą kwerendę, aby zwrócić zestaw wyników tabelaryczne.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Powtórz poprzednie kroki, począwszy od **azurediagnostics,** aby zwrócić wszystkie kolumny w celach informacyjnych, a następnie bardziej selektywne zapytanie, które wyodrębnia bardziej interesujące informacje.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="log-schema"></a>Schemat dziennika

Struktury danych, które zawierają dane dziennika usługi Azure Cognitive Search są zgodne ze schematem poniżej. 

W przypadku magazynu obiektów Blob każdy obiekt blob ma jeden obiekt główny o nazwie **rekordy** zawierające tablicę obiektów dziennika. Każdy obiekt blob zawiera rekordy dla wszystkich operacji, które miały miejsce w tej samej godzinie.

Poniższa tabela zawiera częściową listę pól typowych dla rejestrowania diagnostycznego.

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| czasGenerowany |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/SUBSCRIPTIONS/1111111-1111-1111-1111-111111111111111111/<br/>GRUPY ZASOBÓW/DOMYŚLNE/DOSTAWCY/<br/> Microsoft. SZUKAJ/SZUKAJ USŁUGI/SEARCHSERVICE" |Identyfikator zasobu |
| operationName |ciąg |"Szukaj zapytań" |Nazwa operacji |
| operationVersion |ciąg |"2019-05-06" |Wersja api używana |
| category |ciąg |"OperationLogs" | — stała |
| resultType |ciąg |"Sukces" |Możliwe wartości: Sukces lub porażka |
| resultSignature |int |200 |Kod wyniku HTTP |
| durationMS |int |50 |Czas trwania operacji w milisekundach |
| properties |obiekt |zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

### <a name="properties-schema"></a>Schemat właściwości

Poniższe właściwości są specyficzne dla usługi Azure Cognitive Search.

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Description_s |ciąg |"GET /indexes('content')/docs" |Punkt końcowy operacji |
| Documents_d |int |42 |Liczba przetworzonych dokumentów |
| IndexName_s |ciąg |"test-indeks" |Nazwa indeksu skojarzonego z operacją |
| Query_s |ciąg |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parametry kwerendy |

## <a name="metrics-schema"></a>Schemat metryki

Metryki są przechwytywane dla żądań zapytań i mierzone w odstępach jednominutowych. Każda metryka udostępnia minimalne, maksymalne i średnie wartości na minutę. Aby uzyskać więcej informacji, zobacz [Monitorowanie żądań kwerend](search-monitor-queries.md).

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/SUBSCRIPTIONS/1111111-1111-1111-1111-111111111111111111/<br/>GRUPY ZASOBÓW/DOMYŚLNE/DOSTAWCY/<br/>Microsoft. SZUKAJ/SZUKAJ USŁUGI/SEARCHSERVICE" |identyfikator zasobu |
| metricName |ciąg |"Opóźnienie" |nazwa metryki |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |sygnatura czasowa operacji |
| średnia |int |64 |Średnia wartość próbek surowców w przedziale czasu metryki, jednostki w sekundach lub procentach, w zależności od metryki. |
| minimum |int |37 |Minimalna wartość próbek pierwotnych w przedziale czasu metryki, jednostki w sekundach. |
| maksimum |int |78 |Maksymalna wartość próbek pierwotnych w przedziale czasu metryki, jednostki w sekundach.  |
| suma |int |258 |Całkowita wartość próbek surowców w przedziale czasu metryki, jednostki w sekundach.  |
| count |int |4 |Liczba metryk emitowanych z węzła do dziennika w ciągu jednej minuty interwału.  |
| w czasie |ciąg |"PT1M" |Ziarno czasu metryki w ISO 8601. |

Jest to typowe dla kwerend do wykonania w milisekundach, więc tylko kwerendy, które mierzą jako sekundy pojawią się w metryki, takich jak QPS.

W przypadku metryki **Zapytania wyszukiwania na sekundę** minimalna jest najniższą wartością zapytań wyszukiwania na sekundę, która została zarejestrowana w tej minucie. To samo dotyczy wartości maksymalnej. Średnia, jest agregacji w całej minucie. Na przykład w ciągu jednej minuty może mieć wzorzec tak: jedna sekunda wysokiego obciążenia, który jest maksymalna dla SearchQueriesPerSecond, a następnie 58 sekund średniego obciążenia i wreszcie jedną sekundę z tylko jedną kwerendą, która jest minimalna.

W przypadku **procentu kwerend wyszukiwania ograniczonego**, minimalna, maksymalna, średnia i całkowita, wszystkie mają tę samą wartość: procent zapytań wyszukiwania, które zostały ograniczone, z całkowitej liczby zapytań wyszukiwania w ciągu jednej minuty.

## <a name="view-raw-log-files"></a>Wyświetlanie nieprzetworzonych plików dziennika

Magazyn obiektów blob służy do archiwizowania plików dziennika. Do wyświetlenia pliku dziennika można użyć dowolnego edytora JSON. Jeśli go nie masz, zalecamy [program Visual Studio Code.](https://code.visualstudio.com/download)

1. W witrynie Azure portal otwórz swoje konto magazynu. 

2. W lewym okienku nawigacji kliknij pozycję **Obiekty Blob**. Powinieneś zobaczyć **insights-logs-operationlogs** i **insights-metrics-pt1m**. Te kontenery są tworzone przez usługę Azure Cognitive Search, gdy dane dziennika są eksportowane do magazynu obiektów Blob.

3. Klikaj w dół hierarchię folderów, aż do niego dojdziesz do pliku .json.  Użyj menu kontekstowego, aby pobrać plik.

Po pobraniu pliku otwórz go w edytorze JSON, aby wyświetlić zawartość.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, zapoznaj się z podstawami monitorowania usługi wyszukiwania, aby dowiedzieć się więcej o pełnym zakresie możliwości nadzoru.

> [!div class="nextstepaction"]
> [Monitorowanie operacji i aktywności w usłudze Azure Cognitive Search](search-monitor-usage.md)