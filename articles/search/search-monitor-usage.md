---
title: Statystyki użycia i zapytania zasobów dla usługi wyszukiwania — usługa Azure Search
description: Pobierz metryki działań zapytania, zużycia zasobów i inne dane systemu z usługi Azure Search.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f8a4e7dcaa1bc2df71246f67d06fc63ae4fcd06
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883504"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Monitorowanie usługi Azure Search w witrynie Azure portal

Na stronie Przegląd usługi Azure Search możesz wyświetlić systemowe dane dotyczące użycia zasobów, a także zapytania metryki, takie jak zapytania na drugim (QPS), opóźnienie zapytań i Procent żądań, które zostały ograniczone. Ponadto można użyć portalu wykorzystywać szeroką gamę możliwości platformy Azure dla obszerniejsze zbieranie danych monitorowania. 

W tym artykule identyfikuje i porównano opcje dostępne dla rejestrowania działań usługi Azure Search. Zawiera on instrukcje dotyczące włączania rejestrowania i dzienniki i sposobu rozszerzania na informacjach, które są zbierane.

Jeśli są wypełniając bilet pomocy technicznej, brak poszczególnych zadań i informacje, które należy podać. Pracowników działu pomocy technicznej mają informacje niezbędne do badania określonych problemów.  

## <a name="metrics-at-a-glance"></a>Najistotniejszymi metrykami

**Użycie** i **monitorowanie** sekcje wbudowaną omówienie wizualizacji wykorzystanie magazynu i metryk wykonywania zapytania. Te informacje będą dostępne, zaraz po uruchomieniu przy użyciu usługi, bez konieczności konfiguracji. Na tej stronie są odświeżane co kilka minut. Jeśli są finalizowanie decyzje dotyczące [którą warstwę dla obciążeń produkcyjnych](search-sku-tier.md), lub czy [dostosować liczbę aktywnych replik i partycji](search-capacity-planning.md), te metryki ułatwiają wykonywanie tych decyzji przez Pokazuje jak szybko są zużywane zasobów i jak bieżąca konfiguracja obsługuje istniejące obciążenia.

**Użycia** karta przedstawia dostępność zasobów względem bieżącego [limity](search-limits-quotas-capacity.md). Na poniższej ilustracji jest dla bezpłatnej usługi, jest ograniczone do 3 obiekty danego typu i 50 MB miejsca. Podstawowa lub standardowa usługi mają wyższe limity i zwiększenie liczby partycji pamięci masowej rośnie wraz ze proporcjonalnie.

![Stan zużycia względem skuteczne limity](./media/search-monitor-usage/usage-tab.png
 "stan zużycia względem skuteczne limity")

## <a name="queries-per-second-qps-and-other-metrics"></a>Zapytań na sekundę (QPS) i innych metryk

**Monitorowanie** karcie pokazuje średnie ruchome dla metryki, takie jak wyszukiwanie *zapytań na sekundę* (QPS), zagregowane na minutę. 
*Opóźnienie wyszukiwania* jest ilość czasu, Usługa wyszukiwania jest potrzebne do przetwarzania kwerend wyszukiwania, zagregowane na minutę. *Wyszukiwania z ograniczoną przepustowością zapytania procent* (niewyświetlany) jest wyrażoną w procentach kwerend wyszukiwania, które zostały ograniczone, również są agregowane na minutę.

![Zapytania na drugie działanie](./media/search-monitor-usage/monitoring-tab.png "zapytania na drugie działanie")

## <a name="activity-logs"></a>Dzienniki aktywności

**Dziennika aktywności** zbiera informacje z usługi Azure Resource Manager. Przykładami informacji zawartych w dzienniku aktywności tworzenia lub usuwania usługi aktualizowanie grupy zasobów, sprawdzanie dostępności nazwy lub pobieranie klucza dostępu usługi do obsługi żądania. 

Możesz uzyskać dostęp **dziennika aktywności** z okienka nawigacji po lewej stronie lub powiadomienia w poleceniu okna górny pasek lub z **diagnozowanie i rozwiązywanie problemów** strony.

Użytkowanych zadań, takich jak tworzenie indeksu lub usuwanie źródła danych zostanie wyświetlone powiadomienie ogólnych, takich jak "Pobierz Admin Key" dla każdego żądania, ale nie sam określonej akcji. Ten poziom informacji, należy włączyć dodatek rozwiązania do monitorowania.

## <a name="add-on-monitoring-solutions"></a>Dodatek rozwiązania do monitorowania

Usługa Azure Search nie przechowuje żadnych danych poza obiektów, którymi zarządza, co oznacza dziennika, które dane mają być przechowywane zewnętrznie. Można skonfigurować dowolne poniższe zasoby, jeśli chcesz zachować dane dzienników. 

W poniższej tabeli porównano opcje przechowywania dzienników oraz dodanie, szczegółowe monitorowanie operacji usługi i obciążeń związanych z zapytaniami za pomocą usługi Application Insights.

| Zasób | Używane dla |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | [Analiza ruchu wyszukiwania](search-traffic-analytics.md). Jest to jedyne rozwiązanie, które znajdują się dodatkowe informacje dotyczące żądań, wykraczając poza wartości określone w schematach rejestrowanie i metryki, które muszą być poniżej. Dzięki tej metodzie można kopiowanie i wklejanie kod Instrumentacji do plików źródłowych, aby kierować informacje o żądaniach do usługi Application Insights do analizy w danych wejściowych z termin query, zapytania z żadnych wyników i tak dalej. Firma Microsoft zaleca Power BI jako fronton analiza danych przechowywanych w usłudze Application Insights.  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Żądania i metryki, na podstawie jednej schematów poniżej. Zdarzenia są rejestrowane na kontener obiektów Blob. Firma Microsoft zaleca programu Excel lub Power BI jako fronton analiz do danych przechowywanych w usłudze Azure Blob storage.|
| [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) | Żądania i metryki, w oparciu o schematy opisany w tym artykule. Wybierz tę opcję jako usługa zbierania danych alternatywnych dla bardzo dużych dzienników. |

Usługa Azure search oferuje funkcje monitorowania [pakiet zawartości usługi Power BI](https://app.powerbi.com/getdata/services/azure-search) tak, aby analizować dane dzienników. Pakiet zawartości składa się z raportów skonfigurowany do automatycznego nawiązywania połączenia danych i zapewniają wizualizację wyników analizy danych dotyczących usługi wyszukiwania. Aby uzyskać więcej informacji, zobacz [stronę pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Opcji magazynu obiektów Blob jest dostępna jako bezpłatna usługa udostępniona, dzięki czemu możesz wypróbować ją bezpłatnie przez okres istnienia subskrypcji platformy Azure. Następna sekcja przeprowadzi Cię przez kroki włączania i używania usługi Azure Blob storage do zbierania i uzyskać dostęp do danych dziennika utworzone przez operacje usługi Azure Search.

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie obciążeń indeksowania i zapytania jest domyślnie wyłączona i zależy dodatek rozwiązania dla infrastruktury rejestrowania i magazynu zewnętrznego. Przez siebie tylko utrwalonych danych w usłudze Azure Search jest indeksów, więc dzienników muszą być przechowywane w innym miejscu.

W tej sekcji dowiesz się, jak używać magazynu obiektów Blob zawierają danych zarejestrowanych zdarzeń i metryk.

1. [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Jeśli nie masz jeszcze jeden. Należy je umieścić w tej samej grupie zasobów usługi Azure Search, aby uprościć czyszczenie później, jeśli chcesz usunąć wszystkie zasoby używane w tym ćwiczeniu.

2. Otwórz stronę przeglądu usługi wyszukiwania w sieci. W okienku nawigacji po lewej stronie, przewiń w dół do **monitorowanie** i kliknij przycisk **Włącz monitorowanie**.

   ![Aby włączyć monitorowanie](./media/search-monitor-usage/enable-monitoring.png "Włącz monitorowanie")

3. Wybierz dane, które mają zostać wyeksportowane: Dzienniki, metryki lub obu. Możesz skopiować go do konta magazynu, wysyłać je do Centrum zdarzeń lub wyeksportować je do usługi Log Analytics.

   Aby uzyskać dane archiwalne do magazynu obiektów Blob na koncie magazynu, musi istnieć. Kontenery i obiekty BLOB zostaną utworzone podczas eksportowania danych dziennika.

   ![Konfigurowanie obiektu blob magazynu archiwum](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurowanie obiektu blob magazynu w warstwie archiwum")

4. Zapisz profil.

5. Logowania przez tworzenie lub usuwanie obiektów dla testu (generuje dziennik operacji) i wysyłaniem zapytań (generuje metryk). 

Rejestrowanie jest włączone po zapisaniu profilu, kontenery są tworzone tylko wtedy, gdy występuje zdarzenie dotyczące dziennika lub miary. Może potrwać kilka minut, zanim kontenerów są wyświetlane. Możesz [wizualizowanie danych w usłudze Power BI](#analyze-with-power-bi) gdy stanie się dostępna.

Gdy dane są kopiowane do konta magazynu, dane te są w formacie JSON, a następnie umieszczony w dwóch kontenerów:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights — metryki pt1m: dla metryki

Brak obiektu blob na godzinę na kontener.

Przykładowa ścieżka: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json`

## <a name="log-schema"></a>Schemat dziennika
Obiekty BLOB, zawierające dzienniki ruchu usługi wyszukiwania są skonstruowane zgodnie z opisem w tej sekcji. Każdy obiekt blob ma jeden główny obiekt o nazwie **rekordów** zawierający tablicę obiektów dziennika. Każdy obiekt blob zawiera rekordy dla wszystkich operacji, które miały miejsce w ciągu jednej godziny.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |ciąg |"2017-11-11" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |Stałe |
| resultType |ciąg |Komunikat "success" |Możliwe wartości: Powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania (w milisekundach) |
| properties |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

**Właściwości schematu**

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Opis |ciąg |"Pobierz /indexes('content')/docs" |Operacja punktu końcowego |
| Zapytanie |ciąg |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Parametry zapytania |
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

## <a name="analyze-with-power-bi"></a>Analizowanie za pomocą usługi Power BI

Firma Microsoft zaleca używanie [usługi Power BI](https://powerbi.microsoft.com) można badać i wizualizować dane, zwłaszcza, jeśli włączono [analiza ruchu wyszukiwania](search-traffic-analytics.md). Aby uzyskać więcej informacji, zobacz [stronę pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Połączenia wymagają magazynu nazwę oraz klucz dostępu konta, którą możesz pobrać ze strony portalu platformy Azure na **klucze dostępu** strony pulpitu nawigacyjnego konta magazynu.

1. Zainstaluj [usługi Power BI Content Pack](https://app.powerbi.com/getdata/services/azure-search). Pakiet zawartości dodaje wstępnie zdefiniowanych wykresów i tabel przydatne podczas analizowania dodatkowe dane przechwycone dla analiza ruchu wyszukiwania. 

   Jeśli używasz magazynu obiektów Blob lub inny mechanizm magazynu, a nie dodano Instrumentacji w kodzie, możesz pominąć ten pakiet zawartości i korzystanie z wbudowanych wizualizacji usługi Power BI.

2. Otwórz **usługi Power BI**, kliknij przycisk **Pobierz dane** > **usług** > **usługi Azure Search**.

3. Wprowadź nazwę konta magazynu, wybierz **klucz** do uwierzytelniania i Wklej klucz dostępu.

4. Importowanie danych, a następnie kliknij przycisk **wyświetlania danych**.

Poniższy zrzut ekranu przedstawia wbudowanych raportów i wykresów na potrzeby analizowania, analiza ruchu wyszukiwania.

![Pulpit nawigacyjny usługi Power BI dla usługi Azure Search](./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png "nawigacyjnym usługi Power BI dla usługi Azure Search")

## <a name="get-sys-info-apis"></a>Uzyskaj informacje o sys interfejsów API
Zarówno w przypadku interfejsu API REST usługi Azure Search, jak i zestawu .NET SDK zapewniają programowy dostęp do informacji metryki, indeksu i indeksatora usługi i liczby dokumentów.

* [Zebranie statystyk, usługi](/rest/api/searchservice/get-service-statistics)
* [Pobieranie statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Liczba dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

Aby włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie usługą wyszukiwania w systemie Microsoft Azure](search-manage.md) więcej informacji na temat administrowania usługą i [wydajność i optymalizacja](search-performance-optimization.md) dla wskazówki dotyczące dostrajania.

Dowiedz się więcej na temat tworzenia zachwycającymi raportami. Zobacz [wprowadzenie do usługi Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) Aby uzyskać szczegółowe informacje.

