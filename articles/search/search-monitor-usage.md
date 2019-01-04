---
title: Monitorowanie użycia i statystyk usługi wyszukiwania — usługa Azure Search
description: Śledzenie użycia i indeks rozmiar zasobu usługi Azure Search, Usługa wyszukiwania w hostowanej chmurze Microsoft Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: aaeb24b836b47f72d0be299738e6c90f599f8d1f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631903"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Monitorowanie usługi Azure Search w witrynie Azure portal

Usługa Azure Search udostępnia różne zasoby dla śledzenia użycia i wydajności usługi wyszukiwania. Daje ona dostęp do metryk, dzienników, statystyki indeksu i rozszerzone możliwości monitorowania w usłudze Power BI. W tym artykule opisano sposób włączania różne strategie monitorowania i jak interpretować dane wynikowe.

## <a name="azure-search-metrics"></a>Metryki usługi Azure Search
Metryki umożliwiają niemal w czasie rzeczywistym wgląd w usługi wyszukiwania i są dostępne dla każdej usługi, za pomocą żadna dodatkowa konfiguracja. Umożliwiają one można śledzić wydajność usługi przez maksymalnie 30 dni.

Usługa Azure Search służy do zbierania danych, aby uzyskać trzy różne metryki:

* Opóźnienie wyszukiwania: Czas usługę wyszukiwania, potrzebne do przetwarzania kwerend wyszukiwania, zagregowane na minutę.
* Zapytania wyszukiwania na sekundę (QPS): Liczba wyszukiwania kwerend odebranych na sekundę, zagregowane na minutę.
* Procent zapytań wyszukiwania z ograniczoną przepustowością: Procent zapytań wyszukiwania, które zostały ograniczone zagregowane na minutę.

![Zrzut ekranu QPS działania][1]

### <a name="set-up-alerts"></a>Konfigurowanie alertów
Na stronie szczegółów metryki można skonfigurować alerty, aby wyzwolić wiadomość e-mail z powiadomieniem lub zautomatyzowanych akcji, gdy Metryka przekracza wartość progową, które zostały zdefiniowane.

Aby uzyskać więcej informacji na temat metryk Sprawdź pełną dokumentację dotyczącą usługi Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Jak śledzić użycie zasobów
Śledzenie indeksy i rozmiaru dokumentu może pomóc aktywnie dostosować pojemność przed osiągnięcia górnego limitu, który został określony dla usługi. Można to zrobić w portalu lub programowo przy użyciu interfejsu API REST.

### <a name="using-the-portal"></a>Korzystanie z portalu

Do monitorowania użycia zasobów, wyświetlania liczby i statystyki dla usługi w [portal](https://portal.azure.com).

1. Zaloguj się do [portalu](https://portal.azure.com).
2. Otwórz pulpit nawigacyjny usługi Azure Search. Kafelki usługi można znaleźć na stronie głównej, lub możesz przejść do usługi z przeglądania na pasku dostępu.

Sekcja użycia zawiera miernika, informujące o tym, jaka część dostępne zasoby są obecnie używane. Aby uzyskać informacji na temat limitów za daną usługę, indeksów, dokumenty i magazynu, zobacz [limitów usług](search-limits-quotas-capacity.md).

  ![Kafelek użycie][2]

> [!NOTE]
> Na powyższym zrzucie ekranu jest bezpłatna usługa, która może zawierać maksymalnie jedną replikę i każdej partycji i można tylko hosta 3 indeksów, 10 000 dokumentów lub 50 MB danych, osiągnięta jako pierwsza. Utworzone w warstwie podstawowa lub standardowa usługi mają znacznie większe limity usługi. Aby uzyskać więcej informacji na temat wybierania warstwy, zobacz [wybierz jednostkę SKU lub warstwy](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Korzystanie z interfejsu API REST
Zestaw SDK platformy .NET i interfejsu API REST usługi Azure Search zapewniają programistyczny dostęp do metryk usług.  Jeśli używasz [indeksatory](https://msdn.microsoft.com/library/azure/dn946891.aspx) ładowanie indeksu z bazy danych SQL Azure lub usługi Azure Cosmos DB, interfejsu API jest dostępnych numerów, potrzebujesz.

* [Pobieranie statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Liczba dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Eksportowanie dzienników i metryk

Dzienniki operacji można wyeksportować do usługi i dane pierwotne dotyczące metryk, opisanego w poprzedniej sekcji. Let, wiesz, jak usługa jest używana i mogą być używane z usługi Power BI, gdy dane są kopiowane do konta magazynu dzienników operacji. Usługa Azure search udostępnia monitorowania pakietu zawartości usługi Power BI, w tym celu.


### <a name="enabling-monitoring"></a>Włączanie monitorowania
Otwórz swoją usługę Azure Search w [witryny Azure portal](https://portal.azure.com) pod opcją Włącz monitorowanie.

Wybierz dane, które mają zostać wyeksportowane: Dzienniki, metryki lub obu. Możesz skopiować go do konta magazynu, wysyłać je do Centrum zdarzeń lub wyeksportować je do usługi Log Analytics.

![Jak włączyć monitorowanie w portalu][3]

Aby włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Schematy dzienniki i metryki
Gdy dane są kopiowane do konta magazynu, dane są sformatowane jako JSON i jego miejsce w dwóch kontenerów:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights — metryki pt1m: dla metryki

Brak obiektu blob na godzinę na kontener.

Przykładowa ścieżka: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schemat dziennika
Dzienniki obiektów blob zawierają dzienniki ruchu usługi wyszukiwania.
Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika.
Każdy obiekt blob ma rekordów o nieudanej operacji, które miało miejsce w ciągu jednej godziny.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |ciąg |"2015-02-28" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |Stałe |
| resultType |ciąg |Komunikat "success" |Możliwe wartości: Powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania (w milisekundach) |
| properties |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

**Właściwości schematu**

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Opis |ciąg |"Pobierz /indexes('content')/docs" |Operacja punktu końcowego |
| Zapytanie |ciąg |"? wyszukiwania = AzureSearch & $count = true & parametru api-version = 2015-02-28" |Parametry zapytania |
| Dokumenty |int |42 |Liczba przetworzonych dokumentów |
| indexName |ciąg |"testindex" |Nazwa indeksu skojarzone z operacją |

#### <a name="metrics-schema"></a>Schemat metryki

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/>FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twój identyfikator zasobu |
| MetricName |ciąg |"Opóźnienie" |Nazwa metryki |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
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

## <a name="analyzing-your-data-with-power-bi"></a>Analizowanie danych przy użyciu usługi Power BI

Firma Microsoft zaleca używanie [usługi Power BI](https://powerbi.microsoft.com) można badać i wizualizować dane. Można łatwo połączyć z kontem magazynu platformy Azure i szybko zacząć analizować dane.

Usługa Azure Search udostępnia [pakiet zawartości usługi Power BI](https://app.powerbi.com/getdata/services/azure-search) pozwala na monitorowanie i zrozumienie ruchu wyszukiwania przy użyciu wstępnie zdefiniowanych wykresów i tabel. Zawiera zestaw raportów usługi Power BI, które automatyczne łączenie z danymi i zapewniają wizualizację wyników analizy danych dotyczących usługi wyszukiwania. Aby uzyskać więcej informacji, zobacz [stronę pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Pulpit nawigacyjny usługi Power BI dla usługi Azure Search][4]

## <a name="next-steps"></a>Kolejne kroki
Przegląd [skalowanie replik i partycji](search-limits-quotas-capacity.md) wskazówki dotyczące sposobu równoważenia przydziału partycji i replik dla istniejącej usługi.

Odwiedź stronę [Zarządzanie usługą wyszukiwania w systemie Microsoft Azure](search-manage.md) więcej informacji na temat administrowania usługą lub [wydajność i optymalizacja](search-performance-optimization.md) dla wskazówki dotyczące dostrajania.

Dowiedz się więcej na temat tworzenia zachwycającymi raportami. Zobacz [wprowadzenie do usługi Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) Aby uzyskać szczegółowe informacje

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
