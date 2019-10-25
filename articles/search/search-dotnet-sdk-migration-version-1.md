---
title: Uaktualnianie do Azure Search .NET SDK w wersji 1,1
titleSuffix: Azure Cognitive Search
description: Migruj kod do Azure Search .NET SDK wersja 1,1 ze starszych wersji interfejsu API. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792379"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Uaktualnianie do Azure Search .NET SDK w wersji 1,1

Jeśli używasz wersji 1.0.2-Preview lub starszej części [zestawu SDK Azure Search .NET](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 1,1.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po uaktualnieniu do wersji 1,1 lub jeśli korzystasz już z wersji z zakresu od 1,1 do 2,0 — wersja zapoznawcza, należy przeprowadzić uaktualnienie do wersji 3. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search .NET SDK w wersji 3](search-dotnet-sdk-migration.md) .
>

Najpierw należy zaktualizować odwołanie NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając pozycję "Zarządzaj pakietami NuGet...". w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt.

Jeśli wcześniej używasz wersji 1.0.0-Preview, 1.0.1-Preview lub 1.0.2-Preview, kompilacja powinna zakończyć się powodzeniem i wszystko gotowe do użycia.

Jeśli wcześniej używasz wersji 0.13.0-Preview lub starszej, powinny być widoczne błędy kompilacji podobne do następujących:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Następnym krokiem jest poprawienie błędów kompilacji po jednym. Większość będzie wymagała zmiany nazw klas i metod, które zostały zmienione w zestawie SDK. [Lista istotnych zmian w wersji 1,1](#ListOfChangesV1) zawiera listę zmian nazw.

Jeśli używasz klas niestandardowych do modelowania dokumentów, a te klasy mają właściwości niedopuszczające wartości null typy pierwotne (na przykład `int` lub `bool` w C#), istnieje Poprawka błędu w wersji 1,1 zestawu SDK, którego należy wiedzieć. Aby uzyskać więcej informacji, zobacz [poprawki błędów w wersji 1,1](#BugFixesV1) .

Na koniec po usunięciu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista istotnych zmian w wersji 1,1
Poniższa lista jest uporządkowana według prawdopodobieństwa, że zmiana wpłynie na kod aplikacji.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch i IndexAction zmiany
Nazwa `IndexBatch.Create` została zmieniona na `IndexBatch.New` i nie ma już argumentu `params`. `IndexBatch.New` dla partii, które mieszają różne typy akcji (Scalanie, usuwanie itp.). Ponadto istnieją nowe metody statyczne do tworzenia partii, w których wszystkie akcje są takie same: `Delete`, `Merge`, `MergeOrUpload`i `Upload`.

`IndexAction` nie ma już konstruktorów publicznych i jego właściwości są teraz niezmienne. Należy używać nowych metod statycznych do tworzenia akcji do różnych celów: `Delete`, `Merge`, `MergeOrUpload`i `Upload`. `IndexAction.Create` został usunięty. Jeśli użyto przeciążenia, które pobiera tylko dokument, należy zamiast tego użyć `Upload`.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Można to zmienić w celu usunięcia błędów kompilacji:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Jeśli chcesz, możesz uprościć to:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException zmiany
Nazwa właściwości `IndexBatchException.IndexResponse` została zmieniona na `IndexingResults`, a jej typ jest teraz `IList<IndexingResult>`.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Można to zmienić w celu usunięcia błędów kompilacji:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Zmiany metody operacji
Każda operacja w Azure Search .NET SDK jest uwidaczniana jako zestaw przeciążeń metody dla wywołań synchronicznych i asynchronicznych. Sygnatury i ich fabryki zostały zmienione w wersji 1,1.

Na przykład operacja "Pobierz statystyki indeksu" we wcześniejszych wersjach zestawu SDK uwidacznia następujące sygnatury:

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

Sygnatury metod dla tej samej operacji w wersji 1,1 wyglądać następująco:

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

Począwszy od wersji 1,1, zestaw SDK Azure Search .NET organizuje różne metody operacji:

* Parametry opcjonalne są teraz modelowane jako parametry domyślne, a nie dodatkowe przeciążenia metod. Zmniejsza to liczbę przeciążeń metod, czasami znacząco.
* Metody rozszerzające teraz ukrywają wiele nadmiarowych szczegółów protokołu HTTP z obiektu wywołującego. Na przykład starsze wersje zestawu SDK zwróciły obiekt odpowiedzi z kodem stanu HTTP, którego często nie trzeba było sprawdzić, ponieważ metody operacji zgłaszają `CloudException` dla dowolnego kodu stanu, który wskazuje na błąd. Nowe metody rozszerzające zwracają obiekty modelu, co pozwala na ich odwinięcie w kodzie.
* Z drugiej strony interfejsy podstawowe teraz uwidaczniają metody, które zapewniają większą kontrolę na poziomie protokołu HTTP, jeśli jest to potrzebne. Teraz można przekazać niestandardowe nagłówki HTTP, aby uwzględnić je w żądaniach, a nowy typ zwracany `AzureOperationResponse<T>` zapewnia bezpośredni dostęp do `HttpRequestMessage` i `HttpResponseMessage` dla operacji. `AzureOperationResponse` jest zdefiniowany w przestrzeni nazw `Microsoft.Rest.Azure` i zastępuje `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>ScoringParameters zmiany
Dodano nową klasę o nazwie `ScoringParameter` w najnowszym zestawie SDK, aby ułatwić dostarczenie parametrów do profilów oceniania w zapytaniu wyszukiwania. Wcześniej Właściwość `ScoringProfiles` klasy `SearchParameters` została wpisana jako `IList<string>`; Teraz jest wpisana jako `IList<ScoringParameter>`.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Można to zmienić w celu usunięcia błędów kompilacji: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Zmiany klasy modelu
Ze względu na zmiany sygnatury opisane w [metodzie operacji zmiany](#OperationMethodChanges)nazwy wielu klas w przestrzeni nazw `Microsoft.Azure.Search.Models`. Na przykład:

* `IndexDefinitionResponse` został zastąpiony przez `AzureOperationResponse<Index>`
* Zmieniono nazwę polecenia `DocumentSearchResponse` na `DocumentSearchResult`
* Zmieniono nazwę polecenia `IndexResult` na `IndexingResult`
* `Documents.Count()` teraz zwraca `long` z liczbą dokumentów zamiast `DocumentCountResponse`
* Zmieniono nazwę polecenia `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Zmieniono nazwę polecenia `IndexListResponse` na `IndexListResult`

Aby podsumować `OperationResponse`klasy pochodne, które istniały tylko w przypadku, gdy obiekt modelu został usunięty. Sufiks pozostałych klas został zmieniony z `Response` na `Result`.

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

Można to zmienić w celu usunięcia błędów kompilacji:

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
Dodatkowa zmiana, która może mieć wpływ na Twój kod, to Klasa odpowiedzi, która przechowuje kolekcje, nie implementuje już `IEnumerable<T>`. Zamiast tego można uzyskać bezpośredni dostęp do właściwości kolekcji. Na przykład, jeśli kod wygląda następująco:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Można to zmienić w celu usunięcia błędów kompilacji:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Specjalny przypadek dla aplikacji sieci Web
Jeśli masz aplikację sieci Web, która deserializacji `DocumentSearchResponse` bezpośrednio do wysyłania wyników wyszukiwania do przeglądarki, musisz zmienić kod lub wyniki nie będą poprawnie serializowane. Na przykład, jeśli kod wygląda następująco:

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

Możesz to zmienić, pobierając Właściwość `.Results` odpowiedzi wyszukiwania, aby naprawić renderowanie wyników wyszukiwania:

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

Należy poszukać takich przypadków w kodzie, **Kompilator nie wyświetli ostrzeżenia,** ponieważ `JsonResult.Data` jest typu `object`.

### <a name="cloudexception-changes"></a>Zmiany w chmurzeexception
Klasa `CloudException` została przeniesiona z przestrzeni nazw `Hyak.Common` do przestrzeni nazw `Microsoft.Rest.Azure`. Ponadto zmieniono nazwę jego właściwości `Error` na `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient i SearchIndexClient zmiany
Typ właściwości `Credentials` został zmieniony z `SearchCredentials` na jego klasę bazową, `ServiceClientCredentials`. Jeśli musisz uzyskać dostęp do `SearchCredentials` `SearchIndexClient` lub `SearchServiceClient`, użyj nowej właściwości `SearchCredentials`.

W starszych wersjach zestawu SDK `SearchServiceClient` i `SearchIndexClient` miały konstruktory, które miały `HttpClient` parametr. Zostały one zastąpione konstruktorami, które pobierają `HttpClientHandler` i tablicę obiektów `DelegatingHandler`. Ułatwia to instalowanie niestandardowych obsługi żądań HTTP w razie potrzeby.

Na koniec konstruktory, które miały `Uri` i `SearchCredentials` zostały zmienione. Na przykład, jeśli masz kod, który wygląda następująco:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Można to zmienić w celu usunięcia błędów kompilacji:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Należy również zauważyć, że typ parametru poświadczenia został zmieniony na `ServiceClientCredentials`. Prawdopodobnie nie wpłynie to na kod, ponieważ `SearchCredentials` pochodzi od `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Przekazywanie identyfikatora żądania
W starszych wersjach zestawu SDK można ustawić identyfikator żądania na `SearchServiceClient` lub `SearchIndexClient` i będzie on uwzględniany w każdym żądaniu do interfejsu API REST. Jest to przydatne w przypadku rozwiązywania problemów z usługą wyszukiwania, jeśli musisz skontaktować się z pomocą techniczną. Jednak bardziej przydatne jest ustawienie unikatowego identyfikatora żądania dla każdej operacji, a nie użycie tego samego identyfikatora dla wszystkich operacji. Z tego powodu `SetClientRequestId` metody `SearchServiceClient` i `SearchIndexClient` zostały usunięte. Zamiast tego można przekazać identyfikator żądania do każdej metody operacji za pomocą opcjonalnego parametru `SearchRequestOptions`.

> [!NOTE]
> W przyszłej wersji zestawu SDK dodamy nowy mechanizm konfigurowania identyfikatora żądania globalnie dla obiektów klienta, które są spójne z podejściem używanym przez inne zestawy SDK platformy Azure.
> 
> 

### <a name="example"></a>Przykład
Jeśli masz kod, który wygląda następująco:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Można to zmienić w celu usunięcia błędów kompilacji:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Zmiany nazw interfejsów
Wszystkie nazwy interfejsów grupy operacji zostały zmienione tak, aby były spójne z odpowiadającymi im nazwami właściwości:

* Zmieniono nazwę typu `ISearchServiceClient.Indexes` z `IIndexOperations` na `IIndexesOperations`.
* Zmieniono nazwę typu `ISearchServiceClient.Indexers` z `IIndexerOperations` na `IIndexersOperations`.
* Zmieniono nazwę typu `ISearchServiceClient.DataSources` z `IDataSourceOperations` na `IDataSourcesOperations`.
* Zmieniono nazwę typu `ISearchIndexClient.Documents` z `IDocumentOperations` na `IDocumentsOperations`.

Ta zmiana nie wpłynie na kod, chyba że nie zostały utworzone żadne imitacje tych interfejsów do celów testowych.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Poprawki błędów w wersji 1,1
Wystąpił błąd we wcześniejszych wersjach Azure Search .NET SDK odnoszących się do serializacji niestandardowych klas modelu. Usterka może wystąpić, jeśli utworzono niestandardową klasę modelu z właściwością typu wartości niedopuszczające wartości null.

### <a name="steps-to-reproduce"></a>Kroki do odtworzenia
Utwórz niestandardową klasę modelu z właściwością typu wartości, która nie dopuszcza wartości null. Na przykład Dodaj publiczną właściwość `UnitCount` typu `int` zamiast `int?`.

Jeśli indeksowanie dokumentu ma wartość domyślną tego typu (na przykład 0 dla `int`), pole będzie miało wartość null w Azure Search. W przypadku późniejszego wyszukania tego dokumentu wywołanie `Search` zgłosi `JsonSerializationException` skarżący, że nie można skonwertować `null` na `int`.

Ponadto filtry mogą nie zadziałały zgodnie z oczekiwaniami, ponieważ w indeksie zamiast zamierzonej wartości Zapisano wartość null.

### <a name="fix-details"></a>Popraw szczegóły
Ten problem został rozwiązany w wersji 1,1 zestawu SDK. Teraz, jeśli masz klasę modelu podobną do tej:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

ustawienie wartości `IntValue` na 0 spowoduje, że ta wartość jest teraz prawidłowo serializowana jako 0 w locie i przechowywana jako 0 w indeksie. Rounding działa również zgodnie z oczekiwaniami.

Istnieje jeden potencjalny problem, który należy wiedzieć o tym podejściu: Jeśli używasz typu modelu z właściwością, która nie dopuszcza wartości null, musisz **zagwarantować** , że żadne dokumenty w indeksie nie zawierają wartości null dla odpowiedniego pola. Nie można wymusić tego zestawu SDK ani interfejsu API REST Azure Search.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu nadal zalecamy używanie typów dopuszczających wartości null w klasach modelu jako najlepsze rozwiązanie.

Aby uzyskać więcej informacji na temat tego błędu i poprawki, zobacz [ten problem w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

