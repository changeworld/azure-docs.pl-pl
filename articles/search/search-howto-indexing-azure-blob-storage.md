---
title: Indeksowanie zawartości usługi Azure Blob Storage na potrzeby wyszukiwania pełnotekstowego — Azure Search
description: Dowiedz się, jak indeksować Blob Storage platformy Azure i wyodrębnić tekst z dokumentów z Azure Search.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 03f828be603720871672b9b5d90eb87dd283c002
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842542"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indeksowanie dokumentów na platformie Azure Blob Storage przy użyciu Azure Search
W tym artykule pokazano, jak używać Azure Search do indeksowania dokumentów (takich jak pliki PDF, dokumenty Microsoft Office i inne popularne formaty) przechowywane w usłudze Azure Blob Storage. Najpierw objaśnia podstawowe informacje na temat konfigurowania i konfigurowania indeksatora obiektów BLOB. Następnie oferuje dokładniejszą eksplorację zachowań i scenariuszy, które prawdopodobnie napotkasz.

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów
Indeksator obiektów BLOB może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurowanie indeksowania obiektów BLOB
Można skonfigurować usługę Azure Blob Storage indeksator przy użyciu:

* [Azure Portal](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Azure Search
* [Zestaw SDK Azure Search .NET](https://aka.ms/search-sdk)

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pól) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>

W tym miejscu zademonstrowano przepływ przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1: Utwórz źródło danych
Źródło danych określa, które dane mają być indeksowane, które są potrzebne do uzyskiwania dostępu do danych, oraz zasady umożliwiające wydajne identyfikowanie zmian w danych (nowych, zmodyfikowanych lub usuniętych wierszy). Źródło danych może być używane przez wiele indeksatorów w tej samej usłudze wyszukiwania.

W przypadku indeksowania obiektów BLOB źródło danych musi mieć następujące wymagane właściwości:

* **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
* **Typ** musi być `azureblob`.
* **poświadczenia** udostępniają parametry połączenia konta magazynu jako `credentials.connectionString` parametr. Aby uzyskać szczegółowe informacje [, zobacz Jak określić poświadczenia](#Credentials) poniżej.
* **kontener** Określa kontener na koncie magazynu. Domyślnie można pobrać wszystkie obiekty blob w kontenerze. Jeśli chcesz tylko indeksować obiekty blob w konkretnym katalogu wirtualnym, możesz określić ten katalog przy użyciu opcjonalnego parametru **zapytania** .

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

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Create DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak określić poświadczenia ####

Poświadczenia dla kontenera obiektów BLOB można podać w jeden z następujących sposobów:

- **Parametry połączenia z pełnymi dostępem do konta magazynu**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`Parametry połączenia można uzyskać z Azure Portal, przechodząc do bloku konto magazynu > Ustawienia > klucze (dla klasycznych kont magazynu) lub Ustawienia > klucze dostępu (dla Azure Resource Manager kont magazynu).
- **Sygnatura dostępu współdzielonego konta magazynu** (SAS) parametry połączenia: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu w kontenerach i obiektach (w tym przypadku obiektów BLOB).
-  **Sygnatura dostępu współdzielonego kontenera**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu kontenera.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> W przypadku używania poświadczeń SAS należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator zakończy się niepowodzeniem z `Credentials provided in the connection string are invalid or have expired.`komunikatem o błędzie podobnym do.  

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z `content` polem z możliwością wyszukiwania, aby przechowywać tekst wyodrębniony z obiektów blob:   

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

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Utwórz indeksator
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram do automatyzowania odświeżania danych.

Po utworzeniu indeksu i źródła danych można rozpocząć tworzenie indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z tematem [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla Azure Search](search-howto-schedule-indexers.md).

## <a name="how-azure-search-indexes-blobs"></a>Jak Azure Search indeksów obiektów BLOB

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex)indeksator obiektów BLOB może indeksować tylko metadane magazynu (przydatne w przypadku ponoszenia informacji o metadanych i braku potrzeby indeksowania zawartości obiektów BLOB), magazynu i metadanych zawartości, a także metadanych i zawartości tekstowej. Domyślnie indeksator wyodrębnia zarówno metadane, jak i zawartość.

> [!NOTE]
> Domyślnie obiekty blob o zawartości strukturalnej, takiej jak JSON lub CSV, są indeksowane jako pojedynczy fragment tekstu. Jeśli chcesz indeksować obiekty blob JSON i CSV w uporządkowany sposób, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) i [indeksowanie obiektów BLOB woluminów CSV](search-howto-index-csv-blobs.md) , aby uzyskać więcej informacji.
>
> Dokument złożony lub osadzony (na przykład archiwum ZIP lub dokument programu Word z osadzoną pocztą e-mail programu Outlook zawierający załączniki) również jest indeksowany jako pojedynczy dokument.

* Zawartość tekstowa dokumentu jest wyodrębniana do pola ciągu o nazwie `content`.

> [!NOTE]
> Azure Search ogranicza liczbę wyodrębnianych tekstów w zależności od warstwy cenowej: 32 000 znaków dla warstwy Bezpłatna, 64 000 dla warstwy Podstawowa i 4 000 000 dla warstw Standard, standard S2 i Standard S3. Ostrzeżenie jest zawarte w odpowiedzi stanu indeksatora dla obciętych dokumentów.  

* Właściwości metadanych określone przez użytkownika w obiekcie BLOB (jeśli istnieją) są wyodrębniane Verbatim.
* Standardowe właściwości metadanych obiektów BLOB są wyodrębniane do następujących pól:

  * **nazwa\_magazynu\_metadanych** (EDM. String) — nazwa pliku obiektu BLOB. Na przykład jeśli masz obiekt BLOB/my-Container/my-folder/subfolder/Resume.PDF, wartość tego pola to `resume.pdf`.
  * **ścieżka\_magazynu\_metadanych** (EDM. String) — pełny identyfikator URI obiektu BLOB, w tym konto magazynu. Na przykład: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **Typ\_zawartościmagazynu\_metadanych (EDM. String) — typ zawartości określony przez kod, który został użyty do przekazania obiektu BLOB.\_** Na przykład `application/octet-stream`.
  * **\_ostatnio\_modyfikowanymagazynmetadanych(EDM.DateTimeOffset)—sygnaturaczasowaostatniejmodyfikacjidlaobiektuBLOB.\_** Azure Search używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystkiego po początkowym indeksowaniu.
  * **rozmiar\_magazynu\_metadanych** (EDM. Int64) — rozmiar obiektu BLOB w bajtach.
  * **wartość\_MD5\_zawartościmagazynu\_metadanych** (EDM. String) — skrót MD5 zawartości obiektu BLOB, jeśli jest dostępny.
  * **token\_SASmagazynu\_metadanych (EDM. String) — tymczasowy token SAS, który może być używany przez niestandardowe umiejętności w celu uzyskania dostępu do obiektu BLOB.\_** [](cognitive-search-custom-skill-interface.md) Ten token nie powinien być przechowywany do późniejszego użycia, ponieważ mógł wygasnąć.

* Właściwości metadanych specyficzne dla każdego formatu dokumentu są wyodrębniane do pól wymienionych w [tym miejscu](#ContentSpecificMetadata).

Nie musisz definiować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — wystarczy przechwycić właściwości potrzebne dla aplikacji.

> [!NOTE]
> Często nazwy pól w istniejącym indeksie będą różnić się od nazw pól generowanych podczas wyodrębniania dokumentu. **Mapowania pól** można użyć do mapowania nazw właściwości dostarczonych przez Azure Search do nazw pól w indeksie wyszukiwania. Zobaczysz przykładowe mapowania pól poniżej.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowań pól
W Azure Search klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu EDM. String. Pole klucza jest wymagane dla każdego dokumentu dodawanego do indeksu (w rzeczywistości jest to jedyne pole wymagane).  

Należy uważnie rozważyć, które wyodrębnione pole powinno być mapowane na pole klucza dla indeksu. Kandydaci są:

* **nazwa\_magazynu\_metadanych** — może to być wygodny kandydat, ale należy zauważyć, że 1) nazwy mogą nie być unikatowe, ponieważ mogą istnieć obiekty blob o takiej samej nazwie w różnych folderach i 2) nazwa może zawierać nieprawidłowe znaki w kluczach dokumentów, takich jak łączniki. Za pomocą `base64Encode` [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction) można zajmować się nieprawidłowymi znakami — Jeśli to zrobisz, pamiętaj, aby zakodować klucze dokumentu podczas przekazywania ich w wywołaniach interfejsu API, takich jak odnośnik. (Na przykład w programie .NET można użyć w tym celu [metody UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) ).
* **ścieżka\_magazynu\_metadanych** — przy użyciu pełnej ścieżki zapewnia unikatowość, ale ścieżka w nieskończoność `/` zawiera znaki, które są [nieprawidłowe w kluczu dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak powyżej, można zakodować klucze przy użyciu `base64Encode` [funkcji](search-indexer-field-mappings.md#base64EncodeFunction).
* Jeśli żadna z powyższych opcji nie zadziałała, możesz dodać niestandardową Właściwość metadanych do obiektów BLOB. Ta opcja wymaga jednak, aby proces przekazywania obiektów BLOB mógł dodać tę właściwość metadanych do wszystkich obiektów BLOB. Ponieważ klucz jest wymaganą właściwością, wszystkie obiekty blob, które nie mają tej właściwości, będą kończyć się niepowodzeniem.

> [!IMPORTANT]
> Jeśli nie istnieje jawne mapowanie pola klucza w indeksie, Azure Search automatycznie używa `metadata_storage_path` jako klucza i Base-64 wartości klucza kodu (druga opcja powyżej).
>
>

Na potrzeby tego przykładu wybierzemy `metadata_storage_name` pole jako klucz dokumentu. Załóżmy również, że indeks ma pole klucza o nazwie `key` i pole `fileSize` do przechowywania rozmiaru dokumentu. Aby odpowiednio podpracować, określ następujące mapowania pól podczas tworzenia lub aktualizowania indeksatora:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby to zrobić, możesz dodać mapowania pól i włączyć kodowanie Base-64 kluczy dla istniejącego indeksatora:

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
> Aby dowiedzieć się więcej o mapowaniu pól, zobacz [ten artykuł](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Kontrolowanie, które obiekty blob są indeksowane
Można kontrolować, które obiekty blob są indeksowane i które są pomijane.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indeksuj tylko obiekty blob z określonymi rozszerzeniami plików
Można indeksować tylko obiekty blob z rozszerzeniami nazw plików, które można określić za `indexedFileNameExtensions` pomocą parametru konfiguracji indeksatora. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby zindeksować tylko. PDF i. Pliki BLOB DOCX, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Wyklucz obiekty blob z określonymi rozszerzeniami plików
Można wykluczyć obiekty blob z określonymi rozszerzeniami nazw plików z indeksowania przy użyciu `excludedFileNameExtensions` parametru konfiguracji. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby indeksować wszystkie obiekty blob z wyjątkiem. PNG i. Rozszerzenia JPEG, wykonaj następujące czynności:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Jeśli oba `indexedFileNameExtensions` parametry `excludedFileNameExtensions` i są obecne `indexedFileNameExtensions`, Azure Search najpierw wyszukać, następnie `excludedFileNameExtensions`o. Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wyłączone z indeksowania.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrolowanie, które części obiektu BLOB są indeksowane

Można kontrolować, które fragmenty obiektów BLOB są indeksowane przy użyciu `dataToExtract` parametru konfiguracji. Może przyjmować następujące wartości:

* `storageMetadata`-Określa, że indeksowane są tylko [standardowe właściwości obiektów blob i metadane określone przez użytkownika](../storage/blobs/storage-properties-metadata.md) .
* `allMetadata`-Określa, że metadane magazynu i [metadane specyficzne dla typu zawartości](#ContentSpecificMetadata) wyodrębnione z zawartości obiektu BLOB są indeksowane.
* `contentAndMetadata`-Określa, że wszystkie metadane i zawartość tekstowa wyodrębnione z obiektu BLOB są indeksowane. Jest to wartość domyślna.

Na przykład aby zindeksować tylko metadane magazynu, użyj:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Używanie metadanych obiektów BLOB do kontrolowania sposobu indeksowania obiektów BLOB

Opisane powyżej parametry konfiguracji dotyczą wszystkich obiektów BLOB. Czasami może być konieczne sterowanie sposobem indeksowania *poszczególnych obiektów BLOB* . Można to zrobić, dodając następujące właściwości i wartości metadanych obiektu BLOB:

| Nazwa właściwości | Wartość właściwości | Wyjaśnienie |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Instruuje indeksator obiektu BLOB, aby całkowicie pominąć obiekt BLOB. Nie podjęto próby przeprowadzenia żadnej metadanych ani wyodrębniania zawartości. Jest to przydatne, gdy konkretny obiekt BLOB powtarza się wielokrotnie i przerywa proces indeksowania. |
| AzureSearch_SkipContent |"true" |Jest to odpowiednik `"dataToExtract" : "allMetadata"` ustawienia opisanego [powyżej](#PartsOfBlobToIndex) w zakresie określonego obiektu BLOB. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Rozpatrywanie błędów

Domyślnie indeksator obiektu BLOB jest zatrzymywany zaraz po napotkaniu obiektu BLOB z nieobsługiwanym typem zawartości (na przykład obrazem). Możesz użyć parametru, `excludedFileNameExtensions` aby pominąć niektóre typy zawartości. Jednak może być konieczne indeksowanie obiektów Blob bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować indeksowanie, gdy zostanie napotkany nieobsługiwany typ zawartości `failOnUnsupportedContentType` , ustaw parametr `false`konfiguracji na:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

W przypadku niektórych obiektów Blob Azure Search nie może określić typu zawartości lub nie można przetworzyć dokumentu z nieobsługiwanym typem zawartości. Aby zignorować ten tryb niepowodzenia, ustaw `failOnUnprocessableDocument` dla parametru konfiguracji wartość false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search ogranicza rozmiar indeksowanych obiektów BLOB. Limity te są udokumentowane w [limitach usługi w Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Zbyt duże obiekty blob są domyślnie traktowane jako błędy. Można jednak nadal indeksować metadane magazynu o zwiększonym rozmiarze obiektów BLOB w `indexStorageMetadataOnlyForOversizedDocuments` przypadku ustawienia dla parametru konfiguracji wartości true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Możesz również kontynuować indeksowanie w przypadku wystąpienia błędów w dowolnym momencie przetwarzania, podczas analizowania obiektów blob lub dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić `maxFailedItems` wymagane wartości parametrów i `maxFailedItemsPerBatch` konfiguracji. Na przykład:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie przyrostowe i usuwanie
W przypadku skonfigurowania indeksatora obiektu BLOB do uruchamiania zgodnie z harmonogramem program ponownie indeksuje tylko zmienione obiekty blob, zgodnie z `LastModified` sygnaturą czasową obiektu BLOB.

> [!NOTE]
> Nie trzeba określać zasad wykrywania zmian — indeksowanie przyrostowe jest automatycznie włączone.

Aby obsługiwać usuwanie dokumentów, użyj podejścia "usuwanie nietrwałe". Po usunięciu obiektów blob z prawej strony, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania. Zamiast tego należy wykonać następujące czynności:  

1. Dodaj niestandardową Właściwość metadanych do obiektu BLOB, aby wskazać, Azure Search został usunięty logicznie
2. Konfigurowanie zasad wykrywania usuwania nietrwałego w źródle danych
3. Gdy indeksator przetworzy obiekt BLOB (jak pokazano w interfejsie API stanu indeksatora), można fizycznie usunąć obiekt BLOB

Na przykład następujące zasady uznają obiekt BLOB, który ma zostać usunięty, jeśli ma właściwość `IsDeleted` metadanych o wartości: `true`

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

Indeksowanie obiektów BLOB może być czasochłonnym procesem. W przypadkach, gdy masz miliony obiektów BLOB do indeksowania, możesz przyspieszyć indeksowanie, partycjonowanie danych i używanie wielu indeksatorów do równoległego przetwarzania danych. Oto, jak można je skonfigurować:

- Partycjonowanie danych w wielu kontenerach obiektów blob lub w folderach wirtualnych
- Skonfiguruj kilka Azure Search źródeł danych, jeden dla każdego kontenera lub folderu. Aby wskazać folder obiektu BLOB, użyj `query` parametru:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Utwórz odpowiedni indeksator dla każdego źródła danych. Wszystkie indeksatory mogą wskazywać na ten sam docelowy indeks wyszukiwania.  

- Jedna jednostka wyszukiwania w usłudze może uruchamiać jeden indeksator w danym momencie. Tworzenie wielu indeksatorów, jak opisano powyżej, jest przydatne tylko wtedy, gdy rzeczywiście są uruchamiane równolegle. Aby uruchamiać wiele indeksatorów równolegle, Skaluj w poziomie usługę wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Jeśli na przykład usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 repliki x 3), to 6 indeksatorów może być uruchomionych jednocześnie, co powoduje sześć przyrostów przepływności indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [poziomy zasobów skalowania dla obciążeń zapytań i indeksowania w Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indeksowanie dokumentów wraz z powiązanymi danymi

Możesz chcieć "złożyć" dokumenty z wielu źródeł w indeksie. Na przykład możesz chcieć scalić tekst z obiektów blob z innymi metadanymi przechowywanymi w Cosmos DB. Można nawet użyć interfejsu API indeksowania wypychania razem z różnymi indeksatorami w celu utworzenia dokumentów wyszukiwania z wielu części. 

Aby to działało, wszystkie indeksatory i inne składniki muszą wyrazić zgodę na klucz dokumentu. Aby uzyskać dodatkowe informacje na temat tego tematu, zobacz [indeksowanie wielu źródeł danych platformy Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Aby uzyskać szczegółowy przewodnik, zobacz ten artykuł zewnętrzny: [Połącz dokumenty z innymi danymi w Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indeksowanie zwykłego tekstu 

Jeśli wszystkie obiekty blob zawierają zwykły tekst w tym samym kodowaniu, można znacząco poprawić wydajność indeksowania przy użyciu **trybu analizowania tekstu**. Aby użyć trybu analizowania tekstu, ustaw `parsingMode` właściwość konfiguracja na: `text`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Domyślnie jest założono `UTF-8` kodowanie. Aby określić inne kodowanie, użyj `encoding` właściwości konfiguracja: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości
Poniższa tabela zawiera podsumowanie przetwarzania dla każdego formatu dokumentu oraz opis właściwości metadanych wyodrębnionych przez Azure Search.

| Format dokumentu/typ zawartości | Właściwości metadanych specyficznych dla typu zawartości | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rozpakowywanie znacznika HTML i wyodrębnienie tekstu |
| PDF (aplikacja/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty (z wyjątkiem obrazów) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOCM (application/vnd. MS-Word. Document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnij tekst, w tym załączniki |
| ODT (application/vnd. języka Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODS (application/vnd. języka Oasis. OpenDocument. arkusza kalkulacyjnego) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODP (application/vnd. języka Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| Plik ZIP (Application/zip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| GZ (aplikacja/gzip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| XML (aplikacja/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Wyodrębnij tekst<br/>UWAGA: Jeśli musisz wyodrębnić wiele pól dokumentu z obiektu BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) w celu uzyskania szczegółów |
| EML (Message/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnij tekst, w tym załączniki |
| RTF (aplikacja/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Wyodrębnij tekst|
| Zwykły tekst (tekst/zwykły) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Wyodrębnij tekst|


## <a name="help-us-make-azure-search-better"></a>Pomóż nam w ulepszaniu Azure Search
Jeśli masz żądania dotyczące funkcji lub pomysły dotyczące ulepszeń, daj nam znać w naszej [witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
