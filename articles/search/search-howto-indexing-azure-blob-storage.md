---
title: Indeksowanie zawartości magazynu obiektów Blob platformy Azure w celu wyszukiwania pełnotekstowego — usługa Azure Search
description: Dowiedz się, jak indeksu usługi Azure Blob Storage i wyodrębnianie tekstu z dokumentów za pomocą usługi Azure Search.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 832be20f78d1e88a3bb6d1c25c7aaf5d7354e857
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753983"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indeksowanie dokumentów w usłudze Azure Blob Storage z usługą Azure Search
W tym artykule pokazano, jak używać usługi Azure Search do indeksowania dokumentów (takich jak pliki PDF, dokumentów programu Microsoft Office i kilka innych typowych formatów) przechowywanych w usłudze Azure Blob storage. Po pierwsze wyjaśnia podstawowe informacje dotyczące instalowania i konfigurowania indeksatora obiektów blob. Następnie oferuje bardziej zaawansowanej eksploracji zachowań, scenariuszy może wystąpić.

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów
Indeksowanie obiektów blob można wyodrębnić tekst z następujących formatów w dokumencie:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurowanie indeksowanie obiektów blob
Możesz skonfigurować indeksator usługi Azure Blob Storage za pomocą:

* [Azure Portal](https://ms.portal.azure.com)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Usługa Azure Search [zestawu SDK platformy .NET](https://aka.ms/search-sdk)

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pola) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>

Tutaj pokażemy przepływu przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1: Tworzenie źródła danych
Źródło danych określa danych do indeksu poświadczeń wymaganych do dostępu do danych i zasady, aby efektywnie zidentyfikować zmiany danych (nowe, zmodyfikowane lub usunięte wiersze). Źródło danych może służyć przez wiele indeksatorów w tej samej usługi wyszukiwania.

Indeksowanie obiektów blob, aby uzyskać źródła danych musi mieć następujące wymagane właściwości:

* **Nazwa** unikatowa nazwa źródła danych w ramach usługi wyszukiwania.
* **Typ** musi być `azureblob`.
* **poświadczenia** zawiera parametry połączenia konta magazynu jako `credentials.connectionString` parametru. Zobacz [sposobu określania poświadczeń](#Credentials) poniżej szczegółowe informacje.
* **kontener** Określa kontener na koncie magazynu. Domyślnie wszystkie obiekty BLOB w kontenerze są możliwe do pobierania. Jeśli chcesz tylko do obiektów blob indeksu, w określonym katalogu wirtualnego, można określić tego katalogu przy użyciu opcjonalnego **zapytania** parametru.

Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak określić poświadczenia ####

Można podać poświadczenia dla kontenera obiektów blob w jednym z następujących sposobów:

- **Parametry połączenia konta magazynu pełny dostęp**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Parametry połączenia można uzyskać w witrynie Azure portal, przechodząc do bloku konto magazynu > Ustawienia > klucze (dla kont magazynu Classic) lub Ustawienia > klucze (dla kont magazynu usługi Azure Resource Manager) dostępu.
- **Sygnatura dostępu współdzielonego konta magazynu** parametry połączenia (SAS): `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` Sygnatury dostępu Współdzielonego powinny mieć listy i uprawnienia do odczytu z kontenerów i obiektów (obiekty BLOB w tym przypadku).
-  **Sygnatury dostępu współdzielonego kontenera**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` Sygnatury dostępu Współdzielonego powinny mieć listy i uprawnienia do odczytu w kontenerze.

Aby uzyskać więcej informacji na magazyn udostępniony sygnatury dostępu, zobacz [za pomocą udostępnionej sygnatury dostępu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń sygnatury dostępu Współdzielonego, należy okresowo aktualizowany poświadczenia źródła danych za pomocą odnowione podpisów, aby zapobiec ich wygaśnięciem. Jeśli poświadczenia sygnatury dostępu Współdzielonego wygasają, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobny do `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i innych konstrukcji, które kształt wyszukiwania środowiska.

Poniżej przedstawiono sposób, by utworzyć indeks z możliwością wyszukiwania `content` pola do przechowywania tekstu wyodrębnionego z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zapewnia harmonogram w celu zautomatyzowania odświeżania danych.

Po utworzeniu indeks i źródło danych możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiona na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalnie — w przypadku pominięcia, indeksatora jest uruchamiany tylko raz, po jego utworzeniu. Jednak w dowolnym momencie można uruchomić indeksatora na żądanie.   

Aby uzyskać szczegółowe informacje na temat tworzenia interfejsu API indeksatora, zapoznaj się [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji na temat definiowania harmonogramy indeksatora zobacz [sposób tworzenia harmonogramu indeksatorów usługi Azure Search](search-howto-schedule-indexers.md).

## <a name="how-azure-search-indexes-blobs"></a>Jak usługa Azure Search indeksuje obiektów blob

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex), indeksatora obiektów blob będzie mogła indeksować tylko metadane magazynu (przydatne, gdy tylko interesujące Cię metadane i nie wymagają do indeksowania zawartości obiektów blob), magazynu i zawartości metadanych lub zarówno metadane i Zawartość tekstowa. Domyślnie indeksator wyodrębnia metadane i zawartość.

> [!NOTE]
> Domyślnie obiekty BLOB z zawartością strukturalnych, takich jak JSON lub CSV są indeksowane jako jeden fragment tekstu. Indeksowanie obiektów blob JSON i woluminów CSV w taki sposób, ze strukturą, zobacz temat [JSON indeksowanie obiektów blob](search-howto-index-json-blobs.md) i [CSV indeksowanie obiektów blob](search-howto-index-csv-blobs.md) Aby uzyskać więcej informacji.
>
> Złożone lub osadzonego dokumentu (np. archiwum ZIP lub dokument programu Word z osadzonym zawierające załączniki wiadomości e-mail programu Outlook) są również indeksowane, jako pojedynczy dokument.

* Zawartość tekstowa dokumentu jest następnie wyodrębniany do pola ciągu o nazwie `content`.

> [!NOTE]
> Usługa Azure Search ogranicza ilość tekstu wyodrębnia w zależności od warstwy cenowej: 32000 znaków bezpłatnie warstwy, 64 000 for podstawowe i 4 mln dla warstwy standardowa, — warstwa standardowa S2 i S3 standardowych. Ostrzeżenie znajduje się w odpowiedzi stan indeksatora obcięte dokumentów.  

* Właściwości określone przez użytkownika metadanych dla obiektu blob, jeśli są wyodrębniane dosłowne wyrażenie.
* Właściwości metadanych obiektu blob Standard są wyodrębniane do następujących pól:

  * **metadane\_magazynu\_nazwa** (Edm.String) — Nazwa pliku obiektu blob. Na przykład, jeśli masz /my-container/my-folder/subfolder/resume.pdf obiektów blob, wartość tego pola jest `resume.pdf`.
  * **metadane\_magazynu\_ścieżki** (Edm.String) — pełny identyfikator URI obiektu blob, w tym konto magazynu. Na przykład: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadane\_magazynu\_zawartości\_typu** (Edm.String) — typ określony przez kod używany do przekazania obiektu blob zawartości. Na przykład `application/octet-stream`.
  * **metadane\_magazynu\_ostatniego\_zmodyfikowane** (Edm.DateTimeOffset) - Ostatnia modyfikacja sygnaturę czasową dla obiektu blob. Usługa Azure Search używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystko po początkowej indeksowania.
  * **metadane\_magazynu\_rozmiar** (Edm.Int64) — obiekt blob rozmiar w bajtach.
  * **metadane\_magazynu\_zawartości\_md5** (Edm.String) — Skrót MD5 zawartość obiektu blob, jeśli jest dostępny.
  * **metadane\_magazynu\_sygnatury dostępu współdzielonego\_tokenu** (Edm.String) - tymczasowe tokenu sygnatury dostępu Współdzielonego, który może być używany przez [umiejętności niestandardowe](cognitive-search-custom-skill-interface.md) do uzyskania dostępu do obiektu blob. Ten token nie powinny być przechowywane do późniejszego użycia, ponieważ może ona wygaśnie.

* Metadane właściwości specyficzne dla każdego format dokumentu są wyodrębniane do pól na liście [tutaj](#ContentSpecificMetadata).

Nie ma potrzeby definiowania wszystkich powyższych właściwości pola w indeksie wyszukiwania — po prostu Przechwytywanie właściwości, czego potrzebujesz do aplikacji.

> [!NOTE]
> Nazwy pól w indeksie istniejących będzie często różni się od nazwy pól wygenerowanych podczas wyodrębniania dokumentu. Możesz użyć **mapowania pól** do mapowania nazw właściwości udostępniane przez usługę Azure Search do nazw pól w indeksie wyszukiwania. Widoczne są przykładem pola, które przy mapowaniach poniżej.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowań pól
W usłudze Azure Search klucz dokumentu jednoznacznie identyfikuje dokumentu. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu Edm.String. Pole klucza jest wymagana dla każdego dokumentu, który jest dodawany do indeksu (jest naprawdę jedyne wymagane pole).  

Należy rozważyć wyodrębnione pole, które powinny być mapowane na pola klucza indeksu. Kandydaci to:

* **metadane\_magazynu\_nazwa** — może to być wygodna Release candidate, ale należy pamiętać, że 1) nazwy nie muszą być unikatowe, ponieważ może mieć obiektów blob za pomocą tej samej nazwie w różnych folderach, a (2) nazwa może zawierać znaków, które są nieprawidłowa w kluczach dokumentu, takich jak kreski. Poradzenie sobie z nieprawidłowych znaków za pomocą `base64Encode` [pola mapowania funkcji](search-indexer-field-mappings.md#base64EncodeFunction) — Jeśli to zrobisz, pamiętaj, aby kodowanie kluczy dokumentu, podczas przekazywania ich w interfejsie API wywołuje takie jak wyszukiwanie. (Na przykład na platformie .NET należy użyć [metoda UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) do tego celu).
* **metadane\_magazynu\_ścieżki** — użycie pełnej ścieżki gwarantuje unikatowość, ale zdecydowanie zawiera ścieżkę `/` znaki, które są [nieprawidłowy klucz dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak powyżej, istnieje możliwość kluczy przy użyciu kodowania `base64Encode` [funkcja](search-indexer-field-mappings.md#base64EncodeFunction).
* Jeśli żaden z powyższych opcji działa, można dodać właściwości niestandardowych metadanych do obiektów blob. Ta opcja wymaga jednak proces przekazywania obiektów blob, można dodać właściwości metadanych do wszystkich obiektów blob. Ponieważ klucz jest wymagana właściwość, wszystkie obiekty BLOB, które nie mają tej właściwości nie zostać pomyślnie zindeksowane.

> [!IMPORTANT]
> W przypadku jawnego mapowania pola klucza w indeksie usługi Azure Search automatycznie używa `metadata_storage_path` jako klucz i base-64 koduje wartości klucza (druga opcja powyżej).
>
>

W tym przykładzie możliwości wybrania `metadata_storage_name` pola jako klucz dokumentu. Załóżmy również indeksu ma pole klucza o nazwie `key` i pole `fileSize` do przechowywania rozmiaru dokumentu. Aby powiązać elementy w górę zgodnie z potrzebami, należy określić następujące mapowania pola podczas tworzenia lub aktualizowania indeksator:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby wyświetlić to wszystko w jednym, Oto jak można dodać mapowania pól i włączyć kodowanie base-64 kluczy dla istniejącego indeksatora:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
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

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indeks tylko obiektów blob za pomocą określonych rozszerzeń plików
Umożliwia indeksowanie tylko obiektów blob za pomocą rozszerzeń nazw plików, należy określić za pomocą `indexedFileNameExtensions` parametru konfiguracji indeksatora. Wartość jest ciąg zawierający rozdzielaną przecinkami listę rozszerzeń nazw plików (z wiodących kropką). Na przykład, aby tylko indeks. Pliki PDF i. Obiekty BLOB DOCX, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Wykluczanie obiektów blob za pomocą określonych rozszerzeń plików
Można wykluczyć obiektów blob za pomocą określonych rozszerzeń nazw plików z indeksowania za pomocą `excludedFileNameExtensions` parametru konfiguracji. Wartość jest ciąg zawierający rozdzielaną przecinkami listę rozszerzeń nazw plików (z wiodących kropką). Na przykład, aby wszystkie obiekty BLOB, z wyjątkiem tych z indeksu. PNG i. Rozszerzenia JPEG, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Jeśli oba `indexedFileNameExtensions` i `excludedFileNameExtensions` parametrów, usługa Azure Search, ale najpierw analizuje `indexedFileNameExtensions`, następnie w `excludedFileNameExtensions`. Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się w obu list, będzie on wykluczony z indeksowania.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrolowanie, które części obiektu blob są indeksowane.

Można kontrolować, które części obiektu blob są indeksowane, za pomocą `dataToExtract` parametru konfiguracji. Może upłynąć następujące wartości:

* `storageMetadata` -Określa, że tylko [właściwości standardowych obiektów blob i metadanych określone przez użytkownika](../storage/blobs/storage-properties-metadata.md) są indeksowane.
* `allMetadata` -Określa metadane magazynu i [określonych metadanych typu zawartości](#ContentSpecificMetadata) wyodrębnione z obiektu blob są indeksowane zawartości.
* `contentAndMetadata` -Określa, czy wszystkich metadanych i zawartości tekstowej wyodrębnione z obiektu blob są indeksowane. Jest to wartość domyślna.

Na przykład aby indeksować tylko metadane magazynu, należy użyć:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Przy użyciu metadanych obiektu blob do kontrolowania, jak obiekty BLOB są indeksowane

Parametry konfiguracji opisanych powyżej mają zastosowanie do wszystkich obiektów blob. Czasami możesz chcieć kontroli sposobu *poszczególne obiekty BLOB* są indeksowane. Można to zrobić, dodając następujące właściwości metadanych obiektu blob i wartości:

| Nazwa właściwości | Wartość właściwości | Wyjaśnienie |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Powoduje, że indeksatora obiektów blob, aby całkowicie pominąć obiektu blob. Wyodrębnianie metadanych ani zawartość nie zostanie podjęta. Jest to przydatne, gdy określonego obiektu blob nie powiedzie się wielokrotnie i przerwanie procesu indeksowania. |
| AzureSearch_SkipContent |"true" |Jest to równoważne z `"dataToExtract" : "allMetadata"` ustawienia opisane [powyżej](#PartsOfBlobToIndex) ograniczone do określonego obiektu blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Obsługa błędów

Domyślnie indeksatora obiektów blob zatrzymuje się zaraz po napotkaniu obiektu blob o nieobsługiwanym typie zawartości (na przykład obraz). Oczywiście można użyć `excludedFileNameExtensions` parametru, aby pominąć niektóre typy zawartości. Może być jednak konieczne indeksu obiektów blob bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować, indeksowanie po napotkaniu nieobsługiwanym typie zawartości, należy ustawić `failOnUnsupportedContentType` parametr konfiguracji `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Dla niektórych obiektów blob usługi Azure Search jest w stanie określić typu zawartości lub nie można przetworzyć typu dokumentu w przeciwnym razie obsługiwany typ zawartości. Aby zignorować ten tryb błędu, należy ustawić `failOnUnprocessableDocument` wartość false dla parametru konfiguracji:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Usługa Azure Search limity rozmiaru obiektów blob, które są indeksowane. Te limity są udokumentowane w artykule [limity usługi w usłudze Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Zbyt duże obiekty BLOB są traktowane jako błędy domyślnie. Jednakże, można nadal zaindeksować metadane magazynu obiektów blob w dużych Jeśli ustawisz `indexStorageMetadataOnlyForOversizedDocuments` parametru konfiguracji na wartość true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Nadal m ożna również indeksowania Jeśli błędy występować w dowolnym momencie przetwarzania podczas analizowania obiektów blob lub podczas dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić `maxFailedItems` i `maxFailedItemsPerBatch` odpowiednie wartości parametrów konfiguracji. Na przykład:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Przyrostowe wykrywanie indeksowania i usuwanie
Po skonfigurowaniu indeksatora obiektów blob do uruchomienia zgodnie z harmonogramem indeksuje ponownie tylko zmienionych obiektów blob, zgodnie z ustaleniami obiektu blob `LastModified` sygnatury czasowej.

> [!NOTE]
> Nie trzeba określać zasady wykrywania zmian — Indeksowanie przyrostowe jest włączana automatycznie dla Ciebie.

Aby obsługiwać usuwanie dokumentów, należy skorzystać z podejścia "usuwania nietrwałego". Usunięcie obiektów BLOB od razu wykupić odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania. Zamiast tego wykonaj następujące czynności:  

1. Dodawanie właściwości niestandardowych metadanych do obiektów blob, aby wskazać do usługi Azure Search logicznie usunięty
2. Skonfiguruj zasady wykrywania usuwania nietrwałego w źródle danych
3. Po przetworzeniu indeksatora obiektów blob (opisane przez interfejs API stanu indeksatora) fizycznie można usunąć obiektu blob

Na przykład, następujące zasady uwzględnia obiektu blob do usunięcia, jeśli ma ona właściwość metadanych `IsDeleted` wartością `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
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

Indeksowanie obiektów blob może być czasochłonne. W przypadkach, w którym masz milionów obiektów blob do indeksowania można przyspieszyć indeksowania, partycjonowanie danych i używając wiele indeksatorów do przetwarzania danych w sposób równoległy. Poniżej przedstawiono, jak możesz skonfigurować to:

- Podzielić dane na wielu kontenerów obiektów blob lub foldery wirtualne
- Skonfiguruj kilka źródeł danych usługi Azure Search, po jednym w każdym kontener lub folder. Wskaż folder obiektów blob, użyj `query` parametru:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Utworzenie odpowiedniego indeksatora dla każdego źródła danych. Wszystkie indeksatory może wskazywać na tym samym docelowym indeksem wyszukiwania.  

- Jedna jednostka wyszukiwania w usłudze można uruchomić jeden indeksator w danym momencie. Tworzenie wiele indeksatorów, zgodnie z powyższym opisem jest przydatna, jeśli są faktycznie wykonywane równolegle. Aby uruchomić wiele indeksatorów w sposób równoległy, skalowanie w poziomie usługi wyszukiwania, tworząc odpowiedniej liczby partycji i replik. Na przykład jeśli usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 partycjach x 3 repliki), następnie 6 indeksatory można uruchomić jednocześnie skutkuje six-fold wzrost przepływność indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowanie pojemności, zobacz [poziomy skalowania zasobów dla zapytań i indeksowania obciążeń w usłudze Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indeksowanie dokumentów, wraz z powiązanych danych

Możesz chcieć "złożyć" dokumenty z wielu źródeł w indeksie. Na przykład można scalić tekstu z obiektów blob z inne metadane przechowywane w usłudze Cosmos DB. Nawet służy wypychania indeksowania interfejsu API wraz z różnych indeksatorów do tworzenia dokumentów wyszukiwania z wielu części. 

Aby to działało wszystkie indeksatory i inne składniki muszą uzgodnić klucz dokumentu. Szczegółowy przewodnik znajduje się w artykule zewnętrznych: [Łączenie dokumentów z innymi danymi w usłudze Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indeksowanie Zwykły tekst 

Jeśli wszystkie obiekty BLOB zawierają zwykły tekst, w tym samym kodowaniem, może znacznie poprawić wydajność indeksowania, przy użyciu **tekstu podczas analizowania trybu**. Aby użyć tryb analizy tekstu, ustaw `parsingMode` właściwości konfiguracji `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Domyślnie `UTF-8` zakłada, że kodowania. Aby określić inne kodowanie, użyj `encoding` właściwość konfiguracji: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości
Poniższa tabela zawiera podsumowanie przetwarzania wykonywane dla każdego dokumentu formatu i zawiera opis właściwości metadanych wyodrębnionych przez usługę Azure Search.

| Format dokumentu / typ zawartości | Właściwości określonych metadanych typu zawartości | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Usuń kod znaczników HTML i wyodrębnianie tekstu |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnij tekst, łącznie z osadzone dokumenty (z wyjątkiem obrazów) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| DOC (aplikacja / (MS Word)) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, łącznie z osadzone dokumenty |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnienie tekstu, w tym z załączników |
| ZIP (aplikacja/zip) |`metadata_content_type` |Wyodrębnianie tekstu z wszystkich dokumentów w archiwum |
| XML (aplikacja/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Usuń znacznik XML i wyodrębnianie tekstu |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Wyodrębnij tekst<br/>UWAGA: Jeśli zachodzi potrzeba wyodrębnić wielu pól dokumentu z obiektu blob JSON, zobacz [JSON indeksowanie obiektów blob](search-howto-index-json-blobs.md) Aby uzyskać szczegółowe informacje |
| EML (komunikat/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnienie tekstu, w tym z załączników |
| RTF (aplikacja/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Wyodrębnij tekst|
| Zwykłego tekstu (zwykły tekst) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Wyodrębnij tekst|


## <a name="help-us-make-azure-search-better"></a>Pomóż nam ulepszyć usługę Azure Search
Daj nam znać, jeśli masz sugestie funkcji lub pomysły dotyczące ulepszeń w naszym [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
