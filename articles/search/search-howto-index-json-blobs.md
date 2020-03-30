---
title: Wyszukiwanie w obiektach blob JSON
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie obiektów blob usługi Azure JSON dla zawartości tekstowej przy użyciu indeksatora obiektów blob usługi Azure Cognitive Search. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak magazyn obiektów Blob platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533968"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Jak indeksować obiekty blob JSON przy użyciu indeksatora obiektów Blob w usłudze Azure Cognitive Search

W tym artykule pokazano, jak skonfigurować [indeksator](search-indexer-overview.md) obiektów blob usługi Azure Cognitive Search, aby wyodrębnić zawartość strukturalną z dokumentów JSON w magazynie obiektów Blob platformy Azure i uczynić ją przeszukiwaną w usłudze Azure Cognitive Search. Ten przepływ pracy tworzy indeks usługi Azure Cognitive Search i ładuje go z istniejącym tekstem wyodrębnionym z obiektów blob JSON. 

Do indeksu zawartości JSON można użyć [interfejsu portal,](#json-indexer-portal) [interfejsów API REST](#json-indexer-rest)lub [.NET SDK.](#json-indexer-dotnet) Typowe dla wszystkich podejść jest to, że dokumenty JSON znajdują się w kontenerze obiektów blob na koncie usługi Azure Storage. Aby uzyskać wskazówki dotyczące wypychania dokumentów JSON z innych platform innych niż platformy Azure, zobacz [Importowanie danych w usłudze Azure Cognitive Search](search-what-is-data-import.md).

Obiekty BLOB JSON w magazynie obiektów Blob platformy Azure są zazwyczaj `json`pojedynczym dokumentem JSON (tryb analizowania jest) lub kolekcją jednostek JSON. W przypadku kolekcji obiekt blob może mieć **tablicę** dobrze ukształtowanych elementów JSON (tryb analizowania jest `jsonArray`). Obiekty BLOB mogą również składać się z wielu pojedynczych jednostek JSON `jsonLines`oddzielonych nową linię (tryb analizowania jest ). **ParsingMode** parametr na żądanie określa struktur danych wyjściowych.

> [!NOTE]
> Aby uzyskać więcej informacji na temat indeksowania wielu dokumentów wyszukiwania z jednego obiektu blob, zobacz [Indeksowanie jeden do wielu](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostszą metodą indeksowania dokumentów JSON jest użycie kreatora w [witrynie Azure portal](https://portal.azure.com/). Analizując metadane w kontenerze obiektów blob platformy Azure, Kreator [**importu danych**](search-import-data-portal.md) może utworzyć domyślny indeks, mapować pola źródłowe do docelowych pól indeksu i załadować indeks w jednej operacji. W zależności od rozmiaru i złożoności danych źródłowych może mieć operacyjny indeks wyszukiwania pełnotekstowego w ciągu kilku minut.

Firma Microsoft zaleca używanie tego samego regionu lub lokalizacji dla usługi Azure Cognitive Search i usługi Azure Storage w celu zmniejszenia opóźnień i uniknięcia opłat za przepustowość.

### <a name="1---prepare-source-data"></a>1 - Przygotowanie danych źródłowych

[Zaloguj się do witryny Azure portal](https://portal.azure.com/) i [utwórz kontener obiektów blob,](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) aby zawierał dane. Publiczny poziom dostępu można ustawić na dowolną z jego prawidłowych wartości.

Do pobrania danych w Kreatorze **importowania danych** potrzebna będzie nazwa konta magazynu, nazwa kontenera i klucz dostępu.

### <a name="2---start-import-data-wizard"></a>2 - Uruchom Kreatora danych importu

Na stronie Przegląd usługi wyszukiwania można [uruchomić kreatora](search-import-data-portal.md) na pasku poleceń.

   ![Polecenie Importuj dane w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 - Ustawianie źródła danych

Na stronie **źródła danych** źródłem musi być usługa **Azure Blob Storage**, z następującymi specyfikacjami:

+ **Dane do wyodrębnienia** powinny być *zawartość i metadane*. Wybranie tej opcji umożliwia kreatorowi wywnioskowanie schematu indeksu i mapowanie pól do zaimportowania.
   
+ **Tryb analizy** powinien być ustawiony na *JSON,* *JSON array* lub *JSON.* 

  *JSON* artykułuje każdy obiekt blob jako pojedynczy dokument wyszukiwania, wyświetla się jako niezależny element w wynikach wyszukiwania. 

  *Tablica JSON* jest dla obiektów blob, które zawierają dobrze sformułowane dane JSON - dobrze sformułowany JSON odpowiada tablicy obiektów lub ma właściwość, która jest tablicą obiektów i chcesz, aby każdy element był artykułowany jako samodzielny, niezależny dokument wyszukiwania. Jeśli obiekty BLOB są złożone i nie wybierasz *tablicy JSON,* cały obiekt blob jest pozyskiwania jako pojedynczy dokument.

  *Wiersze JSON* są przeznaczone dla obiektów blob składających się z wielu jednostek JSON oddzielonych nowym wierszem, w których każda encja ma być wyrażona jako samodzielny niezależny dokument wyszukiwania. Jeśli obiekty BLOB są złożone i nie wybierasz trybu analizowania *wierszy JSON,* cały obiekt blob jest pozyskiwania jako pojedynczy dokument.
   
+ **Kontener magazynu** należy określić konto magazynu i kontenera lub parametry połączenia, który jest rozpoznawany do kontenera. Ciągi połączeń można uzyskać na stronie portalu usługi obiektów Blob.

   ![Definicja źródła danych obiektu Blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pomiń stronę "Wzbogać zawartość" w kreatorze

Dodawanie umiejętności poznawczych (lub wzbogacenia) nie jest wymogiem importu. Jeśli nie masz określonej potrzeby, aby [dodać wzbogacenie AI](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, kliknij niebieskie przyciski u dołu strony dla "Dalej" i "Pomiń".

### <a name="5---set-index-attributes"></a>5 - Ustawianie atrybutów indeksu

Na stronie **Indeks** powinna zostać wyświetlona lista pól z typem danych i seria pól wyboru do ustawiania atrybutów indeksu. Kreator może wygenerować listę pól na podstawie metadanych i próbkowania danych źródłowych. 

Atrybuty zaznaczać zbiorczo, klikając pole wyboru u góry kolumny atrybutów. Wybierz **opcję Możliwe do pobrania** i **przeszukiwania** dla każdego pola, które powinno zostać zwrócone do aplikacji klienckiej i podlegać przetwarzaniu wyszukiwania pełnotekstowego. Można zauważyć, że liczby całkowite nie są pełny tekst lub rozmyte przeszukiwania (numery są oceniane dosłownie i są często przydatne w filtrach).

Przejrzyj opis [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatorów języka,](https://docs.microsoft.com/rest/api/searchservice/language-support) aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć swoje wybory. Po uruchomieniu kreatora tworzone są struktury danych fizycznych i nie będzie można edytować tych pól bez upuszczania i ponownego tworzenia wszystkich obiektów.

   ![Definicja indeksu obiektów blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Tworzenie indeksatora

W pełni określony kreator tworzy trzy odrębne obiekty w usłudze wyszukiwania. Obiekt źródła danych i obiekt indeksu są zapisywane jako nazwane zasoby w usłudze Azure Cognitive Search. Ostatni krok tworzy obiekt indeksatora. Nazywanie indeksatora umożliwia jego istnienie jako autonomicznego zasobu, który można zaplanować i zarządzać niezależnie od obiektu źródła indeksu i danych, utworzonego w tej samej sekwencji kreatora.

Jeśli nie jesteś zaznajomiony z indeksatorów, *indeksator* jest zasobem w usłudze Azure Cognitive Search, który indeksuje zewnętrzne źródło danych dla zawartości z wyszukuj. Dane wyjściowe Kreatora **danych importu** jest indeksatorem, który indeksuje źródło danych JSON, wyodrębnia zawartość zbywalną i importuje ją do indeksu w usłudze Azure Cognitive Search.

   ![Definicja indeksatora obiektów blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Kliknij **przycisk OK,** aby uruchomić kreatora i utworzyć wszystkie obiekty. Indeksowanie rozpoczyna się natychmiast.

Importowanie danych można monitorować na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania i liczbę dokumentów. 

Po zakończeniu indeksowania można użyć [Eksploratora wyszukiwania,](search-explorer.md) aby zbadać indeks.

> [!NOTE]
> Jeśli nie widzisz oczekiwanych danych, może być konieczne ustawienie większej liczby atrybutów w większej liczbie pól. Usuń indeks i indeksator, który właśnie utworzono, i ponownie przestępuj kreatora, modyfikując wybrane atrybuty indeksu w kroku 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Za pomocą interfejsu API REST można indeksować obiekty BLOB JSON, zgodnie z trzyczęściowym przepływem pracy wspólnym dla wszystkich indeksatorów w usłudze Azure Cognitive Search: tworzenie źródła danych, tworzenie indeksu, tworzenie indeksatora. Wyodrębnianie danych z magazynu obiektów blob występuje podczas przesyłania żądania Utwórz indeksatora. Po zakończeniu tego żądania, będziesz miał indeks queryable. 

Można przejrzeć [przykładowy kod REST](#rest-example) na końcu tej sekcji, który pokazuje, jak utworzyć wszystkie trzy obiekty. Ta sekcja zawiera również szczegółowe informacje o [trybach analizy JSON, pojedynczych obiektach](#parsing-modes) [blob,](#parsing-single-blobs) [tablicach JSON](#parsing-arrays)i [tablicach zagnieżdżonych.](#nested-json-arrays)

W przypadku indeksowania JSON opartego na kodzie użyj [postmana](search-get-started-postman.md) i interfejsu API REST, aby utworzyć następujące obiekty:

+ [Indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Kolejność operacji wymaga tworzenia i wywoływania obiektów w tej kolejności. W przeciwieństwie do przepływu pracy portalu podejście kodu wymaga dostępnego indeksu do akceptowania dokumentów JSON wysłanych za pośrednictwem żądania **Utwórz indeksatora.**

Obiekty BLOB JSON w magazynie obiektów Blob platformy Azure są zazwyczaj pojedynczym dokumentem JSON lub "tablicą" JSON. Indeksator obiektów blob w usłudze Azure Cognitive Search można przeanalizować albo budowy, w zależności od sposobu **ustawiania parsingMode** parametru na żądanie.

| Dokument JSON | parsingMode | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Jeden na obiekt blob | `json` | Analizuje obiekty blob JSON jako pojedynczy fragment tekstu. Każdy obiekt blob JSON staje się pojedynczym dokumentem usługi Azure Cognitive Search. | Ogólnie dostępne zarówno w interfejsie API [REST,](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) jak i w pliku [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Wiele na obiekt blob | `jsonArray` | Analizuje tablicę JSON w obiekcie blob, gdzie każdy element tablicy staje się oddzielnym dokumentem usługi Azure Cognitive Search.  | Ogólnie dostępne zarówno w interfejsie API [REST,](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) jak i w pliku [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Wiele na obiekt blob | `jsonLines` | Analizuje obiekt blob, który zawiera wiele jednostek JSON ("tablica") oddzielone przez nową linię, gdzie każda jednostka staje się oddzielnym dokumentem usługi Azure Cognitive Search. | Ogólnie dostępne zarówno w interfejsie API [REST,](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) jak i w pliku [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montaż wejść dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora usługi Azure Cognitive Search (w nagłówku POST) oraz nazwę konta magazynu i klucz magazynu dla magazynu obiektów blob. [Listonosza](search-get-started-postman.md) służy do wysyłania żądań HTTP do usługi Azure Cognitive Search.

Skopiuj następujące cztery wartości do Notatnika, aby można je wkleić do żądania:

+ Nazwa usługi Azure Cognitive Search
+ Klucz administratora usługi Azure Cognitive Search
+ Nazwa konta usługi Azure Storage
+ Klucz konta magazynu platformy Azure

Wartości te można znaleźć w portalu:

1. Na stronach portalu usługi Azure Cognitive Search skopiuj adres URL usługi wyszukiwania ze strony Przegląd.

2. W lewym okienku nawigacji kliknij pozycję **Klawisze,** a następnie skopiuj klucz podstawowy lub pomocniczy (są one równoważne).

3. Przełącz się na strony portalu dla swojego konta magazynu. W lewym okienku nawigacji w obszarze **Ustawienia**kliknij pozycję **Klawisze dostępu**. Ta strona zawiera zarówno nazwę konta, jak i klucz. Skopiuj nazwę konta magazynu i jeden z kluczy do Notatnika.

### <a name="2---create-a-data-source"></a>2 - Tworzenie źródła danych

Ten krok zawiera informacje o połączeniu źródła danych używane przez indeksatora. Źródło danych jest nazwany obiekt w usłudze Azure Cognitive Search, który utrwala informacje o połączeniu. Typ źródła danych `azureblob`, określa, które zachowania wyodrębniania danych są wywoływane przez indeksatora. 

Zastąp prawidłowe wartości dla nazwy usługi, klucza administratora, konta magazynu i symboli zastępczych klucza konta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Tworzenie docelowego indeksu wyszukiwania 

Indeksatory są sparowane ze schematem indeksu. Jeśli używasz interfejsu API (a nie portalu), przygotuj indeks z wyprzedzeniem, dzięki czemu można określić go w operacji indeksatora.

Indeks przechowuje zawartość z przeszukiwalną w usłudze Azure Cognitive Search. Aby utworzyć indeks, podaj schemat określający pola w dokumencie, atrybuty i inne konstrukcje kształtujące środowisko wyszukiwania. Jeśli utworzysz indeks, który ma takie same nazwy pól i typy danych jak źródło, indeksator będzie zgodny z pól źródłowych i docelowych, oszczędzając pracę konieczności jawnie mapowania pól.

W poniższym przykładzie przedstawiono żądanie [tworzenia indeksu.](https://docs.microsoft.com/rest/api/searchservice/create-index) Indeks będzie miał pole `content` z wyszukujalnym do przechowywania tekstu wyodrębnianego z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurowanie i uruchamianie indeksatora

Podobnie jak w indeksie i źródle danych, a indeksator jest również nazwany obiekt, który można utworzyć i ponownie użyć w usłudze Azure Cognitive Search. W pełni określone żądanie utworzenia indeksatora może wyglądać następująco:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Konfiguracja indeksatora znajduje się w treści żądania. Wymaga źródła danych i pustego indeksu docelowego, który już istnieje w usłudze Azure Cognitive Search. 

Harmonogram i parametry są opcjonalne. Jeśli je pominąć, indeksator uruchamia `json` się natychmiast, przy użyciu jako tryb analizy.

Ten konkretny indeksator nie zawiera mapowań pól. W definicji indeksatora można pominąć **mapowania pól,** jeśli właściwości źródłowego dokumentu JSON są zgodne z polami docelowego indeksu wyszukiwania. 


### <a name="rest-example"></a>Przykład REST

Ta sekcja jest podsumowanie wszystkich żądań używanych do tworzenia obiektów. Aby zapoznać się z omówienia części składowych, zobacz poprzednie sekcje tego artykułu.

### <a name="data-source-request"></a>Żądanie źródła danych

Wszystkie indeksatory wymagają obiektu źródła danych, który dostarcza informacji o połączeniu z istniejącymi danymi. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Żądanie indeksu

Wszystkie indeksatory wymagają indeksu docelowego, który odbiera dane. Treść żądania definiuje schemat indeksu, składający się z pól, przypisane do obsługi żądanych zachowań w indeksie z wyszukanym. Ten indeks powinien być pusty po uruchomieniu indeksatora. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Żądanie indeksatora

To żądanie zawiera w pełni określony indeksator. Zawiera mapowania pól, które zostały pominięte w poprzednich przykładach. Przypomnijmy, że "harmonogram", "parametry" i "fieldMappings" są opcjonalne, o ile istnieje dostępne domyślne. Pominięcie "harmonogram" powoduje, że indeksator do uruchomienia natychmiast. Pominięcie "parsingMode" powoduje, że indeks do korzystania z "json" domyślnie.

Tworzenie indeksatora w usłudze Azure Cognitive Search wyzwala import danych. Działa natychmiast, a następnie zgodnie z harmonogramem, jeśli podałeś jeden.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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

Plik SDK .NET ma pełną parzystość z interfejsem API REST. Zaleca się przejrzenie poprzedniej sekcji interfejsu API REST, aby poznać pojęcia, przepływ pracy i wymagania. Następnie można odwołać się do następującej dokumentacji odwołania do interfejsu API platformy .NET, aby zaimplementować indeksator JSON w kodzie zarządzanym.

+ [źródło danych microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Tryby analizowania

Obiekty BLOB JSON mogą przyjąć wiele formularzy. Parametr **parsingMode** w indeksatorze JSON określa sposób analizowania zawartości obiektu blob JSON i strukturyzowania ich w indeksie usługi Azure Cognitive Search:

| parsingMode | Opis |
|-------------|-------------|
| `json`  | Indeksowanie każdego obiektu blob jako pojedynczego dokumentu. Domyślnie włączone. |
| `jsonArray` | Wybierz ten tryb, jeśli obiekty blob składają się z tablic JSON i potrzebujesz każdego elementu tablicy, aby stać się oddzielnym dokumentem w usłudze Azure Cognitive Search. |
|`jsonLines` | Wybierz ten tryb, jeśli obiekty blob składają się z wielu jednostek JSON, które są oddzielone nowym wierszem i każda jednostka musi stać się oddzielnym dokumentem w usłudze Azure Cognitive Search. |

Dokument można myśleć jako o pojedynczym elemencie w wynikach wyszukiwania. Jeśli chcesz, aby każdy element w tablicy był pokazywał się `jsonArray` `jsonLines` w wynikach wyszukiwania jako element niezależny, użyj odpowiedniego elementu lub opcji.

W definicji indeksatora można opcjonalnie użyć [mapowania pól,](search-indexer-field-mappings.md) aby wybrać właściwości źródłowego dokumentu JSON są używane do wypełniania docelowego indeksu wyszukiwania. W `jsonArray` trybie analizowania, jeśli tablica istnieje jako właściwość niższego poziomu, można ustawić katalog główny dokumentu wskazujący, gdzie tablica jest umieszczona w obiekcie blob.

> [!IMPORTANT]
> Podczas korzystania `json` `jsonArray` z `jsonLines` trybu analizy lub analizowania usługa Azure Cognitive Search zakłada, że wszystkie obiekty blob w źródle danych zawierają usługę JSON. Jeśli chcesz obsługiwać kombinację obiektów blob JSON i innych niż JSON w tym samym źródle danych, poinformuj nas o tym na [naszej stronie UserVoice.](https://feedback.azure.com/forums/263029-azure-search)


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analizuj pojedyncze obiekty blob JSON

Domyślnie [indeksator obiektów blob usługi Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) analizuje obiekty BLOB JSON jako pojedynczy fragment tekstu. Często chcesz zachować strukturę dokumentów JSON. Załóżmy na przykład, że masz następujący dokument JSON w magazynie obiektów blob platformy Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indeksator obiektów blob analizuje dokument JSON w jednym dokumencie usługi Azure Cognitive Search. Indeksator ładuje indeks przez dopasowanie "tekst", "datePublikowane" i "tagi" ze źródła względem pól indeksu docelowego o identycznym i nazwanym.

Jak wspomniano, mapowania pól nie są wymagane. Biorąc pod uwagę indeks z "tekst", "datePublikowane i "tagi" pola, indeksator obiektów blob można wywnioskować poprawne mapowanie bez mapowania pola obecne w żądaniu.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizuj tablice JSON

Alternatywnie można użyć opcji tablicy JSON. Ta opcja jest przydatna, gdy obiekty BLOB zawierają *tablicę dobrze sformułowanych obiektów JSON*i chcesz, aby każdy element stał się oddzielnym dokumentem usługi Azure Cognitive Search. Na przykład biorąc pod uwagę następujące JSON obiektu blob, można wypełnić indeks usługi Azure Cognitive Search z trzech oddzielnych dokumentów, każdy z "id" i "tekst" pola.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Dla tablicy JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parsingMode `jsonArray` parametr określa analizatora. Określanie odpowiedniego analizatora i posiadanie odpowiedniego danych wejściowych są tylko dwa wymagania specyficzne dla tablicy do indeksowania obiektów blob JSON.

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

Ponownie należy zauważyć, że mapowania pól można pominąć. Zakładając, że indeks o identycznych nazwach "id" i "tekst" pól, indeksator obiektów blob można wywnioskować poprawne mapowanie bez jawnej listy mapowania pól.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analizuj tablice zagnieżdżone
Dla tablic JSON mających elementy zagnieżdżone, można `documentRoot` określić, aby wskazać strukturę wielopoziomową. Na przykład, jeśli obiekty blob wyglądają następująco:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Ta konfiguracja służy do indeksu `level2` tablicy zawartej we właściwości:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analizuj obiekty blob oddzielone nowymi liniami

Jeśli obiekt blob zawiera wiele jednostek JSON oddzielonych nową linią i chcesz, aby każdy element stał się oddzielnym dokumentem usługi Azure Cognitive Search, możesz wybrać opcję wierszy JSON. Na przykład biorąc pod uwagę następujące obiektu blob (gdzie istnieją trzy różne jednostki JSON), można wypełnić indeks usługi Azure Cognitive Search z trzech oddzielnych dokumentów, każdy z "id" i "tekst" pola.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

W przypadku wierszy JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parsingMode `jsonLines` parametr określa analizatora. 

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

Ponownie należy zauważyć, że mapowania pól można `jsonArray` pominąć, podobnie jak w trybie analizy.

## <a name="add-field-mappings"></a>Dodawanie mapowań pól

Jeśli pola źródłowe i docelowe nie są idealnie wyrównane, można zdefiniować sekcję mapowania pól w treści żądania dla jawnych skojarzeń pola z polami.

Obecnie usługa Azure Cognitive Search nie może indeksować dowolnych dokumentów JSON bezpośrednio, ponieważ obsługuje tylko podstawowe typy danych, tablice ciągów i punkty GeoJSON. Można jednak użyć **mapowań pól,** aby wybrać części dokumentu JSON i "podnieść" je do pól najwyższego poziomu dokumentu wyszukiwania. Aby dowiedzieć się więcej o podstawach mapowania pól, zobacz [Mapowanie pól w indeksatorach usługi Azure Cognitive Search](search-indexer-field-mappings.md).

Ponowne zapoznanie się z naszym przykładowym dokumentem JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Załóżmy, że indeks `text` wyszukiwania `Edm.String`z `date` następującymi polami: typu , typu `Edm.DateTimeOffset`i `tags` typu `Collection(Edm.String)`. Zwróć uwagę na rozbieżność między "datePublikowane" w źródle i `date` polu w indeksie. Aby zamapować json do żądanego kształtu, użyj następujących mapowań pól:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Nazwy pól źródłowych w mapowaniach są określane przy użyciu notacji [wskaźnika JSON.](https://tools.ietf.org/html/rfc6901) Możesz zacząć od ukośnika do przodu, aby odwołać się do katalogu głównego dokumentu JSON, a następnie wybierz żądaną właściwość (na dowolnym poziomie zagnieżdżania) przy użyciu ścieżki oddzielonej ukośnikiem.

Można również odwoływać się do poszczególnych elementów tablicy przy użyciu indeksu opartego na wartości zero. Na przykład, aby wybrać pierwszy element tablicy "tagi" z powyższego przykładu, użyj mapowania pola w ten sposób:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródłowego w ścieżce mapowania pól odwołuje się do właściwości, która nie istnieje w JSON, to mapowanie jest pomijane bez błędu. Odbywa się to tak, że możemy obsługiwać dokumenty z innym schematem (co jest typowym przypadkiem użycia). Ponieważ nie ma sprawdzania poprawności, należy zadbać o unikanie literówek w specyfikacji mapowania pól.
>
>

## <a name="see-also"></a>Zobacz też

+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Indeksowanie usługi Azure Blob Storage za pomocą usługi Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów BLOB za pomocą indeksatora obiektów blob usługi Azure Cognitive Search](search-howto-index-csv-blobs.md)
+ [Samouczek: wyszukiwanie danych o częściowo ustrukturyzowanych danych z magazynu obiektów blob platformy Azure](search-semi-structured-data.md)
