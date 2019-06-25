---
title: Uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 1.1 — usługa Azure Search
description: Migrowanie kodu do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 1.1 ze starszych wersji interfejsu API. Poznaj nowe funkcje i zmiany kodu są wymagane.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 82823bae76521080634d4f7ff285d94ce8495fbf
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "65024165"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Uaktualnianie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 1.1

Jeśli używasz wersji 1.0.2-preview lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację do używania w wersji 1.1.

Aby uzyskać ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [jak używać usługi Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po uaktualnieniu do wersji 1.1 lub jeśli już korzystasz z wersji między 1.1 i 2.0 — w wersji zapoznawczej (włącznie), należy uaktualnić do wersji 3. Zobacz [uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 3](search-dotnet-sdk-migration.md) instrukcje.
>

Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety i ich zależności, należy ponownie skompilować projekt.

Jeśli była wcześniej używana wersja 1.0.0-preview, 1.0.1-preview lub 1.0.2-preview, kompilacji ma być pomyślnie wykonane i wszystko gotowe!

Jeśli wcześniej używano 0.13.0-preview wersji lub starsze, powinien zostać wyświetlony tworzenia błędów, takich jak następujące:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Następnym krokiem jest, aby naprawić błędy kompilacji jedno po drugim. Większość będzie wymagać, zmieniając niektóre nazwy klasy i metody, które zostały zmienione w zestawie SDK. [Listę istotnych zmian w wersji 1.1](#ListOfChangesV1) zawiera listę tych zmian nazw.

Jeśli za pomocą niestandardowej klasy modelu dokumentów, a te klasy posiadają właściwości niedopuszczającej typy pierwotne (na przykład `int` lub `bool` w C#), w wersji 1.1, zestawu SDK, które należy zwrócić uwagę jest naprawienie usterki. Zobacz [poprawki w wersji 1.1](#BugFixesV1) Aby uzyskać więcej informacji.

Na koniec gdy problem został rozwiązany błędów kompilacji, można wprowadzeniu zmian do aplikacji w taki sposób, aby móc korzystać z nowych funkcji, jeśli chcesz, aby.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Listę istotnych zmian w wersji 1.1
Poniższa lista są uporządkowane według prawdopodobieństwo, że zmiana wpłynie na kod aplikacji.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch i IndexAction zmian
`IndexBatch.Create` została zmieniona na `IndexBatch.New` i nie ma już `params` argumentu. Możesz użyć `IndexBatch.New` partii, które łączyć różnego rodzaju akcje (scaleń, usuwa itp.). Ponadto istnieją nowe metody statyczne do tworzenia instancji gdzie wszystkie akcje są takie same: `Delete`, `Merge`, `MergeOrUpload`, i `Upload`.

`IndexAction` nie ma już konstruktorów publicznych i jego właściwości, teraz są niezmienne. Należy używać nowej metody statyczne do tworzenia akcji do różnych celów: `Delete`, `Merge`, `MergeOrUpload`, i `Upload`. `IndexAction.Create` została usunięta. Jeśli używane jest przeciążenie, które przyjmuje tylko dokumenty, upewnij się, że użyj `Upload` zamiast tego.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Jeśli chcesz, możesz ją jeszcze bardziej uprościć do tego:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Zmiany IndexBatchException
`IndexBatchException.IndexResponse` Właściwości została zmieniona na `IndexingResults`, a jej typ jest teraz `IList<IndexingResult>`.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Zmiany metody operacji
Każda operacja w zestawu .NET SDK usługi Azure Search jest udostępniany jako zbiór przeciążenia metody dla obiektów wywołujących synchroniczne i asynchroniczne. Podpisy i uwzględniając z tych przeciążeń metody została zmieniona w wersji 1.1.

Na przykład operacji "Pobierz statystyki indeksu" w starszych wersjach zestawu SDK udostępniane tymi podpisami:

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

Podpisy metod dla tej samej operacji w wersji 1.1 wyglądać następująco:

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

Począwszy od wersji 1.1, zestawu .NET SDK usługi Azure Search porządkuje metod operacji inaczej:

* Następujące parametry opcjonalne teraz są modelowane jako domyślne parametry raczej niż dodatkową metodę przeciążenia. Czasami znacznie zmniejsza liczbę przeciążenia metody.
* Metody rozszerzające ukryć teraz mnóstwo nadmiarowe szczegóły HTTP od elementu wywołującego. Na przykład starsze wersje zestawu SDK zwrócony obiekt odpowiedzi z kodem stanu HTTP, który często nie trzeba sprawdzić, ponieważ operacja metod generują `CloudException` dla dowolnego kodu stanu, który wskazuje na błąd. Nowe metody rozszerzenia po prostu zwraca obiekty modelu, co pozwala wyeliminować wysiłek związany z konieczności Odkodowywanie je w kodzie.
* Z drugiej strony podstawowe interfejsy udostępniają teraz te metody, które zapewniają więcej kontroli na poziomie protokołu HTTP, gdy ich potrzebujesz. Możesz teraz przekazać niestandardowych nagłówków HTTP, które mają zostać uwzględnione w żądań i nowym `AzureOperationResponse<T>` zwracany typ zapewnia bezpośredni dostęp do `HttpRequestMessage` i `HttpResponseMessage` dla tej operacji. `AzureOperationResponse` jest zdefiniowany w `Microsoft.Rest.Azure` przestrzeni nazw i zastępuje `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>Zmiany ScoringParameters
Nową klasę o nazwie `ScoringParameter` został dodany w najnowszy zestaw SDK, aby ułatwić Podaj parametry dla profile oceniania w zapytaniu wyszukiwania. Wcześniej `ScoringProfiles` właściwość `SearchParameters` klasa została wpisana jako `IList<string>`; Teraz ma `IList<ScoringParameter>`.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Zmiany w modelu klas
Z powodu zmian podpisu opisanych w [zmiany metody operacji](#OperationMethodChanges), wiele klas w `Microsoft.Azure.Search.Models` przestrzeni nazw, został przeniesiony lub usunięty. Na przykład:

* `IndexDefinitionResponse` został zastąpiony przez `AzureOperationResponse<Index>`
* Zmieniono nazwę polecenia `DocumentSearchResponse` na `DocumentSearchResult`
* Zmieniono nazwę polecenia `IndexResult` na `IndexingResult`
* `Documents.Count()` Zwraca teraz `long` z liczbą dokumentów zamiast `DocumentCountResponse`
* Zmieniono nazwę polecenia `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Zmieniono nazwę polecenia `IndexListResponse` na `IndexListResult`

Aby podsumować, `OperationResponse`-pochodne klasy, które istniały tylko do opakowania obiekt modelu zostały usunięte. Pozostałe klasy mieli sufiksów zmieniła się z `Response` do `Result`.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

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

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

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

#### <a name="response-classes-and-ienumerable"></a>Klasy odpowiedzi i interfejsu IEnumerable
Dodatkowe zmiany, które może mieć wpływ na kod jest klasy odpowiedzi, używane do przechowywania kolekcji nie jest już zaimplementować `IEnumerable<T>`. Zamiast tego można bezpośredniego dostępu właściwości kolekcji. Na przykład, jeśli kod wygląda następująco:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Przypadek specjalny dla aplikacji sieci web
W przypadku aplikacji sieci web, który serializuje `DocumentSearchResponse` bezpośrednio wysyłać wyniki wyszukiwania do przeglądarki, konieczne będzie zmian w kodzie lub wyniki nie będą poprawnie serializacji. Na przykład, jeśli kod wygląda następująco:

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

Można ją zmienić, uzyskując `.Results` właściwości odpowiedzi wyszukiwania w celu naprawić renderowania wyników wyszukiwania:

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

Trzeba będzie szukać takiej sytuacji w kodzie **Kompilator nie ostrzega** ponieważ `JsonResult.Data` typu `object`.

### <a name="cloudexception-changes"></a>Zmiany CloudException
`CloudException` Klasa została przeniesiona z `Hyak.Common` przestrzeń nazw `Microsoft.Rest.Azure` przestrzeni nazw. Ponadto jego `Error` właściwości została zmieniona na `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Zmiany SearchServiceClient i SearchIndexClient
Typ `Credentials` właściwości została zmieniona z `SearchCredentials` z klasą bazową `ServiceClientCredentials`. Jeśli potrzebujesz dostępu do `SearchCredentials` z `SearchIndexClient` lub `SearchServiceClient`, użyj nowej `SearchCredentials` właściwości.

W starszych wersjach zestawu SDK `SearchServiceClient` i `SearchIndexClient` ma konstruktorów, które miały `HttpClient` parametru. Te zostały zastąpione konstruktorów, które przyjmują `HttpClientHandler` oraz tablicę `DelegatingHandler` obiektów. Ta funkcja ułatwia instalowanie niestandardowe programy obsługi wstępnie przetworzyć żądania HTTP, jeśli to konieczne.

Na koniec konstruktorów, które miały `Uri` i `SearchCredentials` uległy zmianie. Na przykład, jeśli masz kod, który wygląda w następujący sposób:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Należy także zauważyć, że typ parametru poświadczenia została zmieniona na `ServiceClientCredentials`. Jest to prawdopodobnie nie będzie mieć wpływ na kod od `SearchCredentials` jest tworzony na podstawie `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Identyfikator żądania
W starszych wersjach zestawu SDK, można ustawić identyfikator żądania na `SearchServiceClient` lub `SearchIndexClient` i zostałaby ona dołączona do każdego żądania interfejsu API REST. Ułatwia to Rozwiązywanie problemów z usługą wyszukiwania, jeśli chcesz się z pomocą techniczną. Jest jednak bardziej użyteczny, aby ustawić identyfikator unikatowy żądania, dla każdej operacji, zamiast używać tego samego Identyfikatora dla wszystkich operacji. Z tego powodu `SetClientRequestId` metody `SearchServiceClient` i `SearchIndexClient` zostały usunięte. Zamiast tego Przekaż identyfikator żądania do każdej metody operacji za pomocą opcjonalnego `SearchRequestOptions` parametru.

> [!NOTE]
> W przyszłej wersji zestawu SDK dodamy nowy mechanizm do ustawiania Identyfikatora żądania globalnie na kliencie obiektów, zgodny z podejścia, używany przez inne zestawy SDK platformy Azure.
> 
> 

### <a name="example"></a>Przykład
Jeśli masz kod, który wygląda w następujący sposób:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Zmiany nazwy interfejsu
Nazwy interfejsu grup operacji zostały zmienione aby były zgodne z odpowiadających im nazw właściwości:

* Typ `ISearchServiceClient.Indexes` została zmieniona z `IIndexOperations` do `IIndexesOperations`.
* Typ `ISearchServiceClient.Indexers` została zmieniona z `IIndexerOperations` do `IIndexersOperations`.
* Typ `ISearchServiceClient.DataSources` została zmieniona z `IDataSourceOperations` do `IDataSourcesOperations`.
* Typ `ISearchIndexClient.Documents` została zmieniona z `IDocumentOperations` do `IDocumentsOperations`.

Ta zmiana jest mało prawdopodobne, aby wpłynąć na kod, chyba że zostanie utworzona mocks te interfejsy do celów testowych.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Poprawki błędów w wersji 1.1
Wystąpił błąd w starszych wersjach programu Azure Search .NET SDK odnoszących się do serializacji w klasach modelu niestandardowego. Ten błąd mógł wystąpić, jeśli został utworzony z właściwością typem wartościowym niebędącym klasy modelu niestandardowego.

### <a name="steps-to-reproduce"></a>Kroki prowadzące do odtworzenia
Utwórz klasę modelu niestandardowego z właściwością typu wartości innych niż null. Na przykład dodać publiczny `UnitCount` właściwości typu `int` zamiast `int?`.

Jeśli indeks dokument z wartością domyślną tego typu (na przykład 0 dla `int`), pole jest wartość null w usłudze Azure Search. Jeśli później wyszukiwać tego dokumentu `Search` zgłosi wywołanie `JsonSerializationException` skarżących, który nie może przekonwertować `null` do `int`.

Ponadto filtry może nie działać zgodnie z oczekiwaniami, ponieważ o wartości null zostały zapisane w indeksie zamiast wartość zamierzona.

### <a name="fix-details"></a>Usuń szczegóły
Ten problem został rozwiązany w wersji 1.1 zestawu SDK. Teraz masz klasę modelu następująco:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

i ustawiasz `IntValue` 0, ta wartość jest teraz prawidłowo serializowana jako 0 przesyłania i przechowywane jako 0 w indeksie. Ponadto uruchomienie round działa zgodnie z oczekiwaniami.

Istnieje jeden potencjalny problem pod uwagę w przypadku tej metody: Jeśli używasz typu modelu z właściwością dopuszcza trzeba **gwarantuje** że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Zestaw SDK ani interfejsu API REST usługi Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem. Wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu nadal zalecane, najlepszym rozwiązaniem jest używanie typów dopuszczających wartości zerowe w klasach modeli.

Aby uzyskać szczegółowe informacje na temat tego błędu i poprawki, zobacz [ten problem w serwisie GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

