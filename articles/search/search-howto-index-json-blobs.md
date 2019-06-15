---
title: Indeksowanie obiektów blob JSON z indeksatora obiektów Blob platformy Azure w celu wyszukiwania pełnotekstowego — usługa Azure Search
description: Można przeszukiwać JSON usługi Azure blob dla zawartości tekstu za pomocą indeksatora obiektów Blob platformy Azure Search. Indeksatory zautomatyzować pozyskiwanie danych dla wybranych źródeł danych takich jak usługi Azure Blob storage.
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4f3fb624f5e6137c9edb0be97adc16d8c808ebd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523070"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Jak indeksować obiektów blob JSON za pomocą indeksatora obiektów Blob platformy Azure Search
W tym artykule dowiesz się, jak skonfigurować obiekt blob usługi Azure Search [indeksatora](search-indexer-overview.md) wyodrębniania zawartości ze strukturą z dokumenty JSON w usłudze Azure Blob storage i którą można przeszukiwać w usłudze Azure Search. Ten przepływ tworzy indeks usługi Azure Search i ładuje je za pomocą istniejący tekst wyodrębnione z obiektów blob JSON. 

Możesz użyć [portal](#json-indexer-portal), [interfejsów API REST](#json-indexer-rest), lub [zestawu .NET SDK](#json-indexer-dotnet) do indeksu zawartość JSON. Wspólne wszystkich metod jest, że dokumenty JSON znajdują się w kontenerze obiektów blob na koncie usługi Azure Storage. Aby uzyskać wskazówki dotyczące wypychania dokumentów JSON z innych platform spoza platformy Azure, zobacz [importowanie danych w usłudze Azure Search](search-what-is-data-import.md).

Obiektów blob JSON w usłudze Azure Blob storage są zazwyczaj pojedynczego dokumentu JSON lub kolekcję obiektów JSON. W przypadku kolekcji w formacie JSON, obiekt blob mogą być **tablicy** elementów JSON poprawnie sformułowany. Obiekty BLOB może również składać się z wielu pojedynczych jednostek JSON rozdzielone znakami nowego wiersza. Indeksowanie obiektów blob w usłudze Azure Search może przeanalizować takich konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

Wszystkie analizy tryby JSON (`json`, `jsonArray`, `jsonLines`) są teraz ogólnie dostępne. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami konfiguracji indeksatora w [indeksowania jeden do wielu](search-howto-index-one-to-many-blobs.md) służący do wypełniania wyjściowego wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostsza metoda dla indeksować dokumenty JSON jest użycie kreatora w [witryny Azure portal](https://portal.azure.com/). Analizując metadanych w kontenerze obiektów blob platformy Azure, [ **importowania danych** ](search-import-data-portal.md) kreatora można utworzyć indeks domyślny mapują pola źródłowe na pola indeksu docelowego, a ładowanie indeksu w ramach jednej operacji. W zależności od rozmiaru i stopnia złożoności danych źródła może mieć indeks wyszukiwania operacyjnej pełny tekst w ciągu kilku minut.

Zalecamy używanie tej samej subskrypcji platformy Azure dla usługi Azure Search i usługę Azure storage, najlepiej w tym samym regionie.

### <a name="1---prepare-source-data"></a>1 — Przygotowanie danych źródłowych

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/).

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawierają swoje dane. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

Konieczne będzie nazwa konta magazynu, nazwy kontenera i klucza dostępu, aby pobrać dane w **importowania danych** kreatora.

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Na stronie Przegląd usługi Azure Search, możesz [uruchom kreatora](search-import-data-portal.md) na pasku poleceń lub klikając **Dodaj usługę Azure Search** w **usługi Blob service** części sieci konta magazynu użytkownika lewe okienko nawigacji.

   ![Polecenie importu danych w portalu](./media/search-import-data-portal/import-data-cmd2.png "uruchomić Kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 — Ustaw źródło danych

W **źródła danych** stronie źródłowy musi być **usługi Azure Blob Storage**, z następującymi specyfikacjami:

+ **Dane do wyodrębnienia** powinien być *zawartości i metadanych*. Wybranie tej opcji pozwala kreatorowi rozpoznać schemat indeksu i mapowania pól do zaimportowania.
   
+ **Tryb analizy** powinna być równa *JSON*, *tablicę JSON* lub *wierszy JSON*. 

  *JSON* articulates każdy obiekt blob jako dokument jednego wyszukiwania, pojawią się jako niezależny element w wynikach wyszukiwania. 

  *Tablica JSON* za obiekty BLOB, które zawierają dane JSON sformułowany - poprawnie sformułowanej zawartości JSON odnosi się do tablicy obiektów lub ma właściwość, która jest tablicą obiektów i chcesz, aby każdy element, aby być przegubowe jako autonomiczny, niezależny wyszukiwania dokumentów. Jeśli obiekty BLOB są złożone i nie klikaj *tablicę JSON* całego obiektu blob są pozyskiwane jako pojedynczego dokumentu.

  *Wiersze JSON* dla obiektów blob, który składa się z wielu jednostek JSON oddzielone nowymi wierszami, którym ma każdej jednostki do być przegubowe jako dokument niezależnych wyszukiwania autonomicznych. Jeśli obiekty BLOB są złożone i nie klikaj *wierszy JSON* podczas analizowania trybu, a następnie całego obiektu blob są pozyskiwane jako pojedynczego dokumentu.
   
+ **Kontener magazynu** należy określić konto magazynu i kontener lub parametry połączenia, który jest rozpoznawany jako kontenera. Parametry połączenia można uzyskać na stronie portalu usługi obiektów Blob.

   ![Definicję źródła danych obiektu blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pomiń stronę "Dodaj cognitive search" w Kreatorze

Dodawanie umiejętności poznawcze nie jest konieczne do importowania dokumentu JSON. Jeśli nie ma określonych specjalnych potrzeb [obejmują interfejsy API usług Cognitive Services i przekształcenia](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, najpierw przejdź do następnej strony.

   ![Przycisk następnej strony dla wyszukiwania poznawczego](media/search-get-started-portal/next-button-add-cog-search.png)

Na tej stronie możesz przejść od razu do dostosowywania indeksu.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 — Ustaw atrybuty indeksu

W **indeksu** stronie powinien zostać wyświetlony listy pól z typem danych i serii pola wyboru do ustawiania atrybuty indeksu. Kreator może wygenerować listy pól na podstawie metadanych i przez próbkowanie źródła danych. 

Użytkownik może zbiorcze zaznaczania atrybutów, klikając pole wyboru w górnej części kolumny atrybutu. Wybierz **możliwość pobierania** i **z możliwością wyszukiwania** dla każdego pola, które ma zostać zwrócony do aplikacji klienckich i będą podlegać przetwarzania wyszukiwania pełnotekstowego. Można zauważyć, że liczb całkowitych nie są pełnotekstowego lub rozmyte wyszukiwanie (są oceniane verbatim i często są przydatne w filtrach).

Przejrzyj opis [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) Aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora, struktur danych fizycznych są tworzone i nie będzie można edytować tych pól bez porzucenie i ponowne utworzenie wszystkich obiektów.

   ![Definicja indeksu obiektu blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6\. Tworzenie indeksatora

W pełni określona, Kreator tworzy trzy różne obiekty w usłudze wyszukiwania. Obiekt źródła danych i indeksu obiektu są zapisywane jako o nazwie zasoby w usłudze Azure Search. Ostatnim krokiem tworzy obiekt indeksatora. Nazewnictwo indeksatora umożliwia mu istnieje jako zasób autonomicznych, której możliwe jest planowanie i zarządzanie nimi niezależnie od obiektu źródłowego indeksu i danych, tworzone po kolei kreatora.

Jeśli nie jesteś zaznajomiony z indeksatorów, *indeksatora* jest zasobem w usłudze Azure Search, która przeszukuje zewnętrzne źródło danych dla zawartość do przeszukiwania. Dane wyjściowe **importowania danych** Kreator ma indeksatora przeszukiwania źródła danych JSON, który wyodrębnia zawartość, którą można przeszukiwać i importuje je do indeksu usługi Azure Search.

   ![Definicja indeksatora obiektów blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Kliknij przycisk **OK** uruchom kreatora do tworzenia wszystkich obiektów. Indeksowanie rozpoczyna się natychmiast.

Można monitorować importowanie danych na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania i liczby dokumentów są przekazywane. 

Po zakończeniu indeksowania, można użyć [Eksploratora wyszukiwania](search-explorer.md) wykonywać zapytania w indeksie.

> [!NOTE]
> Jeśli nie widzisz danych, których oczekujesz, może być konieczne ustawienie więcej atrybutów w kolejnych pól. Usuwanie indeksu i indeksatora, właśnie utworzone i przejść przez kreatora ponownie, modyfikując wybrane atrybuty indeksu w kroku 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Interfejs API REST umożliwia indeksowanie obiektów blob JSON, zgodnie z przepływu pracy trzyczęściowej wspólne dla wszystkich indeksatorów w usłudze Azure Search: Utwórz źródło danych, Utwórz indeks, Utwórz indeksator. Wyodrębnianie danych z magazynu obiektów blob występuje, gdy wniosek tworzenie indeksatora. Po zakończeniu tego żądania, konieczne będzie odpytywalny indeksu. 

Możesz przejrzeć [REST przykładowy kod](#rest-example) na końcu tej sekcji, która pokazuje, jak utworzyć wszystkie trzy obiekty. Ta sekcja zawiera również szczegółowe informacje o [analizy tryby JSON](#parsing-modes), [pojedynczy obiektów blob](#parsing-single-blobs), [tablice notacji JSON](#parsing-arrays), i [zagnieżdżonych tablic](#nested-json-arrays).

Czy indeksowanie oparte na kodzie JSON, użyj [Postman](search-fiddler.md) i interfejsu API REST, aby utworzyć te obiekty:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Kolejność operacji wymaga, aby utworzyć i wywoływanie obiektów w tej kolejności. W przeciwieństwie do portalu przepływu pracy, to podejście kod wymaga dostępny indeks, aby akceptować dokumenty JSON wysyłane za pośrednictwem **tworzenie indeksatora** żądania.

Obiektów blob JSON w usłudze Azure Blob storage są zazwyczaj pojedynczego dokumentu JSON lub JSON "array". Indeksowanie obiektów blob w usłudze Azure Search może przeanalizować albo konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

| Dokument JSON | parsingMode | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Jeden na obiekt blob | `json` | Analizuje obiektów blob JSON jako jeden fragment tekstu. Każdy obiekt blob JSON staje się pojedynczym dokumencie usługi Azure Search. | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) interfejsu API i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) zestawu SDK. |
| Wiele na obiekt blob | `jsonArray` | Analizuje tablicę JSON w obiekcie blob, w którym każdy element tablicy staje się oddzielny dokument usługi Azure Search.  | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) interfejsu API i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) zestawu SDK. |
| Wiele na obiekt blob | `jsonLines` | Analizuje obiekcie blob, który zawiera wiele jednostek w formacie JSON ("array") rozdzielonych znakami nowego wiersza, w którym każda jednostka staje się oddzielny dokument usługi Azure Search. | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) interfejsu API i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) zestawu SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 — złożyć dane wejściowe dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora dla usługi Azure Search (w nagłówku POST), a nazwa konta magazynu i klucz do magazynu obiektów blob. Możesz użyć [Postman](search-fiddler.md) do wysyłania żądań HTTP do usługi Azure Search.

Skopiuj następujące cztery wartości do Notatnika, dzięki czemu można je wkleić do żądania:

+ Nazwa usługi w usłudze Azure Search
+ Klucz administratora w usłudze Azure Search
+ Nazwa konta magazynu platformy Azure
+ Klucz konta usługi Azure storage

Te wartości można znaleźć w portalu:

1. Na stronach portalu usługi Azure Search skopiuj adres URL usługi wyszukiwania na stronie Przegląd.

2. W okienku nawigacji po lewej stronie kliknij **klucze** , a następnie skopiuj podstawowy lub pomocniczy klucza (są one równoważne).

3. Przełącz się do konta magazynu na stronach portalu. W okienku nawigacji po lewej stronie w obszarze **ustawienia**, kliknij przycisk **klucze dostępu**. Ta strona zawiera nazwę konta i klucz. Skopiuj nazwę konta magazynu i jeden z kluczy do Notatnika.

### <a name="2---create-a-data-source"></a>2 — Tworzenie źródła danych

Ten krok obejmuje informacje o połączeniu źródło danych używane przez indeksator. Źródło danych jest nazwany obiekt w usłudze Azure Search, która utrwala dane połączenia. Typ źródła danych `azureblob`, określa, które zachowania wyodrębniania danych są wywoływane przez indeksator. 

Zastąp prawidłowe wartości dla nazwy usługi, klucz administratora i konto magazynu oraz konto klucza symbole zastępcze.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 — Tworzenie docelowym indeksem wyszukiwania 

Indeksatory są skojarzone ze schematem indeksu. Korzystając z interfejsu API (zamiast portalu), przygotuj indeksu z wyprzedzeniem, aby można je określić na indeksatora.

Zawartość do przeszukiwania indeksu są przechowywane w usłudze Azure Search. Aby utworzyć indeks, zapewniają schematu, która określa pola w dokumencie, atrybuty i innych konstrukcji, które kształt środowisko wyszukiwania. Jeśli utworzysz indeks, który ma te same nazwy pól i typów danych jako źródła, indeksator będzie zgodna pola źródłowego i docelowego zaoszczędzić pracy konieczności jawnego mapowania pól.

W poniższym przykładzie przedstawiono [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) żądania. Indeks będzie miał wyszukiwanie `content` pola do przechowywania tekstu wyodrębnionego z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 — Konfigurowanie i uruchamianie indeksatora

Przy użyciu indeksu, jak i danych źródła i indeksatora jest również nazwanego obiektu, który utworzysz i ponowne użycie w usłudze Azure Search. W pełni określonego żądania utworzenia indeksator może wyglądać w następujący sposób:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Konfiguracji indeksatora znajduje się w treści żądania. Wymaga to źródło danych i indeksu docelowego pusty, która już istnieje w usłudze Azure Search. 

Harmonogram i parametry są opcjonalne. Jeżeli pominięto ich uruchomieniu indeksatora natychmiast, za pomocą `json` jako tryb analizy.

Ten indeksator określonego nie zawiera mapowania pól. W ramach definicji indeksatora, możesz pozostawić **mapowania pól** Jeśli właściwości źródła dokument JSON zgodny pola z docelowym indeksem wyszukiwania. 


### <a name="rest-example"></a>Przykład REST

Ta sekcja jest podsumowanie wszystkich żądań, które są używane do tworzenia obiektów. Omówienie części składowe można zobaczyć w poprzednich sekcjach, w tym artykule.

### <a name="data-source-request"></a>Żądanie źródła danych

Wszystkie indeksatory wymaga obiektu źródła danych, który zawiera informacje o połączeniu z istniejącymi danymi. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Żądanie indeksu

Wszystkie indeksatory wymagają indeksu docelowego, który odbiera dane. Treść żądania definiuje schemat indeksu, składający się z pól, przypisane do obsługiwać pożądane zachowania, którą można przeszukiwać indeksu. Indeks ten powinien być pusty, po uruchomieniu indeksatora. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Żądania indeksowania

To żądanie zawiera indeksator z w pełni określona. Zawiera mapowania pól, które zostały pominięte w poprzednich przykładach. Pamiętaj, że "schedule", "parameters" i "fieldMappings" są opcjonalne, tak długo, jak brak dostępnych domyślnych. Pominięcie "zaplanować" powoduje, że indeksator natychmiast uruchomić. Pominięcie "parsingMode" powoduje, że indeks, aby użyć domyślnej "json".

Tworzenie indeksatora w usłudze Azure Search wyzwala importu danych. Działa natychmiast, a następnie zgodnie z harmonogramem, jeśli podano jeden.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Zestaw SDK platformy .NET ma pełną parzystości przy użyciu interfejsu API REST. Firma Microsoft zaleca przejrzenie poprzedniej sekcji interfejsu API REST, aby dowiedzieć się, pojęcia i przepływu pracy oraz wymagania. Następnie można się odwoływać do poniższej dokumentacji interfejsu API platformy .NET do zaimplementowania indeksatora JSON w kodzie zarządzanym.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Tryby analizy

Wiele form, można założyć obiektów blob JSON. **ParsingMode** parametru indeksatora JSON Określa, jak zawartość obiektu blob JSON zostanie przeanalizowana i ze strukturą w indeksie usługi Azure Search:

| parsingMode | Opis |
|-------------|-------------|
| `json`  | Indeks każdego obiektu blob jako pojedynczy dokument. Jest to opcja domyślna. |
| `jsonArray` | Wybierz ten tryb, jeśli obiekty BLOB składają się z tablice notacji JSON i potrzebny każdego elementu tablicy, aby stać się oddzielny dokument w usłudze Azure Search. |
|`jsonLines` | Wybierz ten tryb, jeśli obiekty BLOB składają się z wielu jednostek JSON, które są oddzielone znakiem nowego wiersza, i chcesz, aby każda jednostka, aby stać się oddzielny dokument w usłudze Azure Search. |

Dokumentu można traktować jako pojedynczy element w wynikach wyszukiwania. Jeśli chcesz, aby każdy element w tablicy, które były wyświetlane w wynikach wyszukiwania jako niezależny element, należy użyć `jsonArray` lub `jsonLines` odpowiednią opcję.

W ramach definicji indeksatora możesz opcjonalnie użyć [mapowania pól](search-indexer-field-mappings.md) wyboru właściwości dokumentu JSON źródła, które są używane do wypełnienia z docelowym indeksem wyszukiwania. Aby uzyskać `jsonArray` podczas analizowania trybu, jeśli tablica istnieje jako właściwość niższego poziomu, można ustawić katalog główny dokumentów wskazująca, gdzie tablicy jest umieszczany w ramach obiektu blob.

> [!IMPORTANT]
> Kiedy używasz `json`, `jsonArray` lub `jsonLines` tryb analizy, usługa Azure Search przyjęto założenie, że wszystkie obiekty BLOB ze źródła danych zawierają JSON. Daj nam znać, jeśli potrzebujesz do obsługi różnych obiektów blob JSON i -JSON, w tym samym źródle danych, na [naszą witrynę UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analizowanie pojedynczego obiektów blob JSON

Domyślnie [indeksatora obiektów blob usługi Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje obiektów blob JSON jako jeden fragment tekstu. Często chcesz zachować strukturę dokumentów JSON. Załóżmy na przykład, że masz następujący dokument JSON w usłudze Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indeksowanie obiektów blob analizuje dokument JSON w jednym dokumencie usługi Azure Search. Indeksator ładuje indeks, dopasowując "text", "datePublished" i "tags" ze źródła względem pola indeksu docelowego identycznie nazwane i wpisane.

Jak wspomniano, mapowań pól nie są wymagane. Podany indeks z "text", "datePublished i"tags"pola, obiekt blob indeksator może wywnioskować poprawne mapowania bez pola mapowania znajduje się w żądaniu.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizowanie tablice notacji JSON

Alternatywnie można użyć opcji tablicy JSON. Ta opcja jest przydatna, gdy zawierają obiekty BLOB *Tablica obiektów JSON sformułowany*, i chcesz, aby każdy element, aby stać się oddzielny dokument usługi Azure Search. Na przykład biorąc pod uwagę następujący obiekt blob JSON, które możesz wypełnić indeksu usługi Azure Search przy użyciu trzech oddzielnych dokumentów, każdy z pola "id" i "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Dla tablicy JSON definicja indeksatora powinien wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parametr parsingMode Określa `jsonArray` analizatora. Określanie prawo analizator i posiadające odpowiednie dane dane wejściowe są tylko dwa wymagania specyficzne dla tablicy indeksowanie obiektów blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Ponownie Zwróć uwagę, że mapowania pól można pominąć. Zakładając, że indeks o nazwach "id" i "text" pól, indeksatora obiektów blob można wywnioskować poprawne mapowania bez jawnego pola mapowania listy.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analizowanie zagnieżdżonych tablic
Dla formatu JSON tablice o zagnieżdżone elementy, można określić `documentRoot` do wskazania wielopoziomowe struktury. Na przykład, jeśli obiekty BLOB wyglądać następująco:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Indeks tablicy zawarte w przy użyciu tej konfiguracji `level2` właściwości:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analizowanie obiektów blob, oddzielone tabulacji

Jeśli obiekt blob zawiera wiele jednostek JSON rozdzielone znakami nowego wiersza i chcesz, aby każdy element, aby stać się oddzielny dokument usługi Azure Search, możesz zdecydować się opcji linii JSON. Na przykład, biorąc pod uwagę następujący obiekt blob (tam, gdzie istnieją trzy różne jednostki JSON), które możesz wypełnić indeksu usługi Azure Search przy użyciu trzech oddzielnych dokumentów, każdy z pola "id" i "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Dla wierszy w formacie JSON definicja indeksatora powinien wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parametr parsingMode Określa `jsonLines` analizatora. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Ponownie Zwróć uwagę, że mapowania pól może być pominięty, podobnie jak `jsonArray` tryb analizy.

## <a name="add-field-mappings"></a>Dodaj mapowań pól

Gdy pola źródłowe i docelowe nie są dokładnie wyrównana, można zdefiniować sekcję mapowania pola w treści żądania dla jawnego skojarzeń pola do pola.

Obecnie usługa Azure Search nie może indeksować dowolnych dokumentów JSON bezpośrednio, ponieważ obsługuje on pierwotne typy danych, tablic ciągów i GeoJSON punktów. Można jednak użyć **mapowania pól** wybrać części dokumentu JSON, a "lift" je do pól najwyższego poziomu dokumentu wyszukiwania. Aby poznać podstawy mapowania pól, zobacz temat [mapowania pól w indeksatorach usługi Azure Search](search-indexer-field-mappings.md).

Ponowne spojrzenie na dokument JSON w naszym przykładzie:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Przyjęto założenie, indeks wyszukiwania przy użyciu następujących pól: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, i `tags` typu `Collection(Edm.String)`. Zwróć uwagę rozbieżność między "datePublished" w źródle i `date` pola w indeksie. Aby zamapować JSON do żądanego kształtu, użyj następujące mapowania pola:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Źródło nazwy pól w mapowania są określane za pomocą [wskaźnik JSON](https://tools.ietf.org/html/rfc6901) notacji. Możesz zaczynać się ukośnikiem do odwoływania się do katalogu głównego dokumentu JSON, a następnie wybierz żądaną właściwość (na dowolny poziom zagnieżdżenia) przy użyciu ścieżki oddzielone ukośnikiem do przodu.

Może również odnosić się do elementów tablicy poszczególnych przy użyciu indeksu zaczynającego się od zera. Na przykład wybierz pierwszy element tablicy "tags" z powyższego przykładu, należy użyć mapowania pól w następujący sposób:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródła, w ścieżce mapowania pola odwołuje się do właściwości, która nie istnieje w formacie JSON, mapowanie jest pomijane bez błędów. Można to zrobić, aby firma Microsoft może obsługiwać dokumenty z innym schematem (jest to typowy przypadek użycia). Ponieważ nie ma możliwości weryfikacji, należy uważać, aby uniknąć błędów w specyfikacji mapowania pól.
>
>

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Indeksowanie usługi Azure Blob Storage z usługą Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
+ [Samouczek: Przeszukiwanie częściowo ustrukturyzowanych danych z magazynu obiektów Blob platformy Azure](search-semi-structured-data.md)
