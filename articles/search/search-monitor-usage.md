---
title: Monitorowanie operacji i aktywności
titleSuffix: Azure Cognitive Search
description: Włącz rejestrowanie, pobierz metryki działania kwerendy, użycie zasobów i inne dane systemowe z usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462330"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorowanie operacji i aktywności usługi Azure Cognitive Search

W tym artykule przedstawiono monitorowanie na poziomie usługi (zasobu), na poziomie obciążenia (zapytania i indeksowanie) i sugeruje platformę do monitorowania dostępu użytkowników.

W całym spektrum użyjesz kombinacji wbudowanej infrastruktury i usług podstawowych, takich jak usługa Azure Monitor, a także interfejsów API usług, które zwracają statystyki, liczby i stan. Zrozumienie zakresu możliwości może pomóc w skonstruowaniu pętli sprzężenia zwrotnego, dzięki czemu można rozwiązać problemy w miarę ich pojawiania się.

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Wiele usług, w tym usługa Azure Cognitive Search, korzysta z [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) w zakresie alertów, metryk i rejestrowania danych diagnostycznych. W przypadku usługi Azure Cognitive Search wbudowana infrastruktura monitorowania jest używana głównie do monitorowania na poziomie zasobów (kondycja usługi) i [monitorowania zapytań.](search-monitor-queries.md)

Poniższy zrzut ekranu ułatwia lokalizowanie funkcji usługi Azure Monitor w portalu.

+ Karta **Monitorowanie,** znajdująca się na głównej stronie przeglądu, pokazuje kluczowe metryki na pierwszy rzut oka.
+ **Dziennik aktywności**, tuż poniżej Przegląd, raporty dotyczące akcji na poziomie zasobów: kondycji usługi i powiadomień o kluczu interfejsu API.
+ **Monitorowanie**, w dalszej poniżej listy, zapewnia konfigurowalne alerty, metryki i dzienniki diagnostyczne. Utwórz je, gdy ich potrzebujesz. Po zebraniu i przechowywaniu danych można wysyłać zapytania lub wizualizować informacje w celu uzyskania szczegółowych informacji.

![Integracja z usługą Azure Monitor w usłudze wyszukiwania](./media/search-monitor-usage/azure-monitor-search.png
 "Integracja z usługą Azure Monitor w usłudze wyszukiwania")

### <a name="precision-of-reported-numbers"></a>Dokładność zgłoszonych liczb

Strony portalu są odświeżane co kilka minut. W związku z tym liczby zgłaszane w portalu są przybliżone, co ma na celu ogólne poczucie, jak dobrze system obsługuje żądania. Rzeczywiste metryki, takie jak zapytania na sekundę (QPS) mogą być wyższe lub niższe niż liczba wyświetlana na stronie.

## <a name="activity-logs-and-service-health"></a>Dzienniki aktywności i kondycja usługi

[**Dziennik aktywności**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) zbiera informacje z usługi Azure Resource Manager i raportuje zmiany kondycji usługi. Można monitorować dziennik aktywności pod kątem krytycznych, błędów i warunków ostrzegawczych związanych ze zdrowiem usługi.

W przypadku zadań w usłudze , takich jak kwerendy, indeksowanie lub tworzenie obiektów , zobaczysz ogólne powiadomienia informacyjne, takie jak *Pobierz klucz administratora* i *Pobierz klucze zapytania* dla każdego żądania, ale nie sama określona akcja. Aby uzyskać informacje o tym ziarnu, należy skonfigurować rejestrowanie diagnostyczne.

Dostęp do **dziennika aktywności** można uzyskać z lewego okienka nawigacji lub z obszaru powiadomień na pasku poleceń górnego okna lub ze strony **Diagnozowanie i rozwiązywanie problemów.**

## <a name="monitor-storage"></a>Monitorowanie pamięci masowej

Strony z kartami wbudowane w raport strony Przegląd na temat użycia zasobów. Te informacje stają się dostępne natychmiast po rozpoczęciu korzystania z usługi, bez konieczności konfiguracji, a strona jest odświeżana co kilka minut. 

Jeśli kończysz decyzje dotyczące [warstwy używanej dla obciążeń produkcyjnych](search-sku-tier.md)lub czy dostosować liczbę [aktywnych replik i partycji,](search-capacity-planning.md)te metryki mogą pomóc w podejmowaniu tych decyzji, pokazując, jak szybko zasoby są zużywane i jak dobrze bieżąca konfiguracja obsługuje istniejące obciążenie.

Alerty związane z magazynem nie są obecnie dostępne; zużycie magazynu nie jest agregowane ani rejestrowane w tabeli **AzureMetrics** w usłudze Azure Monitor. Należy utworzyć [niestandardowe rozwiązanie,](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) które emituje powiadomienia związane z zasobami, gdzie kod sprawdza rozmiar magazynu i obsługuje odpowiedzi. Aby uzyskać więcej informacji na temat metryk magazynu, zobacz [Get Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

W przypadku monitorowania wizualnego w portalu na karcie **Użycie** jest wyświetlana dostępność zasobów w stosunku do [bieżących limitów](search-limits-quotas-capacity.md) narzuconych przez warstwę usług. 

Poniższa ilustracja dotyczy bezpłatnej usługi, która jest ograniczona do 3 obiektów każdego typu i 50 MB pamięci masowej. Usługa Podstawowa lub Standardowa ma wyższe limity, a jeśli zwiększysz liczbę partycji, maksymalna liczba magazynowa wzrośnie proporcjonalnie.

![Stan użycia względem limitów warstw](./media/search-monitor-usage/usage-tab.png
 "Stan użycia względem limitów warstw")

## <a name="monitor-workloads"></a>Monitorowanie obciążeń

Zarejestrowane zdarzenia obejmują te związane z indeksowania i kwerend. **Tabela AzureDiagnostics** w usłudze Log Analytics zbiera dane operacyjne związane z zapytaniami i indeksowaniem.

Większość zarejestrowanych danych jest przeznaczonych dla operacji tylko do odczytu. W przypadku innych operacji create-update-delete, które nie zostały przechwycone w dzienniku, można zbadać usługę wyszukiwania w poszukiwaniu informacji o systemie.

| OperationName | Opis |
|---------------|-------------|
| ServiceStats (ServiceStats) | Ta operacja jest rutynowe wywołanie [Get Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), wywoływane bezpośrednio lub niejawnie do wypełniania strony przeglądu portalu, gdy jest ładowany lub odświeżany. |
| Query.Search (Wyszukiwanie). |  Żądania kwerend względem indeksu Zobacz [Zapytania monitoruj,](search-monitor-queries.md) aby uzyskać informacje o zarejestrowanych kwerendach.|
| Indeksowanie.Indeks  | Ta operacja jest wywołaniem [dodawania, aktualizowania lub usuwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| Indeksy. Prototyp | Jest to indeks utworzony przez Kreatora importu danych. |
| Indeksatory.Tworzenie | Utwórz indeksator jawnie lub niejawnie za pomocą Kreatora importu danych. |
| Indeksatory.Pobierz | Zwraca nazwę indeksatora za każdym razem, gdy jest uruchamiany indeksator. |
| Indeksatory.Status | Zwraca stan indeksatora za każdym razem, gdy indeksator jest uruchamiany. |
| DataSources.Get | Zwraca nazwę źródła danych za każdym razem, gdy jest uruchamiany indeksator.|
| Indexes.Get | Zwraca nazwę indeksu za każdym razem, gdy jest uruchamiany indeksator. |

### <a name="kusto-queries-about-workloads"></a>Zapytania Kusto dotyczące obciążeń

Jeśli włączono rejestrowanie, można zapytanie **AzureDiagnostics** dla listy operacji, które uruchomiono w usłudze i kiedy. Można również skorelować działania w celu zbadania zmian w wydajności.

#### <a name="example-list-operations"></a>Przykład: Operacje listy 

Zwraca listę operacji i liczbę każdego z nich.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Przykład: Skorelowanie operacji

Skoreluj żądanie kwerendy z operacjami indeksowania i renderuj punkty danych na wykresie czasu, aby zobaczyć operacje zbieżne.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Korzystanie z interfejsów API wyszukiwania

Interfejs API REST usługi Azure Cognitive Search i zestaw SDK platformy .NET zapewniają programowy dostęp do metryk usługi, informacji o indeksach i indeksatorach oraz liczby dokumentów.

+ [Statystyki usługi GET](/rest/api/searchservice/get-service-statistics)
+ [GET Index Statystyki](/rest/api/searchservice/get-index-statistics)
+ [POBIERZ liczniki dokumentów](/rest/api/searchservice/count-documents)
+ [GET Indexer Status](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorowanie dostępu użytkowników

Ponieważ indeksy wyszukiwania są składnikiem większej aplikacji klienckiej, nie istnieje wbudowana metodologia kontrolowania lub monitorowania dostępu na użytkownika do indeksu. Zakłada się, że żądania pochodzą z aplikacji klienckiej dla żądań administratora lub kwerendy. Operacje odczytu i zapisu administratora obejmują tworzenie, aktualizowanie, usuwanie obiektów w całej usłudze. Operacje tylko do odczytu są zapytania względem kolekcji dokumentów, o zasięgu do jednego indeksu. 

W związku z tym, co zobaczysz w dziennikach aktywności są odwołania do wywołań przy użyciu kluczy administracyjnych lub kluczy kwerendy. Odpowiedni klucz jest uwzględniony w żądaniach pochodzących z kodu klienta. Usługa nie jest przystosowana do obsługi tokenów tożsamości lub personifikacji.

Jeśli istnieją wymagania biznesowe dla autoryzacji dla użytkownika, zalecenie jest integracja z usługi Azure Active Directory. $filter i tożsamości użytkowników można używać do [przycinania wyników wyszukiwania](search-security-trimming-for-azure-search-with-aad.md) dokumentów, których użytkownik nie powinien widzieć. 

Nie ma możliwości rejestrowania tych informacji oddzielnie od ciągu zapytania, który zawiera parametr $filter. Zobacz [Monitorowanie kwerend, aby](search-monitor-queries.md) uzyskać szczegółowe informacje na temat raportowania ciągów zapytań.

## <a name="next-steps"></a>Następne kroki

Płynność usługi Azure Monitor jest niezbędna do nadzorowania dowolnej usługi platformy Azure, w tym zasobów, takich jak usługa Azure Cognitive Search. Jeśli nie jesteś zaznajomiony z usługi Azure Monitor, poświęć trochę czasu na przejrzenie artykułów związanych z zasobami. Oprócz samouczków, poniższy artykuł jest dobrym miejscem do rozpoczęcia.

> [!div class="nextstepaction"]
> [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
