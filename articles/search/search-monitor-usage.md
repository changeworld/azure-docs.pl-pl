---
title: Monitorowanie użycia zasobów i metryk zapytania
titleSuffix: Azure Cognitive Search
description: Włącz rejestrowanie, Pobierz metryki działania zapytania, użycie zasobów i inne dane systemu z usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7ef868f156ac537cb066f293872f69135c4df25f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059656"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-cognitive-search"></a>Monitorowanie użycia zasobów i działania zapytań w usłudze Azure Wyszukiwanie poznawcze

Na stronie Przegląd usługi Azure Wyszukiwanie poznawcze można wyświetlić dane systemowe dotyczące użycia zasobów, metryk zapytania i ilość dostępnego przydziału, aby utworzyć więcej indeksów, indeksatorów i źródeł danych. Możesz również użyć portalu, aby skonfigurować usługę log Analytics lub inny zasób używany do trwałego zbierania danych. 

Konfigurowanie dzienników jest przydatne w przypadku samoobsługowej diagnostyki i zachowywania historii operacyjnej. Wewnętrznie dzienniki znajdują się w zapleczu przez krótki czas, co jest wystarczające do zbadania i analizy w przypadku utworzenia biletu pomocy technicznej. Jeśli chcesz kontrolować i uzyskiwać dostęp do informacji o dziennikach, należy skonfigurować jedno z rozwiązań opisanych w tym artykule.

Ten artykuł zawiera informacje na temat opcji monitorowania, sposobu włączania rejestrowania i przechowywania dzienników oraz wyświetlania zawartości dziennika.

## <a name="metrics-at-a-glance"></a>Metryki w skrócie

Sekcje **użycia** i **monitorowania** wbudowane na stronie przeglądu raportują o zużyciu zasobów i metrykach wykonywania zapytań. Te informacje stają się dostępne zaraz po rozpoczęciu korzystania z usługi, bez konieczności konfigurowania. Ta strona jest odświeżana co kilka minut. Jeśli podejmujesz decyzje dotyczące warstwy, [która ma być używana na potrzeby obciążeń produkcyjnych](search-sku-tier.md), lub czy należy [dostosować liczbę aktywnych replik i partycji](search-capacity-planning.md), te metryki mogą pomóc w podejmowaniu tych decyzji, pokazując, jak szybko zużywane są zasoby i jak również Bieżąca konfiguracja obsługuje istniejące obciążenie.

Karta **użycie** pokazuje dostępność zasobów względem bieżących [limitów](search-limits-quotas-capacity.md). Poniższa ilustracja dotyczy bezpłatnej usługi, która jest ograniczona do 3 obiektów każdego typu i 50 MB miejsca w magazynie. Usługa podstawowa lub standardowa ma wyższe limity i w przypadku zwiększenia liczby partycji Maksymalna ilość miejsca w magazynie jest proporcjonalna.

![Stan użycia względem limitów obowiązujących](./media/search-monitor-usage/usage-tab.png
 "Stan użycia względem limitów obowiązujących")

## <a name="queries-per-second-qps-and-other-metrics"></a>Zapytania na sekundę (zapytań) i inne metryki

Na karcie **monitorowanie** wyświetlane są średnie przenoszone wartości metryk, takich jak *zapytania wyszukiwania na sekundę* (zapytań), zagregowane na minutę. 
*Opóźnienie wyszukiwania* to ilość czasu wymagana przez usługę wyszukiwania do przetwarzania zapytań wyszukiwania, zagregowana na minutę. *Procent zapytań wyszukiwania z ograniczeniami* (niepokazywany) jest wartością procentową zapytań wyszukiwania, które zostały ograniczone, również zagregowanych na minutę.

Te liczby są przybliżone i mają na celu zapewnienie ogólnego pomysłu, jak dobrze system obsługuje żądania. Rzeczywista wartość zapytań może być większa lub mniejsza od liczby raportowanej w portalu.

![Działanie zapytań na sekundę](./media/search-monitor-usage/monitoring-tab.png "Działanie zapytań na sekundę")

## <a name="activity-logs"></a>Dzienniki aktywności

**Dziennik aktywności** zbiera informacje z Azure Resource Manager. Przykłady informacji znajdujących się w dzienniku aktywności obejmują tworzenie lub usuwanie usługi, aktualizowanie grupy zasobów, sprawdzanie dostępności nazwy lub Uzyskiwanie klucza dostępu do usługi w celu obsługi żądania. 

Możesz uzyskać dostęp do **dziennika aktywności** z okienka nawigacji po lewej stronie lub z powiadomień w górnym pasku poleceń okna lub na stronie **diagnozowanie i rozwiązywanie problemów** .

W przypadku zadań w usłudze, takich jak tworzenie indeksu lub usuwanie źródła danych, zobaczysz ogólne powiadomienia, takie jak "Pobierz klucz administratora" dla każdego żądania, ale nie konkretną akcję. W przypadku tego poziomu informacji należy włączyć rozwiązanie do monitorowania dodatków.

## <a name="add-on-monitoring-solutions"></a>Rozwiązania do monitorowania dodatków

Usługa Azure Wyszukiwanie poznawcze nie przechowuje żadnych danych poza obiektami, którymi zarządza, co oznacza, że dane dzienników mają być przechowywane zewnętrznie. Aby zachować dane dziennika, można skonfigurować dowolne z poniższych zasobów. 

W poniższej tabeli porównano opcje przechowywania dzienników i dodawania szczegółowego monitorowania operacji usługi i wykonywania zapytań do obciążeń za pomocą Application Insights.

| Zasób | Używana do |
|----------|----------|
| [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Zarejestrowane zdarzenia i metryki zapytań na podstawie schematów poniżej. Zdarzenia są rejestrowane w obszarze roboczym Log Analytics. Można uruchamiać zapytania w obszarze roboczym, aby zwracać szczegółowe informacje z dziennika. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Zarejestrowane zdarzenia i metryki zapytań na podstawie schematów poniżej. Zdarzenia są rejestrowane w kontenerze obiektów blob i przechowywane w plikach JSON. Aby wyświetlić zawartość plików, użyj edytora JSON.|
| [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) | Rejestrowane zdarzenia i metryki zapytań na podstawie schematów udokumentowanych w tym artykule. Wybierz tę opcję jako alternatywną usługę zbierania danych dla bardzo dużych dzienników. |

Zarówno dzienniki Azure Monitor, jak i magazyn obiektów BLOB są dostępne jako bezpłatna usługa, dzięki czemu można wypróbować ją bezpłatnie w okresie istnienia subskrypcji platformy Azure. Application Insights jest bezpłatny, aby zarejestrować się i używać tak długo, jak rozmiar danych aplikacji ma określone limity (szczegółowe informacje znajdują się na [stronie cennika](https://azure.microsoft.com/pricing/details/monitor/) ).

Następna sekcja przeprowadzi Cię przez kroki umożliwiające włączenie i użycie usługi Azure Blob Storage w celu zbierania i uzyskiwania dostępu do danych dziennika utworzonych przez operacje Wyszukiwanie poznawcze platformy Azure.

## <a name="enable-logging"></a>Włączanie rejestrowania

Rejestrowanie dla obciążeń indeksowania i zapytań jest domyślnie wyłączone i zależy od rozwiązań dodatków zarówno dla infrastruktury rejestrowania, jak i długoterminowego magazynu zewnętrznego. Same dane utrwalone na platformie Azure Wyszukiwanie poznawcze są tworzonymi i zarządzanymi obiektami, dlatego dzienniki muszą być przechowywane w innym miejscu.

W tej sekcji dowiesz się, jak używać magazynu obiektów BLOB do przechowywania zarejestrowanych zdarzeń i danych metryk.

1. [Utwórz konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , jeśli jeszcze go nie masz. Można ją umieścić w tej samej grupie zasobów co usługa Azure Wyszukiwanie poznawcze, aby uprościć czyszczenie później, jeśli chcesz usunąć wszystkie zasoby używane w tym ćwiczeniu.

   Konto magazynu musi znajdować się w tym samym regionie co usługa Azure Wyszukiwanie poznawcze.

2. Otwórz stronę omówienia usługi wyszukiwania. W okienku nawigacji po lewej stronie przewiń w dół do obszarze **monitorowanie** i kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](./media/search-monitor-usage/diagnostic-settings.png "Ustawienia diagnostyczne")

3. Wybierz pozycję **Dodaj ustawienie diagnostyczne**

4. Wybierz dane, którą chcesz wyeksportować: dzienniki, metryki lub obu. Możesz skopiować go do konta magazynu, wysłać do centrum zdarzeń lub wyeksportować do Azure Monitor dzienników.

   W przypadku archiwizowania do usługi BLOB Storage musi istnieć tylko konto magazynu. Kontenery i obiekty blob zostaną utworzone w miarę konieczności podczas eksportowania danych dziennika.

   ![Konfigurowanie archiwum magazynu obiektów BLOB](./media/search-monitor-usage/configure-blob-storage-archive.png "Konfigurowanie archiwum magazynu obiektów BLOB")

5. Zapisz profil

6. Rejestrowanie testowe przez tworzenie lub usuwanie obiektów (tworzy zdarzenia dziennika) i przesyłając zapytania (generuje metryki). 

Rejestrowanie jest włączone po zapisaniu profilu. Kontenery są tworzone tylko w przypadku działania do rejestrowania lub mierzenia. Po skopiowaniu danych na konto magazynu dane są formatowane jako kod JSON i umieszczane w dwóch kontenerach:

* insights — dzienniki operationlogs: dzienników ruchu wyszukiwania
* insights — metryki pt1m: dla metryki

**Trwa godzinę, zanim kontenery pojawią się w magazynie obiektów BLOB. Dla każdego kontenera istnieje jeden obiekt BLOB o godzinie.**

Aby wyświetlić pliki, można użyć [Visual Studio Code](#download-and-open-in-visual-studio-code) lub innego edytora JSON. 

### <a name="example-path"></a>Ścieżka Przykładowa

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schemat dziennika
Obiekty blob zawierające dzienniki ruchu usługi wyszukiwania są uporządkowane zgodnie z opisem w tej sekcji. Każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika. Każdy obiekt BLOB zawiera rekordy dla wszystkich operacji, które miały miejsce w danej godzinie.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/> FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Twoje ResourceId |
| operationName |ciąg |"Query.Search" |Nazwa operacji |
| operationVersion |ciąg |"2019-05-06" |Używana wersja interfejsu api |
| category |ciąg |"OperationLogs" |— stała |
| resultType |ciąg |Komunikat "success" |Możliwe wartości: powodzenie lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku protokołu HTTP |
| durationMS |int |50 |Czas trwania działania (w milisekundach) |
| właściwości |obiekt |Zobacz poniższą tabelę |Obiekt zawierający dane specyficzne dla operacji |

**Schemat właściwości**

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Opis |ciąg |"Pobierz /indexes('content')/docs" |Operacja punktu końcowego |
| Zapytanie |ciąg |"? Search = AzureSearch & $count = true & API-Version = 2019-05-06" |Parametry zapytania |
| Dokumenty |int |42 |Liczba przetworzonych dokumentów |
| indexName |ciąg |"testindex" |Nazwa indeksu skojarzone z operacją |

## <a name="metrics-schema"></a>Schemat metryki

Metryki są przechwytywane dla żądań zapytań.

| Name (Nazwa) | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>DOSTAWCÓW/DOMYŚLNIE/RESOURCEGROUPS /<br/>FIRMY MICROSOFT. WYSZUKIWANIE/SEARCHSERVICES/SEARCHSERVICE" |Identyfikator zasobu |
| metricName |ciąg |"Opóźnienie" |Nazwa metryki |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Sygnatura czasowa operacji |
| średnia |int |64 |Średnia wartość próbek pierwotnych w odstępie czasu metryki |
| minimalnie |int |37 |Minimalna wartość nieprzetworzoną próbek w odstępie czasu metryki |
| maksymalnie |int |78 |Wartość maksymalna pierwotne próbek w odstępie czasu metryki |
| łącznie |int |258 |Łączna wartość pierwotne próbek w odstępie czasu metryki |
| {1&gt;count&lt;1} |int |4 |Liczba próbek raw, używany do generowania metrykę |
| ziarna czasu |ciąg |"PT1M" |Ziarno czasu metryką przy podejmowaniu ISO 8601 |

Wszystkie metryki są raportowane w odstępach jednej minuty. Każdy pomiar przedstawia wartości minimalna, maksymalna i średnia na minutę.

Metryki SearchQueriesPerSecond minimalna jest najniższa wartość dla zapytania wyszukiwania na sekundę, który został zarejestrowany w ciągu tej minuty. To samo dotyczy maksymalnej wartości. Średnia, to agregacji przez całą minutę.
Pomyśl o tym scenariuszu podczas jednej minuty: sekundy wysoki załadować oznacza to maksymalną SearchQueriesPerSecond, następuje 58 sekund średnie obciążenie i na koniec co sekundę przy użyciu tylko jednego zapytania, który jest minimalnym warunkiem.

Aby uzyskać ThrottledSearchQueriesPercentage, minimalna, maksymalna, średnia i total, wszystkie mają taką samą wartość: procent zapytań wyszukiwania, które zostały ograniczone z łączna liczba zapytań wyszukiwania w ciągu jednej minuty.

## <a name="download-and-open-in-visual-studio-code"></a>Pobierz i Otwórz w Visual Studio Code

Aby wyświetlić plik dziennika, można użyć dowolnego edytora JSON. Jeśli go nie masz, zalecamy [Visual Studio Code](https://code.visualstudio.com/download).

1. W Azure Portal Otwórz konto magazynu. 

2. W okienku nawigacji po lewej stronie kliknij pozycję **obiekty blob**. Powinny być widoczne informacje **Insights-Logs-operationlogs** i **Insights-Metrics-pt1m**. Te kontenery są tworzone przez usługę Azure Wyszukiwanie poznawcze podczas eksportowania danych dziennika do magazynu obiektów BLOB.

3. Kliknij kolejno pozycje hierarchia folderów, aby uzyskać dostęp do pliku. JSON.  Użyj menu kontekstowego, aby pobrać plik.

Po pobraniu pliku Otwórz go w edytorze JSON, aby wyświetlić jego zawartość.

## <a name="use-system-apis"></a>Korzystanie z interfejsów API systemu
Zarówno interfejs API REST platformy Azure Wyszukiwanie poznawcze, jak i zestaw .NET SDK zapewniają programistyczny dostęp do metryk usług, informacji o indeksie i indeksatorze oraz liczby dokumentów.

* [Pobierz statystyki usług](/rest/api/searchservice/get-service-statistics)
* [Pobierz statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Liczenie dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

Aby włączyć korzystanie z programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zapoznaj się z dokumentacją [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Następne kroki

[Zarządzaj usługą wyszukiwania w Microsoft Azure](search-manage.md) , aby uzyskać więcej informacji na temat administrowania usługą i [wydajności i optymalizacji](search-performance-optimization.md) pod kątem wskazówek dotyczących dostrajania.
