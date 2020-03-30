---
title: Uaktualnienie do usługi Azure Search SDK w wersji 1.1
titleSuffix: Azure Cognitive Search
description: Migruj kod do narzędzia Azure Search .NET SDK w wersji 1.1 ze starszych wersji interfejsu API. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792379"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Uaktualnienie do usługi Azure Search SDK w wersji 1.1

Jeśli używasz wersji 1.0.2-preview lub starszych [usługi Azure Search .NET SDK,](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 1.1.

Aby uzyskać bardziej ogólne wskazówki dotyczące sdk, w tym przykłady, zobacz [Jak używać usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po uaktualnieniu do wersji 1.1 lub jeśli używasz już wersji z wersji z wersji 1.1 i 2.0 włącznie z podglądem, należy uaktualnić do wersji 3. Aby uzyskać instrukcje, zobacz [uaktualnianie do narzędzia Azure Search .NET SDK w wersji 3.](search-dotnet-sdk-migration.md)
>

Najpierw zaktualizuj `Microsoft.Azure.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu nowych pakietów i ich zależności, odbuduj projekt.

Jeśli wcześniej używałeś wersji 1.0.0-preview, 1.0.1-preview lub 1.0.2-preview, kompilacja powinna zakończyć się pomyślnie i jesteś gotowy do pracy!

Jeśli wcześniej używałeś wersji 0.13.0-preview lub starszej, powinny zostać wyświetlene następujące błędy kompilacji:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Następnym krokiem jest naprawienie błędów kompilacji jeden po drugim. Większość będzie wymagać zmiany niektórych klas i nazw metod, które zostały zmienione w SDK. [Lista przełomowych zmian w wersji 1.1](#ListOfChangesV1) zawiera listę tych zmian nazw.

Jeśli używasz klas niestandardowych do modelowania dokumentów, a te klasy mają właściwości typów pierwotnych niepodważalnych (na przykład `int` w `bool` języku C#), istnieje poprawka błędu w wersji 1.1 sdk, o której powinieneś wiedzieć. Zobacz [poprawki błędów w wersji 1.1,](#BugFixesV1) aby uzyskać więcej informacji.

Na koniec po usunięciu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji, jeśli chcesz.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista przełomowych zmian w wersji 1.1
Poniższa lista jest uporządkowana przez prawdopodobieństwo, że zmiana wpłynie na kod aplikacji.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch i IndexAction zmiany
`IndexBatch.Create`została zmieniona `IndexBatch.New` na argument i `params` nie ma już argumentu. Można użyć `IndexBatch.New` dla partii, które mieszają różne typy akcji (scala, usuwa itp.). Ponadto istnieją nowe metody statyczne do tworzenia partii, w których `Delete`wszystkie `Merge` `MergeOrUpload`akcje `Upload`są takie same: , , , i .

`IndexAction`nie ma już konstruktorów publicznych, a jego właściwości są teraz niezmienne. Nowe metody statyczne należy używać do tworzenia akcji `Delete`do `Merge` `MergeOrUpload`różnych `Upload`celów: , , i . `IndexAction.Create`został usunięty. Jeśli użyto przeciążenia, które zajmuje tylko `Upload` dokument, upewnij się, że zamiast tego.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Jeśli chcesz, możesz jeszcze bardziej uprościć to do tego:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException zmiany
Nazwa `IndexBatchException.IndexResponse` obiektu została zmieniona na `IndexingResults`, `IList<IndexingResult>`a jej typ jest teraz .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Zmiany metody działania
Każda operacja w zestawie Azure Search .NET SDK jest widoczna jako zestaw przeciążeń metody dla wywołań synchronicznych i asynchronicznych. Podpisy i faktoring tych przeciążeń metody uległy zmianie w wersji 1.1.

Na przykład operacja "Pobierz statystyki indeksu" w starszych wersjach SDK ujawniła te podpisy:

W pliku `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

W pliku `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Podpisy metody dla tej samej operacji w wersji 1.1 wyglądają następująco:

W pliku `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

W pliku `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Począwszy od wersji 1.1, zestaw SDK usługi Azure Search .NET organizuje metody obsługi w inny sposób:

* Parametry opcjonalne są teraz modelowane jako parametry domyślne, a nie dodatkowe przeciążenia metody. Zmniejsza to liczbę przeciążeń metody, czasami znacznie.
* Metody rozszerzenia teraz ukryć wiele obcych szczegółów HTTP z wywołującego. Na przykład starsze wersje SDK zwrócił obiekt odpowiedzi z kodem stanu HTTP, który często nie `CloudException` trzeba sprawdzać, ponieważ metody działania zgłosić dla dowolnego kodu stanu, który wskazuje błąd. Nowe metody rozszerzenia po prostu zwracają obiekty modelu, oszczędzając kłopoty z konieczności rozpakować je w kodzie.
* Z drugiej strony interfejsy podstawowe teraz uwidaczniają metody, które zapewniają większą kontrolę na poziomie HTTP, jeśli jest to potrzebne. Teraz można przekazać w niestandardowych nagłówków HTTP, które `AzureOperationResponse<T>` mają być uwzględnione w `HttpRequestMessage` żądaniach, a nowy typ zwracania zapewnia bezpośredni dostęp do i `HttpResponseMessage` dla operacji. `AzureOperationResponse`jest zdefiniowany `Microsoft.Rest.Azure` w obszarze `Hyak.Common.OperationResponse`nazw i zastępuje .

### <a name="scoringparameters-changes"></a>ScoringParameters zmiany
Nowa klasa `ScoringParameter` o nazwie została dodana w najnowszym SDK, aby ułatwić dostarczanie parametrów do oceniania profilów w kwerendzie wyszukiwania. Wcześniej `ScoringProfiles` właściwość `SearchParameters` klasy została wpisana `IList<string>`jako ; Teraz jest wpisany `IList<ScoringParameter>`jako .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Można go zmienić w ten sposób, aby naprawić błędy kompilacji: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Zmiany klasy modelu
Ze względu na zmiany podpisu opisane w operation `Microsoft.Azure.Search.Models` zmiany [metody,](#OperationMethodChanges)wiele klas w obszarze nazw zostały zmienione lub usunięte. Przykład:

* `IndexDefinitionResponse`została zastąpiona przez`AzureOperationResponse<Index>`
* Zmieniono nazwę polecenia `DocumentSearchResponse` na `DocumentSearchResult`
* Zmieniono nazwę polecenia `IndexResult` na `IndexingResult`
* `Documents.Count()`teraz zwraca `long` z liczbą dokumentów zamiast`DocumentCountResponse`
* Zmieniono nazwę polecenia `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Zmieniono nazwę polecenia `IndexListResponse` na `IndexListResult`

Podsumowując, -derived klasy, `OperationResponse`które istniały tylko do zawijania obiektu modelu zostały usunięte. Pozostałe klasy zmieniły sufiks `Response` z `Result`na .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Klasy odpowiedzi i IEnumerable
Dodatkową zmianą, która może mieć wpływ na kod jest `IEnumerable<T>`to, że klasy odpowiedzi, które przechowują kolekcje nie są już implementowane. Zamiast tego można uzyskać dostęp do właściwości kolekcji bezpośrednio. Na przykład, jeśli kod wygląda następująco:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Szczególny przypadek dla aplikacji internetowych
Jeśli masz aplikację sieci web, `DocumentSearchResponse` która serializuje bezpośrednio do wysyłania wyników wyszukiwania do przeglądarki, należy zmienić kod lub wyniki nie będą poprawnie serializować. Na przykład, jeśli kod wygląda następująco:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Można go zmienić, `.Results` uzyskując właściwość odpowiedzi wyszukiwania, aby naprawić renderowanie wyników wyszukiwania:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Będziesz musiał szukać takich przypadków w kodzie samodzielnie; **Kompilator nie ostrzega,** ponieważ `JsonResult.Data` `object`jest typu .

### <a name="cloudexception-changes"></a>Zmiany w cloudexception
Klasa `CloudException` została przeniesiona `Hyak.Common` z obszaru `Microsoft.Rest.Azure` nazw do obszaru nazw. Ponadto jego `Error` właściwość została `Body`zmieniona na .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient i SearchIndexClient zmiany
Typ `Credentials` właściwości został zmieniony `SearchCredentials` z klasy podstawowej, `ServiceClientCredentials`. Goście, którzy chcą `SearchCredentials` uzyskać `SearchIndexClient` `SearchServiceClient`dostęp do obiektu `SearchCredentials` lub , proszeni są o skorzystanie z nowej nieruchomości.

W starszych wersjach SDK i `SearchServiceClient` `SearchIndexClient` miał `HttpClient` konstruktorów, które miały parametr. Zostały one zastąpione konstruktorów, które zajmują `HttpClientHandler` i tablicy `DelegatingHandler` obiektów. Ułatwia to instalowanie niestandardowych programów obsługi do wstępnego przetwarzania żądań HTTP, jeśli to konieczne.

Wreszcie konstruktorów, `Uri` które `SearchCredentials` miały i zmieniły. Na przykład, jeśli masz kod, który wygląda następująco:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Należy również zauważyć, że typ parametru `ServiceClientCredentials`poświadczeń został zmieniony na . Jest mało prawdopodobne, aby `SearchCredentials` miało to `ServiceClientCredentials`wpływ na kod, ponieważ pochodzi od .

### <a name="passing-a-request-id"></a>Przekazywanie identyfikatora żądania
W starszych wersjach zestawu SDK można ustawić `SearchServiceClient` identyfikator `SearchIndexClient` żądania na lub i będzie uwzględniony w każdym żądaniu do interfejsu API REST. Jest to przydatne w przypadku rozwiązywania problemów z usługą wyszukiwania, jeśli musisz skontaktować się z pomocą techniczną. Jednak jest bardziej przydatne, aby ustawić unikatowy identyfikator żądania dla każdej operacji, a nie używać tego samego identyfikatora dla wszystkich operacji. Z tego powodu `SetClientRequestId` metody `SearchServiceClient` `SearchIndexClient` i zostały usunięte. Zamiast tego można przekazać identyfikator żądania do każdej metody `SearchRequestOptions` operacji za pomocą parametru opcjonalnego.

> [!NOTE]
> W przyszłej wersji zestawu SDK dodamy nowy mechanizm ustawiania identyfikatora żądania globalnie na obiektach klienta, który jest zgodny z podejściem używanym przez inne zestawy SDK platformy Azure.
> 
> 

### <a name="example"></a>Przykład
Jeśli masz kod, który wygląda następująco:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Zmiany nazwy interfejsu
Nazwy interfejsów grupy operacji zostały zmienione tak, aby były zgodne z odpowiadającymi im nazwami właściwości:

* Nazwa typu `ISearchServiceClient.Indexes` została zmieniona `IIndexOperations` `IIndexesOperations`na .
* Nazwa typu `ISearchServiceClient.Indexers` została zmieniona `IIndexerOperations` `IIndexersOperations`na .
* Nazwa typu `ISearchServiceClient.DataSources` została zmieniona `IDataSourceOperations` `IDataSourcesOperations`na .
* Nazwa typu `ISearchIndexClient.Documents` została zmieniona `IDocumentOperations` `IDocumentsOperations`na .

Ta zmiana jest mało prawdopodobne, aby mieć wpływ na kod, chyba że utworzono kpiny z tych interfejsów do celów testowych.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Poprawki błędów w wersji 1.1
Wystąpił błąd w starszych wersjach narzędzia Azure Search .NET SDK związany z serializacją klas modelu niestandardowego. Błąd może wystąpić, jeśli utworzono klasę modelu niestandardowego z właściwością typu wartości nienastępulnej.

### <a name="steps-to-reproduce"></a>Kroki do odtworzenia
Utwórz klasę modelu niestandardowego z właściwością typu wartości nienastępulnej. Na przykład dodaj `UnitCount` właściwość `int` publiczną `int?`typu zamiast .

Jeśli indeksujesz dokument o wartości domyślnej tego typu (na przykład 0 for), `int`pole będzie miało wartość null w usłudze Azure Search. Jeśli następnie wyszukujesz ten `Search` dokument, `JsonSerializationException` połączenie zostanie wywołane `null` narzekaniem, że nie można go przekonwertować na `int`.

Ponadto filtry mogą nie działać zgodnie z oczekiwaniami, ponieważ null został zapisany do indeksu zamiast zamierzonej wartości.

### <a name="fix-details"></a>Napraw szczegóły
Naprawiliśmy ten problem w wersji 1.1 sdk. Teraz, jeśli masz klasę modelu tak:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

i ustawiono `IntValue` na 0, ta wartość jest teraz poprawnie serializowane jako 0 w przewodzie i przechowywane jako 0 w indeksie. Potknięcie okrągłe działa również zgodnie z oczekiwaniami.

Istnieje jeden potencjalny problem, o którym należy pamiętać za pomocą tego podejścia: Jeśli używasz typu modelu z właściwością niemożna anulować, musisz **zagwarantować,** że żadne dokumenty w indeksie nie zawierają wartości null dla odpowiedniego pola. Ani zestaw SDK ani interfejs API REST usługi Azure Search nie pomogą Ci to wymusić.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu nadal zaleca się użycie typów nullable w klasach modelu jako najlepsze rozwiązanie.

Aby uzyskać więcej informacji na temat tego błędu i poprawki, zobacz [ten problem na GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

