---
title: Monitorowanie użycia oraz statystyki w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Śledzenie rozmiar konsumenckich i indeksu zasobów dla usługi wyszukiwanie Azure, Usługa wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 286569eef8e17909ecab017b67b0ffc044a4bfe4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-an-azure-search-service"></a>Monitorowanie usługi Azure Search

Usługa Azure Search udostępnia różnych zasobów do śledzenia użycia i wydajności usługi wyszukiwania. Umożliwia on dostęp do metryk, dzienniki statystyki indeksu i rozszerzone możliwości monitorowania usługi Power BI. W tym artykule opisano sposób włączania różne strategie monitorowania oraz sposób interpretowania danych.

## <a name="azure-search-metrics"></a>Metryki usługi Azure Search
Metryki zapewniają niemal w czasie rzeczywistym widoczność usługi wyszukiwania i są dostępne dla każdej usługi, bez dodatkowych ustawień. Pozwalają śledzić wydajność usługi przez maksymalnie 30 dni.

Usługa Azure Search zbiera dane dla trzech różnych metryki:

* Wyszukaj opóźnienia: czasu usługi wyszukiwania niezbędnego do przetworzenia zapytania wyszukiwania, agregowane na minutę.
* Wyszukaj zapytania na sekundę (QPS): liczba wyszukiwania zapytań odebranych na sekundę, agregowane na minutę.
* Wartość procentowa zapytań wyszukiwania z ograniczeniem przepustowości: procent zapytania wyszukiwania, które zostały ograniczany agregowana na minutę.

![Zrzut ekranu QPS działania][1]

### <a name="set-up-alerts"></a>Konfigurowanie alertów
Na stronie szczegółów metryki można skonfigurować alerty, aby wyzwolić wiadomość e-mail z powiadomieniem lub akcji automatycznej podczas metrykę przekracza wartość progową, który został zdefiniowany.

Aby uzyskać więcej informacji na temat metryki Sprawdź pełną dokumentację w monitorze Azure.  

## <a name="how-to-track-resource-usage"></a>Jak śledzić użycie zasobów
Śledzenie indeksy i rozmiar dokumentu może pomóc aktywnie Dostosuj wydajność przed szukaniem górny limit, który został określony dla usługi. Można to zrobić w portalu lub programowo przy użyciu interfejsu API REST.

### <a name="using-the-portal"></a>Korzystanie z portalu

Monitorowanie użycia zasobów, wyświetlanie liczby i statystyki dla usługi w [portal](https://portal.azure.com).

1. Zaloguj się do [portal](https://portal.azure.com).
2. Otwórz pulpit nawigacyjny usługi Azure Search. Kafelki na potrzeby usługi można znaleźć na stronie głównej lub można przeglądać przy użyciu funkcji przeglądania na pasku przechodzenia do usługi.

Sekcja użycia zawiera miernika, informujący o tym, jaka część dostępne zasoby są obecnie używane. Aby informacji na temat limitów-service dla indeksów, dokumentów i magazynu, zobacz [usługi limity](search-limits-quotas-capacity.md).

  ![Kafelek użycie][2]

> [!NOTE]
> Zrzut ekranu powyżej dla bezpłatnej usługi, która może zawierać maksymalnie jedną replikę każdej partycji i można tylko indeksów hosta 3, 10 000 dokumentów lub 50 MB danych, zależnie od zostanie osiągnięty jako pierwszy. Usługi utworzonego w dniu warstwy Basic lub Standard mają znacznie większe ograniczenia usługi. Aby uzyskać więcej informacji o wyborze warstwy, zobacz [wybierz warstwę lub SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Przy użyciu interfejsu API REST
Zarówno w przypadku interfejsu API REST usługi Azure Search, jak i zestawu .NET SDK umożliwiają programowy dostęp do metryk usługi.  Jeśli używasz [indeksatory](https://msdn.microsoft.com/library/azure/dn946891.aspx) indeksu załadować z bazy danych SQL Azure lub bazy danych rozwiązania Cosmos platformy Azure, interfejsu API jest dostępnych numerów, potrzebujesz.

* [Uzyskać statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Liczba dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Jak wyeksportować dzienniki i metryki

Możesz wyeksportować dzienniki operacji dla usługi i nieprzetworzonych danych dla metryki opisanych w poprzedniej sekcji. Dzienniki operacji let wiesz, jak usługa jest używana i mogą być używane z usługi Power BI po skopiowaniu danych do konta magazynu. Usługa Azure search udostępnia monitorowania pakiet zawartości usługi Power BI w tym celu.


### <a name="enabling-monitoring"></a>Włączanie monitorowania
Otwórz usługi Azure Search w [portalu Azure](http://portal.azure.com) w obszarze opcji Włącz monitorowanie.

Wybierz dane, aby wyeksportować: dzienniki, metryki lub oba. Możesz skopiować go do konta magazynu, wysyłają je do Centrum zdarzeń lub eksportowania ich do analizy dzienników.

![Włączanie monitorowania w portalu][3]

Aby włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Schematy dzienniki i metryki
Po skopiowaniu danych na konto magazynu danych jest w formacie JSON i jego miejsce w dwóch kontenerów:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights metryki pt1m: dla metryki

Brak obiektu blob na godzinę na kontenera.

Przykład ścieżki: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schemat dziennika
Obiekty BLOB dzienników zawierają dzienniki ruchu usługi wyszukiwania.
Każdy obiekt blob ma jeden obiekt głównego o nazwie **rekordów** zawiera tablicę obiektów dziennika.
Każdy obiekt blob zawiera rekordy na działanie, które miało miejsce podczas tę samą godzinę.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"11111111-1111-1111-1111-111111111111/SUBSCRIPTIONS / /<br/>DOSTAWCÓW RESOURCEGROUPS/DOMYŚLNIE /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |ciąg |"2015-02-28" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |Stała |
| resultType |ciąg |"Powodzenie" |Możliwe wartości: powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania w milisekundach |
| properties |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

**Schemat właściwości**
| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Opis |ciąg |"GET /indexes('content')/docs" |Operacja punktu końcowego |
| Zapytanie |ciąg |"? wyszukiwania = AzureSearch & $count = true & api-version = 2015-02-28" |Parametry zapytań |
| Dokumenty |int |42 |Liczba przetworzonych dokumentów |
| indexName |ciąg |"testindex" |Nazwa indeksu skojarzonych z operacją |

#### <a name="metrics-schema"></a>Metryki schematu
| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"11111111-1111-1111-1111-111111111111/SUBSCRIPTIONS / /<br/>DOSTAWCÓW RESOURCEGROUPS/DOMYŚLNIE /<br/>FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Identyfikator zasobu |
| metricName |ciąg |"Opóźnienie" |Nazwa metryki |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| średnio |int |64 |Średnia wartość próbek pierwotnych w przedziale czasu metryki |
| minimalnie |int |37 |Wartość minimalna pierwotnych próbek w przedziale czasu metryki |
| maksymalnie |int |78 |Wartość maksymalna pierwotnych próbek w przedziale czasu metryki |
| łącznie |int |258 |Łączna wartość Nieprzetworzona próbek w przedziale czasu metryki |
| liczba |int |4 |Liczba próbek pierwotnych służący do generowania metrykę |
| ziarnem czasu |ciąg |"PT1M" |Ziarnem czasu metryki w ISO 8601 |

Wszystkie metryki są zgłaszane w odstępach jednej minuty. Każdy pomiar przedstawia minimalne, maksymalne i średnie wartości na minutę.

Metryki SearchQueriesPerSecond minimum jest wartością najniższą zapytań wyszukiwania na sekundę, który został zarejestrowany w ciągu tej minuty. Wartość maksymalna to samo dotyczy. Średnia, to agregacji przez cały minutę.
Pomyśl o tym scenariuszu w ciągu jednej minuty: jednej sekundzie wysoki załadować oznacza to maksymalną dla SearchQueriesPerSecond następuje 58 sekund średni obciążenia i koniec jednej sekundy z tylko jednym zapytaniu, czyli minimalnym.

Dla ThrottledSearchQueriesPercentage, minimalne, maksymalne, średni i całkowitej, wszystkie mają taką samą wartość: wartość procentowa zapytania wyszukiwania, które zostały ograniczany z całkowitej liczby zapytań wyszukiwania w ciągu jednej minuty.

## <a name="analyzing-your-data-with-power-bi"></a>Analizowanie danych z usługi Power BI

Firma Microsoft zaleca używanie [usługi Power BI](https://powerbi.microsoft.com) poznanie i wizualizować dane. Można łatwo połączyć z kontem magazynu platformy Azure i szybko rozpocząć analizowanie danych.

Usługa Azure Search udostępnia [pakiet zawartości Power BI](https://app.powerbi.com/getdata/services/azure-search) który umożliwia monitorowanie i zrozumienie ruchu wyszukiwania z wstępnie zdefiniowanych wykresów i tabel. Zawiera zestaw raportów usługi Power BI, które automatycznie Połącz z danymi i podaj visual informacjami na temat usługi wyszukiwania. Aby uzyskać więcej informacji, zobacz [strona pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Power BI, odwiedź pulpit nawigacyjny usługi Azure Search][4]

## <a name="next-steps"></a>Kolejne kroki
Przegląd [skalowania repliki i partycje](search-limits-quotas-capacity.md) wskazówki dotyczące sposobu równoważenia Alokacja partycji i replik dla istniejącej usługi.

Odwiedź stronę [Zarządzanie usługą wyszukiwania w systemie Microsoft Azure](search-manage.md) uzyskać więcej informacji dotyczących administracji usługi lub [wydajności i optymalizacji](search-performance-optimization.md) dostrojenia wskazówki.

Dowiedz się więcej na temat tworzenia wspaniałych raportów. Zobacz [wprowadzenie Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) Aby uzyskać więcej informacji

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
