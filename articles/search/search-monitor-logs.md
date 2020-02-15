---
title: Zbieranie danych dziennika
titleSuffix: Azure Cognitive Search
description: Zbieranie i analizowanie danych dziennika przez włączenie ustawienia diagnostycznego, a następnie użycie języka zapytań Kusto do eksplorowania wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211180"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Zbieranie i analizowanie danych dziennika dla usługi Azure Wyszukiwanie poznawcze

Dzienniki diagnostyczne lub operacyjne zapewniają wgląd w szczegółowe operacje na platformie Azure Wyszukiwanie poznawcze i są przydatne do monitorowania procesów usług i obciążeń. Wewnętrznie dzienniki znajdują się w zapleczu przez krótki czas, co jest wystarczające do zbadania i analizy w przypadku utworzenia biletu pomocy technicznej. Jeśli jednak chcesz mieć własny kierunek danych operacyjnych, należy skonfigurować ustawienie diagnostyczne, aby określić, gdzie zbierane są informacje o rejestrowaniu. 

Konfigurowanie dzienników jest przydatne w przypadku diagnostyki i zachowania historii operacyjnej. Po włączeniu rejestrowania można uruchamiać zapytania lub tworzyć raporty na potrzeby analizy strukturalnej.

W poniższej tabeli przedstawiono opcje zbierania i utrwalania danych.

| Zasób | Służy do |
|----------|----------|
| [Wyślij do obszaru roboczego Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Zarejestrowane zdarzenia i metryki zapytań na podstawie schematów poniżej. Zdarzenia są rejestrowane w obszarze roboczym Log Analytics. Za pomocą Log Analytics można uruchamiać zapytania w celu zwrócenia szczegółowych informacji. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archiwizowanie przy użyciu magazynu obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Zarejestrowane zdarzenia i metryki zapytań na podstawie schematów poniżej. Zdarzenia są rejestrowane w kontenerze obiektów blob i przechowywane w plikach JSON. Dzienniki mogą być bardzo szczegółowe (przez godzinę/minutę), przydatne do przeszukiwania konkretnego zdarzenia, ale nie dla badania otwartego. Użyj edytora JSON, aby wyświetlić plik dziennika.|
| [Przesyłanie strumieniowe do centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) | Rejestrowane zdarzenia i metryki zapytań na podstawie schematów udokumentowanych w tym artykule. Wybierz tę opcję jako alternatywną usługę zbierania danych dla bardzo dużych dzienników. |

Zarówno dzienniki Azure Monitor, jak i magazyn obiektów BLOB są dostępne jako bezpłatna usługa, dzięki czemu można wypróbować ją bezpłatnie w okresie istnienia subskrypcji platformy Azure. Application Insights jest bezpłatny, aby zarejestrować się i używać tak długo, jak rozmiar danych aplikacji ma określone limity (szczegółowe informacje znajdują się na [stronie cennika](https://azure.microsoft.com/pricing/details/monitor/) ).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku korzystania z usługi Log Analytics lub magazynu Azure można z góry utworzyć zasoby.

+ [Tworzenie obszaru roboczego usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Utwórz konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , jeśli potrzebujesz archiwum dzienników.

## <a name="create-a-log"></a>Tworzenie dziennika

Ustawienia diagnostyczne definiują zbieranie danych. Ustawienie określa, jak i co jest zbierane. 

1. W obszarze **monitorowanie**wybierz pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](./media/search-monitor-usage/diagnostic-settings.png "Ustawienia diagnostyczne")

1. Wybierz **+ Dodaj ustawienie diagnostyczne**

1. Wybierz dane, którą chcesz wyeksportować: dzienniki, metryki lub obu. Dane można zbierać w ramach konta magazynu, obszaru roboczego usługi log Analytics lub przesyłania strumieniowego do centrum zdarzeń.

   Usługa log Analytics jest zalecana, ponieważ można wykonywać zapytania dotyczące obszaru roboczego w portalu.

   Jeśli używasz również usługi BLOB Storage, kontenery i obiekty blob zostaną utworzone w miarę konieczności podczas eksportowania danych dziennika.

   ![Konfiguruj zbieranie danych](./media/search-monitor-usage/configure-storage.png "Konfiguruj zbieranie danych")

1. Zapisz ustawienie.

1. Przetestuj przez tworzenie lub usuwanie obiektów (tworzy zdarzenia dziennika) i przesyłając zapytania (generuje metryki). 

W magazynie obiektów BLOB kontenery są tworzone tylko w przypadku działania do rejestrowania lub mierzenia. Po skopiowaniu danych na konto magazynu dane są formatowane jako kod JSON i umieszczane w dwóch kontenerach:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights — metryki pt1m: dla metryki

**Trwa godzinę, zanim kontenery pojawią się w magazynie obiektów BLOB. Dla każdego kontenera istnieje jeden obiekt BLOB o godzinie.**

Dzienniki są archiwizowane przez każdą godzinę, w której występuje działanie. Następująca ścieżka jest przykładem jednego pliku dziennika utworzonego w styczniu 12 2020 w dniu 9:00 rano gdzie każdy `/` jest folderem: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>Schemat dziennika

Struktury danych zawierające dane dziennika usługi Azure Wyszukiwanie poznawcze są zgodne ze schematem poniżej. 

W przypadku usługi BLOB Storage każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika. Każdy obiekt BLOB zawiera rekordy dla wszystkich operacji, które miały miejsce w danej godzinie.

Poniższa tabela jest częściową listą pól wspólnych dla rejestrowania diagnostycznego.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |ciąg |"2019-05-06" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |— stała |
| resultType |ciąg |Komunikat "success" |Możliwe wartości: powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania (w milisekundach) |
| właściwości |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

### <a name="properties-schema"></a>Schemat właściwości

Poniższe właściwości są specyficzne dla Wyszukiwanie poznawcze platformy Azure.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Description_s |ciąg |"Pobierz /indexes('content')/docs" |Operacja punktu końcowego |
| Documents_d |int |42 |Liczba przetworzonych dokumentów |
| IndexName_s |ciąg |"test-index" |Nazwa indeksu skojarzone z operacją |
| Query_s |ciąg |"? Search = AzureSearch & $count = true & API-Version = 2019-05-06" |Parametry zapytania |

## <a name="metrics-schema"></a>Schemat metryki

Metryki są przechwytywane dla żądań zapytań i mierzone w ciągu jednej minuty. Każdy pomiar przedstawia wartości minimalna, maksymalna i średnia na minutę. Aby uzyskać więcej informacji, zobacz [monitorowanie żądań zapytań](search-monitor-queries.md).

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/>FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Identyfikator zasobu |
| metricName |ciąg |"Opóźnienie" |Nazwa metryki |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| średnia |int |64 |Średnia wartość próbek pierwotnych w interwale czasu metryki, jednostki w sekundach lub procent, w zależności od metryki. |
| minimalnie |int |37 |Minimalna wartość próbek pierwotnych w interwale czasu metryki (w sekundach). |
| maksymalnie |int |78 |Maksymalna wartość próbek pierwotnych w interwale czasu metryki (w sekundach).  |
| łącznie |int |258 |Całkowita wartość próbek pierwotnych w interwale czasu metryki (w sekundach).  |
| {1&gt;count&lt;1} |int |4 |Liczba metryk emitowanych z węzła do dziennika w interwale jednominutowym.  |
| ziarna czasu |ciąg |"PT1M" |Ziarno czasu metryki w ISO 8601. |

Jest to typowe dla zapytań wykonywanych w milisekundach, dlatego w metrykach, takich jak zapytań, będą wyświetlane tylko zapytania, które mierzą jako sekundy.

W przypadku metryk **kwerendy wyszukiwania na sekundę** minimalna wartość to najmniejsza liczba zapytań wyszukiwania na sekundę, które zostały zarejestrowane w tej minucie. To samo dotyczy maksymalnej wartości. Średnia, to agregacji przez całą minutę. Na przykład w ciągu jednej minuty może istnieć wzorzec podobny do tego: jedna sekunda wysokiego obciążenia, która jest maksymalną wartością dla SearchQueriesPerSecond, a następnie 58 sekund średniego obciążenia, a wreszcie jedna sekunda z tylko jednym zapytaniem, co jest minimalne.

W przypadku **kwerend wyszukiwania z ograniczeniami wartość procentowa**, minimum, maksimum, średnia i suma — wszystkie mają tę samą wartość: procent zapytań wyszukiwania, które zostały ograniczone, od łącznej liczby zapytań wyszukiwania w ciągu jednej minuty.

## <a name="view-log-files"></a>Wyświetl pliki dziennika

Magazyn obiektów BLOB jest używany do archiwizowania plików dziennika. Aby wyświetlić plik dziennika, można użyć dowolnego edytora JSON. Jeśli go nie masz, zalecamy [Visual Studio Code](https://code.visualstudio.com/download).

1. W Azure Portal Otwórz konto magazynu. 

2. W okienku nawigacji po lewej stronie kliknij pozycję **obiekty blob**. Powinny być widoczne informacje **Insights-Logs-operationlogs** i **Insights-Metrics-pt1m**. Te kontenery są tworzone przez usługę Azure Wyszukiwanie poznawcze podczas eksportowania danych dziennika do magazynu obiektów BLOB.

3. Kliknij kolejno pozycje hierarchia folderów, aby uzyskać dostęp do pliku. JSON.  Użyj menu kontekstowego, aby pobrać plik.

Po pobraniu pliku Otwórz go w edytorze JSON, aby wyświetlić jego zawartość.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zapoznaj się z tematem monitorowanie usługi wyszukiwania, aby dowiedzieć się więcej na temat pełnego zakresu możliwości nadzoru.

> [!div class="nextstepaction"]
> [Monitoruj operacje i działania na platformie Azure Wyszukiwanie poznawcze](search-monitor-usage.md)