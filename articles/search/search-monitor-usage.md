---
title: Monitorowanie użycia i zapytania metryk zasobów usługi wyszukiwania — usługa Azure Search
description: Włącz rejestrowanie, uzyskać metryki działań zapytania, użycie zasobów i inne dane systemu z usługi Azure Search.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bac897178c8220abe72a92a5cf14fc4767cdd3bf
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755058"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitoruj aktywność zużycia i kwerendy zasobów w usłudze Azure Search

Na stronie Przegląd usługi Azure Search możesz wyświetlić systemowe dane dotyczące użycia zasobów, metryki zapytania i ile przydział jest dostępny do utworzenia więcej indeksów, indeksatorów i źródeł danych. Portal umożliwia również konfigurowanie usługi log analytics lub innego zasobu użytych na potrzeby zbierania danych. 

Konfigurowanie dzienników przydaje się do samoobsługowego diagnostyki i przy zachowaniu historię operacyjnej. Wewnętrznie dzienniki istnieje w wewnętrznej bazie danych krótki czas wystarczający do badania i analizy przypadku bilet pomocy technicznej. Jeśli chcesz kontrolować i dostęp do rejestrowania informacji, należy skonfigurować jedną z rozwiązania opisane w tym artykule.

Ten artykuł zawiera informacje na temat monitorowania, opcje, jak włączyć rejestrowanie i dziennika magazynu i sposób wyświetlania zawartości dzienników.

## <a name="metrics-at-a-glance"></a>Najistotniejszymi metrykami

**Użycie** i **monitorowanie** sekcje wbudowane w przeglądzie strony raportu na zużycie zasobów i metryk wykonywania zapytania. Te informacje będą dostępne, zaraz po uruchomieniu przy użyciu usługi, bez konieczności konfiguracji. Na tej stronie są odświeżane co kilka minut. Jeśli są finalizowanie decyzje dotyczące [którą warstwę dla obciążeń produkcyjnych](search-sku-tier.md), lub czy [dostosować liczbę aktywnych replik i partycji](search-capacity-planning.md), te metryki ułatwiają wykonywanie tych decyzji przez Pokazuje jak szybko są zużywane zasobów i jak bieżąca konfiguracja obsługuje istniejące obciążenia.

**Użycia** karta przedstawia dostępność zasobów względem bieżącego [limity](search-limits-quotas-capacity.md). Na poniższej ilustracji jest dla bezpłatnej usługi, jest ograniczone do 3 obiekty danego typu i 50 MB miejsca. Podstawowa lub standardowa usługi mają wyższe limity i zwiększenie liczby partycji pamięci masowej rośnie wraz ze proporcjonalnie.

![Stan zużycia względem skuteczne limity](./media/search-monitor-usage/usage-tab.png
 "stan zużycia względem skuteczne limity")

## <a name="queries-per-second-qps-and-other-metrics"></a>Zapytań na sekundę (QPS) i innych metryk

**Monitorowanie** karcie pokazuje średnie ruchome dla metryki, takie jak wyszukiwanie *zapytań na sekundę* (QPS), zagregowane na minutę. 
*Opóźnienie wyszukiwania* jest ilość czasu, Usługa wyszukiwania jest potrzebne do przetwarzania kwerend wyszukiwania, zagregowane na minutę. *Wyszukiwania z ograniczoną przepustowością zapytania procent* (niewyświetlany) jest wyrażoną w procentach kwerend wyszukiwania, które zostały ograniczone, również są agregowane na minutę.

Te liczby są przybliżone i mają na celu zapewniają ogólne informacje o tym jak system jest obsługi żądania. Rzeczywiste liczby zapytań na Sekundę mogą być wyższe lub niższe niż numer zgłoszony w portalu.

![Zapytania na drugie działanie](./media/search-monitor-usage/monitoring-tab.png "zapytania na drugie działanie")

## <a name="activity-logs"></a>Dzienniki aktywności

**Dziennika aktywności** zbiera informacje z usługi Azure Resource Manager. Przykładami informacji zawartych w dzienniku aktywności tworzenia lub usuwania usługi aktualizowanie grupy zasobów, sprawdzanie dostępności nazwy lub pobieranie klucza dostępu usługi do obsługi żądania. 

Możesz uzyskać dostęp **dziennika aktywności** z okienka nawigacji po lewej stronie lub powiadomienia w poleceniu okna górny pasek lub z **diagnozowanie i rozwiązywanie problemów** strony.

Użytkowanych zadań, takich jak tworzenie indeksu lub usuwanie źródła danych zostanie wyświetlone powiadomienie ogólnych, takich jak "Pobierz Admin Key" dla każdego żądania, ale nie sam określonej akcji. Ten poziom informacji, należy włączyć dodatek rozwiązania do monitorowania.

## <a name="add-on-monitoring-solutions"></a>Dodatek rozwiązania do monitorowania

Usługa Azure Search nie przechowuje żadnych danych poza obiektów, którymi zarządza, co oznacza dziennika, które dane mają być przechowywane zewnętrznie. Można skonfigurować dowolne poniższe zasoby, jeśli chcesz zachować dane dzienników. 

W poniższej tabeli porównano opcje przechowywania dzienników oraz dodanie, szczegółowe monitorowanie operacji usługi i obciążeń związanych z zapytaniami za pomocą usługi Application Insights.

| Resource | Używana do |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Zarejestrowane zdarzenia i metryki zapytania, oparte na schematach poniżej, skorelowane ze zdarzeniami użytkownika w aplikacji. Jest to jedyne rozwiązanie, które uwzględnia akcji użytkownika lub sygnały, mapowania zdarzeń z wyszukiwania zainicjowanego przez użytkownika, w przeciwieństwie do Filtrowanie żądań przesyłanych przez kod aplikacji. Aby użyć tego podejścia, kopiowania i wklejania kod Instrumentacji do plików źródłowych do trasy żądania informacji do usługi Application Insights. Aby uzyskać więcej informacji, zobacz [analiza ruchu wyszukiwania](search-traffic-analytics.md). |
| [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Zarejestrowane zdarzenia i metryki zapytania, oparte na schematach poniżej. Zdarzenia są rejestrowane do obszaru roboczego usługi Log Analytics. Obszar roboczy, aby zwrócić szczegółowe informacje z dziennika można uruchamiać zapytania. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z dziennikami usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Zarejestrowane zdarzenia i metryki zapytania, oparte na schematach poniżej. Zdarzenia są rejestrowane na kontener obiektów Blob i przechowywane w plikach w formacie JSON. Użyj edytora JSON, aby wyświetlić zawartość pliku.|
| [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) | Zarejestrowane zdarzenia i metryki zapytania, w oparciu o schematy opisany w tym artykule. Wybierz tę opcję jako usługa zbierania danych alternatywnych dla bardzo dużych dzienników. |

Dzienniki usługi Azure Monitor i usługi Blob storage są dostępne jako bezpłatna usługa udostępniona, dzięki czemu możesz wypróbować ją bezpłatnie przez okres istnienia subskrypcji platformy Azure. Application Insights jest bezpłatna zarejestrować i używać tak długo, jak rozmiar danych aplikacji podlega pewnym ograniczeniom (zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/) Aby uzyskać szczegółowe informacje).

Następna sekcja przeprowadzi Cię przez kroki włączania i używania usługi Azure Blob storage do zbierania i uzyskać dostęp do danych dziennika utworzone przez operacje usługi Azure Search.

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie obciążeń indeksowania i zapytania jest domyślnie wyłączona i jest zależna od dodatku rozwiązania dla infrastruktury rejestrowania i długoterminowego magazynu zewnętrznego. Przez siebie tylko utrwalonych danych w usłudze Azure Search są obiektów, tworzy i zarządza, więc dzienników muszą być przechowywane w innym miejscu.

W tej sekcji dowiesz się, jak używać magazynu obiektów Blob do przechowywania danych zarejestrowanych zdarzeń i metryk.

1. [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Jeśli nie masz jeszcze jeden. Należy je umieścić w tej samej grupie zasobów usługi Azure Search, aby uprościć czyszczenie później, jeśli chcesz usunąć wszystkie zasoby używane w tym ćwiczeniu.

   Konta magazynu musi istnieć w tym samym regionie co usługa Azure Search.

2. Otwórz stronę przeglądu usługi wyszukiwania w sieci. W okienku nawigacji po lewej stronie, przewiń w dół do **monitorowanie** i kliknij przycisk **Włącz monitorowanie**.

   ![Aby włączyć monitorowanie](./media/search-monitor-usage/enable-monitoring.png "Włącz monitorowanie")

3. Wybierz dane, które mają zostać wyeksportowane: Dzienniki, metryki lub obu. Możesz skopiować go do konta magazynu, wysyłać je do Centrum zdarzeń lub wyeksportować je do dzienników usługi Azure Monitor.

   Aby uzyskać dane archiwalne do magazynu obiektów Blob na koncie magazynu, musi istnieć. Kontenery i obiekty BLOB zostaną utworzone jako wymagane podczas eksportowania danych dziennika.

   ![Konfigurowanie obiektu blob magazynu archiwum](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurowanie obiektu blob magazynu w warstwie archiwum")

4. Zapisz profil.

5. Logowania przez tworzenie lub usuwanie obiektów dla testu (tworzy zdarzenia dziennika) i wysyłaniem zapytań (generuje metryk). 

Rejestrowanie jest włączone po zapisaniu profilu. Kontenery są tworzone tylko po działanie dziennika lub miary. Gdy dane są kopiowane do konta magazynu, dane te są w formacie JSON, a następnie umieszczony w dwóch kontenerów:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights — metryki pt1m: dla metryki

**Może potrwać godzinę przed kontenery pojawi się w magazynie obiektów Blob. Brak obiektu blob na godzinę na kontener.**

Możesz użyć [programu Visual Studio Code](#download-and-open-in-visual-studio-code) lub inny edytor plików JSON, aby wyświetlić pliki. 

### <a name="example-path"></a>Przykładowa ścieżka

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schemat dziennika
Obiekty BLOB, zawierające dzienniki ruchu usługi wyszukiwania są skonstruowane zgodnie z opisem w tej sekcji. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika. Każdy obiekt blob zawiera rekordy dla wszystkich operacji, które miały miejsce w ciągu jednej godziny.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |string |"2019-05-06" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |Stałe |
| resultType |ciąg |Komunikat "success" |Możliwe wartości: Powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania (w milisekundach) |
| properties |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

**Właściwości schematu**

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Opis |ciąg |"Pobierz /indexes('content')/docs" |Operacja punktu końcowego |
| Zapytanie |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parametry zapytania |
| Dokumenty |int |42 |Liczba przetworzonych dokumentów |
| indexName |ciąg |"testindex" |Nazwa indeksu skojarzone z operacją |

## <a name="metrics-schema"></a>Schemat metryki

Metryk jest przechwytywana dla żądań zapytań.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/>FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twój identyfikator zasobu |
| MetricName |ciąg |"Opóźnienie" |Nazwa metryki |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| średnia |int |64 |Średnia wartość próbek pierwotnych w odstępie czasu metryki |
| minimalnie |int |37 |Minimalna wartość nieprzetworzoną próbek w odstępie czasu metryki |
| maksymalnie |int |78 |Wartość maksymalna pierwotne próbek w odstępie czasu metryki |
| łącznie |int |258 |Łączna wartość pierwotne próbek w odstępie czasu metryki |
| count |int |4 |Liczba próbek raw, używany do generowania metrykę |
| ziarna czasu |ciąg |"PT1M" |Ziarno czasu metryką przy podejmowaniu ISO 8601 |

Wszystkie metryki są raportowane w odstępach jednej minuty. Każdy pomiar przedstawia wartości minimalna, maksymalna i średnia na minutę.

Metryki SearchQueriesPerSecond minimalna jest najniższa wartość dla zapytania wyszukiwania na sekundę, który został zarejestrowany w ciągu tej minuty. To samo dotyczy maksymalnej wartości. Średnia, to agregacji przez całą minutę.
Pomyśl o tym scenariuszu podczas jednej minuty: sekundy wysoki załadować oznacza to maksymalną SearchQueriesPerSecond, następuje 58 sekund średnie obciążenie i na koniec co sekundę przy użyciu tylko jednego zapytania, który jest minimalnym warunkiem.

Aby uzyskać ThrottledSearchQueriesPercentage, minimalna, maksymalna, średnia i total, wszystkie mają taką samą wartość: procent zapytań wyszukiwania, które zostały ograniczone z łączna liczba zapytań wyszukiwania w ciągu jednej minuty.

## <a name="download-and-open-in-visual-studio-code"></a>Pobierz i Otwórz w programie Visual Studio Code

Można użyć dowolnego edytora JSON, aby przejrzeć plik dziennika. Jeśli nie masz, firma Microsoft zaleca [programu Visual Studio Code](https://code.visualstudio.com/download).

1. W witrynie Azure portal Otwórz konto usługi Storage. 

2. W okienku nawigacji po lewej stronie kliknij **obiektów blob**. Powinien zostać wyświetlony **insights — dzienniki operationlogs** i **insights — metryki pt1m**. Te kontenery są tworzone przez usługę Azure Search, gdy dane dziennika są eksportowane do magazynu obiektów Blob.

3. Kliknij w dół hierarchii folderów, aż plik JSON.  Użyj menu kontekstowego, aby pobrać plik.

Po pobraniu pliku, otwórz go w edytorze JSON, aby wyświetlić jego zawartość.

## <a name="use-system-apis"></a>Użyj interfejsów API systemu
Zarówno w przypadku interfejsu API REST usługi Azure Search, jak i zestawu .NET SDK zapewniają programowy dostęp do informacji metryki, indeksu i indeksatora usługi i liczby dokumentów.

* [Zebranie statystyk, usługi](/rest/api/searchservice/get-service-statistics)
* [Pobieranie statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Liczba dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

Aby włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie usługą wyszukiwania w systemie Microsoft Azure](search-manage.md) więcej informacji na temat administrowania usługą i [wydajność i optymalizacja](search-performance-optimization.md) dla wskazówki dotyczące dostrajania.