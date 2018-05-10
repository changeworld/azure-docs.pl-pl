---
title: Indeksowanie magazynu obiektów Blob Azure o usłudze Azure Search
description: Dowiedz się, jak indeksu usługi Azure Blob Storage i Wyodrębnij tekst z dokumentów za pomocą usługi Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: b2660a98139068a8472c018de5cfbd29d6867c5a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indeksowanie dokumentów w magazynie obiektów Blob Azure o usłudze Azure Search
W tym artykule przedstawiono sposób użycia usługi Azure Search w celu indeksowania dokumentów (takich jak PDF, dokumentów Microsoft Office i kilka innych typowych formatach) przechowywanych w magazynie obiektów Blob platformy Azure. Po pierwsze wyjaśniono podstawowe informacje o instalowaniu i konfigurowaniu indeksatora obiektu blob. Następnie zapewnia lepszą badań zachowania i scenariusze jest prawdopodobnie mogą wystąpić.

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentu
Indeksator obiektu blob można wyodrębnić tekst z dokumentu w następujących formatach:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurowanie indeksowanie obiektów blob
Można skonfigurować indeksator usługi Azure Blob Storage za pomocą:

* [Azure Portal](https://ms.portal.azure.com)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Usługa Azure Search [zestawu .NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pola) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>
>

W tym miejscu przedstawiony przepływu przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Źródło danych określa, które dane do indeksu poświadczeń potrzebnych do dostępu do danych i zasad, aby wydajnie zidentyfikować zmiany danych (nowych, zmodyfikowanych lub usuniętych wierszy). Źródło danych może służyć przez wiele indeksatorów w tej samej usługi wyszukiwania.

W przypadku indeksowanie obiektów blob, źródło danych musi mieć następujące wymagane właściwości:

* **Nazwa** jest unikatowa nazwa źródła danych w ramach usługi wyszukiwania.
* **Typ** musi być `azureblob`.
* **poświadczenia** zawiera parametry połączenia konta magazynu jako `credentials.connectionString` parametru. Zobacz [sposobu określania poświadczeń](#Credentials) poniżej szczegółowe informacje.
* **kontener** Określa kontener na koncie magazynu. Domyślnie wszystkie obiekty BLOB w kontenerze są pobieranie. Jeśli chcesz tylko obiekty BLOB indeksu z określonego katalogu wirtualnego, można określić tego katalogu przy użyciu opcjonalnego **zapytania** parametru.

Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [utworzyć źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak określać poświadczeń ####

Można podać poświadczenia dla kontenera obiektów blob w jeden z następujących sposobów:

- **Parametry połączenia konta magazynu pełny dostęp**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Parametry połączenia można pobrać z portalu Azure, przechodząc do bloku konto magazynu > Ustawienia > klucze (dla kont magazynu Classic) lub Ustawienia > uzyskać dostęp do kluczy (dla kont magazynu usługi Azure Resource Manager).
- **Sygnatury dostępu współdzielonego konta magazynu** ciąg połączenia (SAS): `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS powinien mieć listy i uprawnienia do odczytu z kontenerów i obiektów (obiekty BLOB w tym przypadku).
-  **Sygnatury dostępu współdzielonego kontenera**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS powinien mieć listy i Odczyt w kontenerze.

Aby uzyskać więcej informacji w magazynie udostępnionym sygnatur dostępu, zobacz [za pomocą sygnatur dostępu udostępnionego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń sygnatury dostępu Współdzielonego, konieczne będzie okresowo aktualizowany poświadczenia źródła danych za pomocą podpisów odnowionego, aby zapobiec ich wygaśnięciem. Jeśli poświadczenia SAS wygaśnie, indeksatora zakończy się niepowodzeniem z komunikatem o błędzie podobny do `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybutów i wystąpić inne konstrukcje, które kształtu wyszukiwania.

Poniżej przedstawiono sposób tworzenia indeksu z możliwością wyszukiwania `content` pola do przechowywania tekstu wyodrębniony z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Aby uzyskać więcej informacji na temat Tworzenie indeksów, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Utwórz indeksator
Indeksator łączy źródła danych z indeksem wyszukiwania docelowego, a zawiera harmonogram do automatyzowania odświeżania danych.

Po utworzeniu indeks i źródło danych, możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksatora działa tylko wtedy, gdy po jego utworzeniu. Jednak w dowolnym momencie można uruchomić indeksatora na żądanie.   

Aby uzyskać więcej szczegółów na tworzenie interfejsu API indeksatora, zapoznaj się [Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Jak usługa Azure Search indeksy obiektów blob

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex), indeksatora obiektu blob może indeksować tylko metadane magazynu (przydatne w przypadku tylko najważniejsze informacje dotyczące metadanych i nie ma potrzeby indeksu zawartości obiektów blob), magazynu i zawartości, ani zarówno metadanych i Zawartość tekstowa. Domyślnie indeksatora wyodrębnia metadane i zawartość.

> [!NOTE]
> Domyślnie obiekty BLOB z zawartością strukturalnych, takich jak JSON lub CSV są indeksowane jako pojedynczy fragmentów tekstu. Do indeksu w postaci struktury obiektów blob JSON i woluminów CSV, zobacz temat [obiektów blob JSON indeksowania](search-howto-index-json-blobs.md) i [CSV indeksowanie obiektów blob](search-howto-index-csv-blobs.md) funkcji w wersji zapoznawczej.
>
> Osadzone lub złożonego dokumentu (na przykład archiwum ZIP lub dokument programu Word z osadzonym e-mail programu Outlook zawierające załączniki) są również indeksowane jako pojedynczego dokumentu.

* Zawartość tekstowa dokumentu jest wyodrębniany do pola ciągu o nazwie `content`.

> [!NOTE]
> Usługa wyszukiwanie Azure ogranicza ilość tekstu wyodrębniane w zależności od warstwy cenowej: 32 000 znaków za darmo w warstwie, 64 000 Basic i 4 milionów do warstwy standardowa, standardowe S2 i standardowa S3. Ostrzeżenie jest dołączony do odpowiedzi stan indeksatora skróconą dokumentów.  

* Właściwości określone przez użytkownika metadanych dla obiektu blob, jeśli są wyodrębniane verbatim.
* Standardowa obiektów blob metadane właściwości są wyodrębniane do następujących pól:

  * **metadane\_magazynu\_nazwa** (typem Edm.String) — Nazwa pliku obiektu blob. Na przykład, jeśli masz /my-container/my-folder/subfolder/resume.pdf obiektów blob, wartość tego pola jest `resume.pdf`.
  * **metadane\_magazynu\_ścieżki** (typem Edm.String) — pełny identyfikator URI obiektu blob, łącznie z konta magazynu. Na przykład: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadane\_magazynu\_zawartości\_typu** (typem Edm.String) — typ określony przez kod używany do przekazania obiektu blob zawartości. Na przykład `application/octet-stream`.
  * **metadane\_magazynu\_ostatniego\_zmodyfikować** (Edm.DateTimeOffset) - Ostatnia modyfikacja sygnatury czasowej dla obiektu blob. Usługa Azure Search używa sygnatura czasowa do identyfikowania zmienionych obiektów blob, aby uniknąć indeksowanie wszystko po początkowej indeksowania.
  * **metadane\_magazynu\_rozmiar** (Edm.Int64) - wyrażony w bajtach rozmiar obiektu blob.
  * **metadane\_magazynu\_zawartości\_md5** (typem Edm.String) - wyznaczania wartości skrótu MD5 w zawartości obiektu blob, jeśli jest dostępna.
* Metadane właściwości specyficzne dla każdego formatu dokumentu są wyodrębniane do pól na liście [tutaj](#ContentSpecificMetadata).

Nie trzeba zdefiniować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — tylko przechwytywania właściwości potrzebnych aplikacji.

> [!NOTE]
> Często nazwy pól w indeksie istniejących może się różnić od nazw pól generowane podczas wyodrębniania dokumentu. Można użyć **mapowań pól** mapowania nazw właściwości udostępniane przez usługi Azure Search do nazwy pól w indeksie wyszukiwania. Przykład pola używanego mapowania poniżej zostanie wyświetlone.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowania pól
W usłudze Azure Search klucz dokumentu unikatowo identyfikuje dokumentu. Każdy indeks musi mieć dokładnie jedno pole klucza typu typem Edm.String. Pole klucza jest wymagana dla każdego dokumentu, który jest dodawany do indeksu (jest rzeczywiście tylko pole wymagane).  

Należy rozważyć wyodrębnionego pole, które powinny być mapowane na pola klucza indeksu. Kandydaci:

* **metadane\_magazynu\_nazwa** — może to być wygodny kandydujących, ale należy pamiętać, że 1) nazwy nie muszą być unikatowe, jak może mieć obiekty BLOB z tej samej nazwie w różnych folderach i 2) nazwy mogą zawierać znaki, które są nieprawidłowa w kluczach dokumentu, takich jak łączniki. Nieprawidłowe znaki mogą dotyczyć przy użyciu `base64Encode` [funkcja mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction) — Jeśli to zrobisz, pamiętaj, aby kodowania dokumentu klucze, gdy przekazując interfejsu API wywołuje takie jak wyszukiwanie. (Na przykład w środowisku .NET można użyć [metody UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) w tym celu).
* **metadane\_magazynu\_ścieżki** — przy użyciu pełnej ścieżki zapewnia unikatowość, ale ostatecznie zawiera ścieżkę `/` znaki, które są [nieprawidłowe w kluczu dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak wyżej, istnieje możliwość klucze, używając kodowania `base64Encode` [funkcja](search-indexer-field-mappings.md#base64EncodeFunction).
* Jeśli żaden z powyższych opcji działa, możesz dodać właściwości niestandardowych metadanych do obiektów blob. Ta opcja wymaga jednak procesu przekazywania obiektów blob, aby dodać właściwości metadanych do wszystkich obiektów blob. Ponieważ klucz jest wymagana właściwość, wszystkie obiekty BLOB, które nie mają tej właściwości nie zostać pomyślnie zindeksowane.

> [!IMPORTANT]
> W przypadku jawnego mapowania pola klucza w indeksie usługi wyszukiwanie Azure automatycznie używa `metadata_storage_path` jako klucz i base-64 koduje wartości klucza (druga opcja powyżej).
>
>

Na przykład umożliwia pobranie `metadata_storage_name` pole jako klucz dokumentu. Załóżmy również, że indeks ma pole klucza o nazwie `key` i pole `fileSize` do przechowywania rozmiar dokumentu. Aby okablować rzeczy się zgodnie z potrzebami, należy określić następujące mapowania pól, podczas tworzenia lub aktualizowania indeksator:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby wyświetlić to wszystko w jednym, Oto jak można dodać mapowania pól i włączyć kodowanie base-64 kluczy dla istniejącego indeksatora:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Aby dowiedzieć się więcej na temat mapowań pól, zobacz [w tym artykule](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Kontrolowanie, które obiekty BLOB są indeksowane.
Można kontrolować, które obiekty BLOB są indeksowane i które są pomijane.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indeksuj tylko obiekty BLOB z określonych rozszerzeń plików
Tylko obiekty BLOB z rozszerzeń nazw plików przy użyciu może indeksować `indexedFileNameExtensions` parametru konfiguracji indeksatora. Wartość jest ciąg zawierający rozdzielaną przecinkami listę rozszerzeń nazw plików (z początku kropką). Na przykład, aby tylko indeks. PDF i. Obiekty BLOB DOCX, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Wyklucz obiekty BLOB z określonych rozszerzeń plików
Obiekty BLOB z określonych rozszerzeń nazw plików można wykluczyć z indeksowania przy użyciu `excludedFileNameExtensions` parametru konfiguracji. Wartość jest ciąg zawierający rozdzielaną przecinkami listę rozszerzeń nazw plików (z początku kropką). Na przykład, aby wszystkie obiekty BLOB, z wyjątkiem tych z indeksu. PNG, a. Rozszerzenia JPEG, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Jeśli oba `indexedFileNameExtensions` i `excludedFileNameExtensions` parametrów, usługi Azure Search najpierw sprawdza `indexedFileNameExtensions`, następnie w `excludedFileNameExtensions`. Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się w obu list, zostanie on wykluczony z indeksowania.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrolowanie, które części obiektu blob są indeksowane.

Można kontrolować, które części obiektów blob są indeksowane przy użyciu `dataToExtract` parametru konfiguracji. Przyjmuje następujące wartości:

* `storageMetadata` — Określa, że tylko [właściwości standardowych obiektów blob i metadanych określonych przez użytkownika](../storage/blobs/storage-properties-metadata.md) są indeksowane.
* `allMetadata` — Określa, że metadane magazynu i [metadane specyficzne dla typu zawartości](#ContentSpecificMetadata) wyodrębniony z obiektu blob są indeksowane zawartości.
* `contentAndMetadata` — Określa, że wszystkie metadane i zawartość tekstową wyodrębniony z obiektu blob są indeksowane. Jest to wartość domyślna.

Na przykład aby indeksu tylko metadane magazynu, należy użyć:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Aby kontrolować sposób obiekty BLOB są indeksowane przy użyciu metadane obiektu blob

Opisane powyżej parametry konfiguracji mają zastosowanie do wszystkich obiektów blob. Czasami może zajść potrzeba kontroli jak *poszczególne obiekty BLOB* są indeksowane. Można to zrobić, dodając następujące właściwości Metadane obiektu blob i wartości:

| Nazwa właściwości | Wartość właściwości | Wyjaśnienie |
| --- | --- | --- |
| AzureSearch_Skip |wartość "prawda" |Powoduje, że indeksator obiektów blob, aby całkowicie pominąć obiektu blob. Nastąpiła wyodrębniania metadanych ani zawartości. Jest to przydatne, gdy konkretnego obiektu blob nie powiedzie się wielokrotnie i przerywa proces indeksowania. |
| AzureSearch_SkipContent |wartość "prawda" |Jest to równoważne z `"dataToExtract" : "allMetadata"` ustawienia opisane [powyżej](#PartsOfBlobToIndex) dostosowanych do określonego obiektu blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Postępowania z błędami

Domyślnie indeksatora blob zatrzymuje zaraz po napotkaniu obiektu blob o nieobsługiwanym typie zawartości (na przykład obraz). Oczywiście można użyć `excludedFileNameExtensions` parametr, aby pominąć niektóre typy zawartości. Może być jednak konieczne obiekty BLOB indeksu bez uprzedniego uzyskania informacji o wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować, indeksowania po napotkaniu nieobsługiwany typ zawartości, należy ustawić `failOnUnsupportedContentType` parametru konfiguracji `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Dla niektórych obiektów blob usługi Azure Search nie może określić typu zawartości lub nie można przetworzyć typu dokumentu w przeciwnym razie obsługiwany typ zawartości. Ignorowanie tego trybu awaryjnego, ustaw `failOnUnprocessableDocument` wartość false parametru konfiguracji:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Usługa wyszukiwanie Azure ogranicza rozmiar obiektów blob, które są indeksowane. Ograniczenia te są udokumentowane w artykule [ograniczenia usługi w usłudze Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Zbyt duże obiekty BLOB są traktowane jako błędy domyślnie. Jednak można nadal indeksowanie metadanych magazynu obiektów blob zbyt duży Jeśli ustawisz `indexStorageMetadataOnlyForOversizedDocuments` parametru konfiguracji true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Możesz także kontynuować indeksowania Jeśli błędy w dowolnym momencie przetwarzania, podczas analizowania obiektów blob lub podczas dodawania dokumenty do indeksu. Ignoruje określoną liczbę błędów, należy ustawić `maxFailedItems` i `maxFailedItemsPerBatch` parametry konfiguracji, aby odpowiednie wartości. Na przykład:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Przyrostowe wykrywania indeksowanie i usuwaniem.
Po skonfigurowaniu indeksatora obiektu blob do uruchamiania zgodnie z harmonogramem reindexes go tylko zmienionych obiektów blob, określone przez właściwość obiektu blob `LastModified` sygnatury czasowej.

> [!NOTE]
> Nie trzeba określać zasady wykrywania zmian — przyrostowe indeksowania jest włączona automatycznie.

Aby obsługuje usuwanie dokumentów, należy użyć metody "usuwania nietrwałego". Jeśli usuniesz ostatecznego obiektów blob, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania. Zamiast tego wykonaj następujące kroki:  

1. Dodawanie właściwości niestandardowych metadanych do obiektu blob, aby wskazać do usługi Azure Search logicznie usunięty
2. Skonfiguruj zasady usuwania nietrwałego wykrywania w źródle danych
3. Po przetworzeniu obiektów blob (jak pokazano przez interfejs API stanu indeksatora) indeksatora fizycznie można usunąć obiektu blob

Na przykład następujące zasady uwzględnia obiektu blob, które mają zostać usunięte, jeśli ma ona właściwości metadanych `IsDeleted` z wartością `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indeksowanie dużych zestawów danych

Indeksowanie obiektów blob może być czasochłonne. W przypadkach, gdy mają miliony obiekty BLOB do indeksu można przyspieszyć indeksowania partycjonowanie danych i używając wiele indeksatorów do przetwarzania danych równolegle. Oto, jak należy wybrać tę opcję:

- Dzielenia danych na wielu kontenerów obiektów blob lub foldery wirtualne
- Skonfiguruj kilka źródeł danych usługi Azure Search, po jednym dla każdego kontenera lub folderu. Aby wskazać folder obiektów blob, użyj `query` parametru:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Tworzenie odpowiedniego indeksatora dla każdego źródła danych. Wszystkie indeksatory może wskazywać tego samego indeksu wyszukiwania programu docelowego.  

- Jednostki wyszukiwania w usłudze można uruchomić jeden indeksator w danym momencie. Tworzenie wiele indeksatorów, zgodnie z powyższym opisem tylko jest przydatne, jeśli są one uruchamiane równolegle. Aby uruchomić wiele indeksatorów równolegle, skalowanie usługi wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Na przykład jeśli usługi wyszukiwania 6 jednostek wyszukiwania (na przykład 2 partycjach x 3 repliki), następnie 6 indeksatory może działać jednocześnie, co six-fold wzrost przepływność indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [skalować poziomy zasobów dla zapytania i obciążeń w usłudze Azure Search indeksowanie](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indeksowanie dokumentów oraz powiązane dane

Może zajść potrzeba "złóż" dokumenty z wielu źródeł w indeksie. Na przykład można scalić tekst z obiektów blob z inne metadane przechowywane w bazie danych rozwiązania Cosmos. Wypychania indeksowania interfejsu API wraz z różnych indeksatory umożliwia nawet kompilacji dokumentów wyszukiwania z wielu części. 

Aby to zrobić wszystkich indeksatorów i inne składniki muszą uzgodnić klucz dokumentu. Aby uzyskać szczegółowy przewodnik, zobacz ten artykuł, zewnętrznych: [łączenie dokumentów z innymi danymi w usłudze Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indeksowania zwykłego tekstu 

Jeśli wszystkie obiekty BLOB zawiera zwykły tekst, w tym samym kodowania, może znacznie poprawić wydajność indeksowania, przy użyciu **tekstu podczas analizowania trybu**. Aby używać podczas analizowania trybu tekstu, ustaw `parsingMode` właściwości konfiguracji `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Domyślnie `UTF-8` zakłada, że kodowania. Aby określić inne kodowanie, użyj `encoding` właściwości konfiguracji: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości
Poniższa tabela zawiera podsumowanie przetwarzania wykonywane dla każdego formatu dokumentu oraz opis właściwości metadanych wyodrębnione przez usługę Azure Search.

| Format dokumentu / typ zawartości | Właściwości typu zawartości określonych metadanych. | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Usuwanie kod znaczników HTML i wyodrębnianie tekstu |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnienie tekstu, w tym dokumenty osadzonych (z wyjątkiem obrazy) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| DOKUMENT (programu application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| PPT (aplikacji vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnienie tekstu, w tym osadzonych dokumentów |
| MSG (aplikacji vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnienie tekstu, w tym załączniki |
| ZIP (aplikacja/zip) |`metadata_content_type` |Wyodrębnienie tekstu z wszystkie dokumenty w archiwum |
| XML (aplikacja/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Usuwanie znaczników XML i wyodrębnianie tekstu |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Wyodrębnienie tekstu<br/>Uwaga: Jeśli potrzebujesz można wyodrębnić wielu pól dokumentów z obiektu blob JSON, zobacz [obiektów blob JSON indeksowania](search-howto-index-json-blobs.md) Aby uzyskać więcej informacji |
| EML (komunikat/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnienie tekstu, w tym załączniki |
| RTF (aplikacja/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Wyodrębnienie tekstu|
| Zwykłego tekstu (zwykły tekst) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Wyodrębnienie tekstu|


## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Daj nam znać, jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń w naszym [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
