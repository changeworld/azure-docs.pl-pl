---
title: Uaktualnienie do zestawu .NET SDK w wersji 1.1 Azure Search | Dokumentacja firmy Microsoft
description: Uaktualnianie do usługi Azure Search .NET SDK wersji 1.1
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: ccefd21e2aa89a2b46129956b3c4417d548cbf32
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796748"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Uaktualnianie do usługi Azure Search .NET SDK wersji 1.1

Jeśli używasz 1.0.2-preview wersji lub starszej programu [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnienie aplikacji w wersji 1.1.

Aby uzyskać bardziej ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [sposobu korzystania z usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po uaktualnieniu do wersji 1.1 lub jeśli już używasz wersji między 1.1 i 2.0 — wersja zapoznawcza włącznie, należy uaktualnić system do wersji 3. Zobacz [uaktualnienie do usługi Azure Search .NET SDK w wersji 3](search-dotnet-sdk-migration.md) instrukcje.
>

Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety oraz ich zależności, ponownie skompiluj projekt.

Jeśli wcześniej zostały przy użyciu wersji 1.0.0-preview, 1.0.1-preview lub 1.0.2-preview, kompilacja ma być pomyślnie wykonane i wszystko jest gotowe!

Jeśli zostały wcześniej przy użyciu wersji 0.13.0-preview lub starsze, powinny pojawić błędy podobnie do następującej kompilacji:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Następnym krokiem jest naprawić błędy kompilacji jeden po drugim. Większość wymaga zmiana niektóre nazwy klasy i metody, których nazwy zostały zmienione w zestawie SDK. [Lista fundamentalne zmiany w wersji 1.1](#ListOfChangesV1) zawiera listę tych zmian nazw.

Jeśli używasz niestandardowej klasy umożliwia modelowanie dokumentów, a te klasy mają właściwości typów pierwotnych wartości null (na przykład `int` lub `bool` w języku C#), jest w wersji 1.1 zestawu SDK, które należy zwrócić uwagę poprawkę. Zobacz [poprawki błędów w wersji 1.1](#BugFixesV1) więcej szczegółów.

Na koniec po problem został rozwiązany błędy kompilacji, można wprowadzić zmiany do aplikacji, aby móc korzystać z nowych funkcji, jeśli chcesz.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Lista fundamentalne zmiany w wersji 1.1
Poniższa lista jest określona przez prawdopodobieństwo, że zmiany będzie miało wpływ na kod aplikacji.

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch i IndexAction zmian
`IndexBatch.Create` została zmieniona na `IndexBatch.New` i nie ma już `params` argumentu. Można użyć `IndexBatch.New` partii, które mieszać różnego rodzaju akcje (scalenia, usuwa itp.). Ponadto istnieją nowe metody statyczne do tworzenia instancji gdzie wszystkie akcje są takie same: `Delete`, `Merge`, `MergeOrUpload`, i `Upload`.

`IndexAction` nie ma konstruktorów publicznych i jego właściwości są niezmienne. Należy używać nowej metody statyczne do tworzenia akcje do różnych celów: `Delete`, `Merge`, `MergeOrUpload`, i `Upload`. `IndexAction.Create` została usunięta. Jeśli używane jest przeciążenie, które przyjmuje tylko dokumenty, upewnij się użyć `Upload` zamiast tego.

##### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Jeśli chcesz, możesz dodatkowo upraszcza go do tego:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException zmiany
`IndexBatchException.IndexResponse` Właściwości została zmieniona na `IndexingResults`, a jej typ jest teraz `IList<IndexingResult>`.

##### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>Operacja zmiany — metoda
Każdej operacji w zestawu .NET SDK usługi Azure Search jest ujawniona jako zbiór przeciążenia metody dla wywoływania synchroniczne i asynchroniczne. Podpisy i factoring z tych przeciążenia metody została zmieniona w wersji 1.1.

Na przykład "Uzyskać statystyki indeksu" operacji w starszej wersji zestawu SDK widoczne te podpisy:

W `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

W `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Podpisy metod do tej samej operacji w wersji 1.1 wyglądać następująco:

W `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

W `IndexesOperationsExtensions`:

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

Począwszy od wersji 1.1, zestawu .NET SDK usługi Azure Search porządkuje metody operacji inaczej:

* Parametry opcjonalne są teraz modelowane jako domyślne parametry raczej niż przeciążenia metody dodatkowe. Czasami znacznie zmniejsza liczbę przeciążenia metody.
* Metody rozszerzenia Ukryj teraz dużo nadmiarowe szczegóły HTTP wywołujący. Na przykład starsze wersje zestawu SDK zwrócił obiekt odpowiedzi z kodem stanu HTTP, który często nie trzeba sprawdzić, ponieważ metody operacji throw `CloudException` dla dowolnego kodu stanu wskazujący błąd. Nowych metod rozszerzenia zwracać tylko obiekty modelu, co pozwala zaoszczędzić pracy związanej z konieczności dekodowania je w kodzie.
* Z drugiej strony podstawowe interfejsów teraz Uwidacznianie metod, które zapewniają większą kontrolę na poziomie protokołu HTTP, jeśli zajdzie taka potrzeba. Teraz można przekazać niestandardowe nagłówki HTTP do uwzględnienia w żądań i nowych `AzureOperationResponse<T>` zwracany typ zapewnia bezpośredni dostęp do `HttpRequestMessage` i `HttpResponseMessage` dla tej operacji. `AzureOperationResponse` jest zdefiniowany w `Microsoft.Rest.Azure` przestrzeni nazw i zastępuje `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>ScoringParameters zmiany
Nową klasę o nazwie `ScoringParameter` został dodany w najnowszej wersji zestawu SDK, aby ułatwić dostarcza parametry do oceniania profile w zapytaniu wyszukiwania. Wcześniej `ScoringProfiles` właściwość `SearchParameters` klasy została wpisana jako `IList<string>`; Teraz jest wpisana jako `IList<ScoringParameter>`.

##### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Aby to naprawić wszystkie błędy kompilacji, można zmienić go: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Zmiany modelu klasy
Z powodu zmian podpisu opisanego w [operację zmiany metody](#OperationMethodChanges), wiele klas w `Microsoft.Azure.Search.Models` przestrzeni nazw został przeniesiony lub usunięty. Na przykład:

* `IndexDefinitionResponse` został zastąpiony `AzureOperationResponse<Index>`
* Zmieniono nazwę polecenia `DocumentSearchResponse` na `DocumentSearchResult`
* Zmieniono nazwę polecenia `IndexResult` na `IndexingResult`
* `Documents.Count()` obecnie zwraca `long` z licznikiem dokumentu, zamiast `DocumentCountResponse`
* Zmieniono nazwę polecenia `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Zmieniono nazwę polecenia `IndexListResponse` na `IndexListResult`

Podsumowując, `OperationResponse`-pochodzi z klasy, które były dostępne tylko do opakowywania usunięto obiekt modelu. Pozostałe klasy miały ich sufiks zmieniła się z `Response` do `Result`.

##### <a name="example"></a>Przykład
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

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

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

##### <a name="response-classes-and-ienumerable"></a>Klasy odpowiedzi i IEnumerable
Dodatkowe zmiany, które mogą mieć wpływ na kod jest klasy odpowiedzi, zawierających kolekcje nie implementuje `IEnumerable<T>`. Zamiast tego można bezpośrednio dostępu właściwości kolekcji. Na przykład, jeśli kod wygląda następująco:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Szczególnych przypadkach dla aplikacji sieci web
Jeśli masz aplikację sieci web, który serializuje `DocumentSearchResponse` bezpośrednio do wyników wyszukiwania wysyłany do przeglądarki, należy zmienić swój kod lub wyniki nie zostaną prawidłowo serializować. Na przykład, jeśli kod wygląda następująco:

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

Można ją zmienić, pobierając `.Results` właściwości odpowiedzi wyszukiwania, aby rozwiązać renderowanie wyniku wyszukiwania:

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

Konieczne będzie szukać takiej sytuacji w kodzie **Kompilator nie ostrzega** ponieważ `JsonResult.Data` jest typu `object`.

#### <a name="cloudexception-changes"></a>CloudException zmiany
`CloudException` Klasy została przeniesiona z `Hyak.Common` przestrzeni nazw do `Microsoft.Rest.Azure` przestrzeni nazw. Ponadto jego `Error` właściwości została zmieniona na `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Zmiany SearchServiceClient i SearchIndexClient
Typ `Credentials` właściwość zostanie zmieniona z `SearchCredentials` do swojej klasy podstawowej `ServiceClientCredentials`. Jeśli chcesz uzyskać dostęp do `SearchCredentials` z `SearchIndexClient` lub `SearchServiceClient`, użyj nowej `SearchCredentials` właściwości.

W starszych wersjach zestawu SDK `SearchServiceClient` i `SearchIndexClient` ma konstruktorów, które miały `HttpClient` parametru. Te zostały zastąpione konstruktorów przyjmujących `HttpClientHandler` i tablica `DelegatingHandler` obiektów. Ułatwia to zainstalować niestandardowe programy obsługi wstępnie przetworzyć żądania HTTP, jeśli to konieczne.

Na koniec konstruktorów, które miały `Uri` i `SearchCredentials` zostały zmienione. Na przykład, jeśli kod, który wygląda następująco:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Również pamiętać, że typ parametru poświadczeń został zmieniony na `ServiceClientCredentials`. Jest to prawdopodobnie nie będzie mieć wpływ na kod od `SearchCredentials` jest pochodną `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Przekazywanie identyfikator żądania
Identyfikator żądania w starszej wersji zestawu SDK, można ustawić na `SearchServiceClient` lub `SearchIndexClient` i może być uwzględniony w każdym żądaniu dla interfejsu API REST. Jest to przydatne podczas rozwiązywania problemów z usługą wyszukiwania, aby się z pomocą techniczną. Jednak jest bardziej użyteczna, aby określić identyfikator unikatowy żądania, dla każdej operacji, a nie do używania tego samego Identyfikatora dla wszystkich operacji. Z tego powodu `SetClientRequestId` metody `SearchServiceClient` i `SearchIndexClient` zostały usunięte. Zamiast tego można przekazać identyfikator żądania do każdej metody operacji za pomocą opcjonalnego `SearchRequestOptions` parametru.

> [!NOTE]
> W przyszłej wersji zestawu SDK zostanie dodany nowy mechanizm do ustawiania Identyfikatora żądania globalnie na kliencie obiektów zgodny z podejścia używane przez innych zestawów SDK usługi Azure.
> 
> 

#### <a name="example"></a>Przykład
Jeśli masz kod, który wygląda następująco:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Zmiany nazwy interfejsu
Nazwy interfejsu grupy operacji zostały zmienione na zgodne z odpowiadającymi im nazwami właściwości:

* Typ `ISearchServiceClient.Indexes` została zmieniona z `IIndexOperations` do `IIndexesOperations`.
* Typ `ISearchServiceClient.Indexers` została zmieniona z `IIndexerOperations` do `IIndexersOperations`.
* Typ `ISearchServiceClient.DataSources` została zmieniona z `IDataSourceOperations` do `IDataSourcesOperations`.
* Typ `ISearchIndexClient.Documents` została zmieniona z `IDocumentOperations` do `IDocumentsOperations`.

Ta zmiana jest prawdopodobnie nie będzie mieć wpływ na kod, chyba że zostaną utworzone mocks te interfejsy do celów testowych.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Poprawki błędów w wersji 1.1
Wystąpił błąd w starszych wersjach programu Azure wyszukiwania zestawu .NET SDK związany z serializacją klas niestandardowych modelu. Ten błąd mógł wystąpić, jeśli utworzone klasy niestandardowej modelu z właściwością typ niedopuszczający wartości null.

#### <a name="steps-to-reproduce"></a>Kroki do odtworzenia
Utwórz klasę modelu niestandardowych z właściwością typ niedopuszczający wartości null. Na przykład, Dodaj publiczną `UnitCount` właściwości typu `int` zamiast `int?`.

Jeśli indeks dokument z wartością domyślną tego typu (na przykład 0 dla `int`), pole będzie równy null w usłudze Azure Search. Jeśli w wyniku tego Wyszukaj tego dokumentu, `Search` zgłosi wywołania `JsonSerializationException` strona skarżąca, który nie może przekonwertować `null` do `int`.

Ponadto filtry mogą nie działać zgodnie z oczekiwaniami, ponieważ wartości null został zapisany do indeksu, a nie wartość docelowa.

#### <a name="fix-details"></a>Usuń szczegóły
Ten problem został rozwiązany w wersji 1.1 zestawu SDK. Teraz, jeśli masz klasę modelu w następujący sposób:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

i ustawisz `IntValue` 0, tę wartość jest teraz prawidłowo zserializowanej 0 umieszczonego w i przechowywane jako 0 w indeksie. Również uruchomienie round działa zgodnie z oczekiwaniami.

Istnieje jeden potencjalny problem, należy pamiętać o wybranie tej metody oznacza: Jeśli używasz typu modelu z właściwością wartości null, jest **zagwarantować** że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Zestaw SDK ani interfejsu API REST usługi Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu Będziemy nadal zaleca się używanie typów dopuszczających wartości zerowe w klasach modeli najlepszym rozwiązaniem.

Aby uzyskać więcej informacji dotyczących tego błędu i poprawki, zobacz [ten problem w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

