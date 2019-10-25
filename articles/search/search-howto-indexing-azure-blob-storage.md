---
title: Indeksuj zawartość usługi Azure Blob Storage na potrzeby wyszukiwania pełnotekstowego
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować Blob Storage platformy Azure i wyodrębnić tekst z dokumentów przy użyciu usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b093525fcabc31074b398444a2fceffd0f6d3493
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791789"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Jak indeksować dokumenty w usłudze Azure Blob Storage przy użyciu usługi Azure Wyszukiwanie poznawcze

W tym artykule pokazano, jak za pomocą usługi Azure Wyszukiwanie poznawcze indeksować dokumenty (takie jak pliki PDF, dokumenty Microsoft Office i inne popularne formaty) przechowywane w usłudze Azure Blob Storage. Najpierw objaśnia podstawowe informacje na temat konfigurowania i konfigurowania indeksatora obiektów BLOB. Następnie oferuje dokładniejszą eksplorację zachowań i scenariuszy, które prawdopodobnie napotkasz.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów
Indeksator obiektów BLOB może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurowanie indeksowania obiektów BLOB
Można skonfigurować usługę Azure Blob Storage indeksator przy użyciu:

* [Azure Portal](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) usługi Azure wyszukiwanie poznawcze
* Azure Wyszukiwanie poznawcze [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pól) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>

W tym miejscu zademonstrowano przepływ przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Źródło danych określa, które dane mają być indeksowane, które są potrzebne do uzyskiwania dostępu do danych, oraz zasady umożliwiające wydajne identyfikowanie zmian w danych (nowych, zmodyfikowanych lub usuniętych wierszy). Źródło danych może być używane przez wiele indeksatorów w tej samej usłudze wyszukiwania.

W przypadku indeksowania obiektów BLOB źródło danych musi mieć następujące wymagane właściwości:

* **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
* **Typ** musi być `azureblob`.
* **poświadczenia** udostępniają parametry połączenia konta magazynu jako parametr `credentials.connectionString`. Aby uzyskać szczegółowe informacje [, zobacz Jak określić poświadczenia](#Credentials) poniżej.
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

- **Pełny dostęp do parametrów połączenia konta magazynu**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` można uzyskać parametry połączenia z Azure Portal, przechodząc do bloku konta magazynu > Ustawienia > klucze (dla klasycznych kont magazynu) lub ustawienia > klucze dostępu (dla platformy Azure Menedżer zasobów kont magazynu).
- Parametry połączenia **sygnatury dostępu współdzielonego** (SAS) konta magazynu: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` skojarzenia zabezpieczeń powinny mieć uprawnienia do listy i odczytu w kontenerach i obiektach (w tym przypadku obiektów BLOB).
-  **Sygnatura dostępu współdzielonego kontenera**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS powinna mieć uprawnienia listy i odczytu do kontenera.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> W przypadku używania poświadczeń SAS należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobnym do `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z polem `content` z możliwością wyszukiwania, aby przechowywać tekst wyodrębniony z obiektów blob:   

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

### <a name="step-3-create-an-indexer"></a>Krok 3. Tworzenie indeksatora
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

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Jak usługa Azure Wyszukiwanie poznawcze indeksuje obiekty blob

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex)indeksator obiektów BLOB może indeksować tylko metadane magazynu (przydatne w przypadku ponoszenia informacji o metadanych i braku potrzeby indeksowania zawartości obiektów BLOB), magazynu i metadanych zawartości, a także metadanych i zawartości tekstowej. Domyślnie indeksator wyodrębnia zarówno metadane, jak i zawartość.

> [!NOTE]
> Domyślnie obiekty blob o zawartości strukturalnej, takiej jak JSON lub CSV, są indeksowane jako pojedynczy fragment tekstu. Jeśli chcesz indeksować obiekty blob JSON i CSV w uporządkowany sposób, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) i [indeksowanie obiektów BLOB woluminów CSV](search-howto-index-csv-blobs.md) , aby uzyskać więcej informacji.
>
> Dokument złożony lub osadzony (na przykład archiwum ZIP lub dokument programu Word z osadzoną pocztą e-mail programu Outlook zawierający załączniki) również jest indeksowany jako pojedynczy dokument.

* Zawartość tekstowa dokumentu jest wyodrębniana do pola ciągu o nazwie `content`.

> [!NOTE]
> Usługa Azure Wyszukiwanie poznawcze ogranicza ilość tekstu wyodrębnianego w zależności od warstwy cenowej: 32 000 znaków dla warstwy Bezpłatna, 64 000 dla warstwy Podstawowa i 4 000 000 dla warstw standardowa, standardowa S2 i Standardowa S3. Ostrzeżenie jest zawarte w odpowiedzi stanu indeksatora dla obciętych dokumentów.  

* Właściwości metadanych określone przez użytkownika w obiekcie BLOB (jeśli istnieją) są wyodrębniane Verbatim.
* Standardowe właściwości metadanych obiektów BLOB są wyodrębniane do następujących pól:

  * **metadane\_magazynu\_nazwa** (EDM. String) — nazwa pliku obiektu BLOB. Na przykład jeśli masz obiekt BLOB/my-Container/my-folder/subfolder/Resume.PDF, wartość tego pola jest `resume.pdf`.
  * **metadata\_storage\_Path** (EDM. String) — pełny identyfikator URI obiektu BLOB, w tym konto magazynu. Na przykład: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadane\_magazynu\_content\_Type** (EDM. String) — typ zawartości określony przez kod używany do przekazywania obiektu BLOB. Na przykład `application/octet-stream`.
  * **metadane usługi\_storage\_ostatnio\_zmodyfikowane** (EDM. DateTimeOffset) — sygnatura czasowa ostatniej modyfikacji dla obiektu BLOB. Usługa Azure Wyszukiwanie poznawcze używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystkiego po początkowej indeksowaniu.
  * **rozmiar\_magazynu metadanych\_** (EDM. Int64) — rozmiar obiektu BLOB w bajtach.
  * **metadane\_storage\_content\_MD5** (EDM. String) — skrót MD5 zawartości obiektu BLOB, jeśli jest dostępny.
  * **metadata\_storage\_sas\_token** (EDM. String) — tymczasowy token SAS, który może być używany przez [umiejętności niestandardowe](cognitive-search-custom-skill-interface.md) do uzyskiwania dostępu do obiektu BLOB. Ten token nie powinien być przechowywany do późniejszego użycia, ponieważ mógł wygasnąć.

* Właściwości metadanych specyficzne dla każdego formatu dokumentu są wyodrębniane do pól wymienionych w [tym miejscu](#ContentSpecificMetadata).

Nie musisz definiować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — wystarczy przechwycić właściwości potrzebne dla aplikacji.

> [!NOTE]
> Często nazwy pól w istniejącym indeksie będą różnić się od nazw pól generowanych podczas wyodrębniania dokumentu. **Mapowania pól** można użyć do mapowania nazw właściwości dostarczanych przez usługę Azure wyszukiwanie poznawcze do nazw pól w indeksie wyszukiwania. Zobaczysz przykładowe mapowania pól poniżej.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowań pól
W usłudze Azure Wyszukiwanie poznawcze klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu EDM. String. Pole klucza jest wymagane dla każdego dokumentu dodawanego do indeksu (w rzeczywistości jest to jedyne pole wymagane).  

Należy uważnie rozważyć, które wyodrębnione pole powinno być mapowane na pole klucza dla indeksu. Kandydaci są:

* **\_nazwa\_magazynu metadanych** — może to być wygodny kandydat, ale należy zauważyć, że 1) nazwy mogą nie być unikatowe, ponieważ mogą istnieć obiekty blob o takiej samej nazwie w różnych folderach i 2) nazwa może zawierać znaki, które są nieprawidłowe w dokumencie klucze, takie jak łączniki. Za pomocą [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode` można zajmować się nieprawidłowymi znakami — Jeśli to zrobisz, pamiętaj, aby zakodować klucze dokumentu podczas przekazywania ich w WYWOŁANIACH interfejsu API, takich jak odnośnik. (Na przykład w programie .NET można użyć w tym celu [metody UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) ).
* **ścieżka\_magazynu\_metadanych** — przy użyciu pełnej ścieżki zapewnia unikatowość, ale ścieżka w nieskończoność zawiera `/` znaków, które są [nieprawidłowe w kluczu dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak powyżej, można zakodować klucze przy użyciu [funkcji](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Jeśli żadna z powyższych opcji nie zadziałała, możesz dodać niestandardową Właściwość metadanych do obiektów BLOB. Ta opcja wymaga jednak, aby proces przekazywania obiektów BLOB mógł dodać tę właściwość metadanych do wszystkich obiektów BLOB. Ponieważ klucz jest wymaganą właściwością, wszystkie obiekty blob, które nie mają tej właściwości, będą kończyć się niepowodzeniem.

> [!IMPORTANT]
> Jeśli nie istnieje jawne mapowanie pola klucza w indeksie, platforma Azure Wyszukiwanie poznawcze automatycznie używa `metadata_storage_path` jako klucza i Base-64 koduje wartości klucza (druga opcja powyżej).
>
>

Na potrzeby tego przykładu Wybierzmy pole `metadata_storage_name` jako klucz dokumentu. Załóżmy również, że indeks ma pole klucza o nazwie `key` i `fileSize` pola do przechowywania rozmiaru dokumentu. Aby odpowiednio podpracować, określ następujące mapowania pól podczas tworzenia lub aktualizowania indeksatora:

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
Można indeksować tylko obiekty blob z rozszerzeniami nazw plików, które można określić za pomocą parametru konfiguracji `indexedFileNameExtensions` indeksatora. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby zindeksować tylko. PDF i. Pliki BLOB DOCX, wykonaj następujące czynności:

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

Jeśli obecne są oba parametry `indexedFileNameExtensions` i `excludedFileNameExtensions`, usługa Azure Wyszukiwanie poznawcze najpierw przegląda `indexedFileNameExtensions`, a następnie na `excludedFileNameExtensions`. Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wyłączone z indeksowania.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrolowanie, które części obiektu BLOB są indeksowane

Można kontrolować, które fragmenty obiektów BLOB są indeksowane przy użyciu `dataToExtract` parametru konfiguracji. Może przyjmować następujące wartości:

* `storageMetadata` — określa, że indeksowane są tylko [standardowe właściwości obiektów blob i metadane określone przez użytkownika](../storage/blobs/storage-properties-metadata.md) .
* `allMetadata` — określa, że metadane magazynu i [metadane specyficzne dla typu zawartości](#ContentSpecificMetadata) wyodrębniane z zawartości obiektu BLOB są indeksowane.
* `contentAndMetadata` — określa, że wszystkie metadane i zawartość tekstowa wyodrębnione z obiektu BLOB są indeksowane. Jest to wartość domyślna.

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
| AzureSearch_Skip |oznacza |Instruuje indeksator obiektu BLOB, aby całkowicie pominąć obiekt BLOB. Nie podjęto próby przeprowadzenia żadnej metadanych ani wyodrębniania zawartości. Jest to przydatne, gdy konkretny obiekt BLOB powtarza się wielokrotnie i przerywa proces indeksowania. |
| AzureSearch_SkipContent |oznacza |Jest to odpowiednik ustawienia `"dataToExtract" : "allMetadata"` opisanego [powyżej](#PartsOfBlobToIndex) zakresu określonego obiektu BLOB. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Rozpatrywanie błędów

Domyślnie indeksator obiektu BLOB jest zatrzymywany zaraz po napotkaniu obiektu BLOB z nieobsługiwanym typem zawartości (na przykład obrazem). Możesz użyć parametru `excludedFileNameExtensions`, aby pominąć niektóre typy zawartości. Jednak może być konieczne indeksowanie obiektów Blob bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować indeksowanie, gdy zostanie napotkany nieobsługiwany typ zawartości, ustaw `failOnUnsupportedContentType` parametr konfiguracji na `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

W przypadku niektórych obiektów BLOB usługa Azure Wyszukiwanie poznawcze nie może określić typu zawartości lub nie może przetworzyć dokumentu z nieobsługiwanym typem zawartości. Aby zignorować ten tryb awarii, należy ustawić wartość false dla parametru konfiguracji `failOnUnprocessableDocument`:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Usługa Azure Wyszukiwanie poznawcze ogranicza rozmiar indeksowanych obiektów BLOB. Limity te są udokumentowane w [limitach usługi w usłudze Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Zbyt duże obiekty blob są domyślnie traktowane jako błędy. Można jednak nadal indeksować metadane magazynu o zwiększonym rozmiarze obiektów blob, jeśli `indexStorageMetadataOnlyForOversizedDocuments` parametr konfiguracji ma wartość true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Możesz również kontynuować indeksowanie w przypadku wystąpienia błędów w dowolnym momencie przetwarzania, podczas analizowania obiektów blob lub dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić parametry konfiguracji `maxFailedItems` i `maxFailedItemsPerBatch` na odpowiednie wartości. Na przykład:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie przyrostowe i usuwanie
Po skonfigurowaniu indeksatora obiektu BLOB do uruchamiania zgodnie z harmonogramem program ponownie indeksuje tylko zmienione obiekty blob, zgodnie z definicją sygnatury czasowej `LastModified` obiektu BLOB.

> [!NOTE]
> Nie trzeba określać zasad wykrywania zmian — indeksowanie przyrostowe jest automatycznie włączone.

Aby obsługiwać usuwanie dokumentów, użyj podejścia "usuwanie nietrwałe". Po usunięciu obiektów blob z prawej strony, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania. Zamiast tego należy wykonać następujące czynności:  

1. Dodaj niestandardową Właściwość metadanych do obiektu BLOB, aby wskazać, że usługa Azure Wyszukiwanie poznawcze jest usuwana logicznie
2. Konfigurowanie zasad wykrywania usuwania nietrwałego w źródle danych
3. Gdy indeksator przetworzy obiekt BLOB (jak pokazano w interfejsie API stanu indeksatora), można fizycznie usunąć obiekt BLOB

Na przykład następujące zasady uznają obiekt BLOB, który ma zostać usunięty, jeśli ma właściwość metadanych `IsDeleted` z wartością `true`:

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
- Skonfiguruj kilka źródeł danych usługi Azure Wyszukiwanie poznawcze, jeden dla każdego kontenera lub folderu. Aby wskazać folder obiektów blob, użyj parametru `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Utwórz odpowiedni indeksator dla każdego źródła danych. Wszystkie indeksatory mogą wskazywać na ten sam docelowy indeks wyszukiwania.  

- Jedna jednostka wyszukiwania w usłudze może uruchamiać jeden indeksator w danym momencie. Tworzenie wielu indeksatorów, jak opisano powyżej, jest przydatne tylko wtedy, gdy rzeczywiście są uruchamiane równolegle. Aby uruchamiać wiele indeksatorów równolegle, Skaluj w poziomie usługę wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Jeśli na przykład usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 repliki x 3), to 6 indeksatorów może być uruchomionych jednocześnie, co powoduje sześć przyrostów przepływności indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [poziomy zasobów skalowania dla obciążeń zapytań i indeksowania na platformie Azure wyszukiwanie poznawcze](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indeksowanie dokumentów wraz z powiązanymi danymi

Możesz chcieć "złożyć" dokumenty z wielu źródeł w indeksie. Na przykład możesz chcieć scalić tekst z obiektów blob z innymi metadanymi przechowywanymi w Cosmos DB. Można nawet użyć interfejsu API indeksowania wypychania razem z różnymi indeksatorami w celu utworzenia dokumentów wyszukiwania z wielu części. 

Aby to działało, wszystkie indeksatory i inne składniki muszą wyrazić zgodę na klucz dokumentu. Aby uzyskać dodatkowe informacje na temat tego tematu, zobacz [indeksowanie wielu źródeł danych platformy Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Aby uzyskać szczegółowy przewodnik, zobacz artykuł zewnętrzny: [łączenie dokumentów z innymi danymi na platformie Azure wyszukiwanie poznawcze](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indeksowanie zwykłego tekstu 

Jeśli wszystkie obiekty blob zawierają zwykły tekst w tym samym kodowaniu, można znacząco poprawić wydajność indeksowania przy użyciu **trybu analizowania tekstu**. Aby użyć trybu analizowania tekstu, ustaw właściwość konfiguracja `parsingMode` na `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Domyślnie założono kodowanie `UTF-8`. Aby określić inne kodowanie, użyj właściwości konfiguracji `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości
Poniższa tabela zawiera podsumowanie przetwarzania dla każdego formatu dokumentu oraz opis właściwości metadanych wyodrębnionych przez usługę Azure Wyszukiwanie poznawcze.

| Format dokumentu/typ zawartości | Właściwości metadanych specyficznych dla typu zawartości | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rozpakowywanie znacznika HTML i wyodrębnienie tekstu |
| PDF (aplikacja/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty (z wyjątkiem obrazów) |
| DOCX (application/vnd. openxmlformats-officedocument. WordprocessingML. Document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOCM (application/vnd. MS-Word. Document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
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
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Wyodrębnij tekst<br/>Uwaga: Jeśli chcesz wyodrębnić wiele pól dokumentu z obiektu BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) w celu uzyskania szczegółów |
| EML (Message/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnij tekst, w tym załączniki |
| RTF (aplikacja/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Wyodrębnij tekst|
| Zwykły tekst (tekst/zwykły) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Wyodrębnij tekst|


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam ulepszyć platformę Azure Wyszukiwanie poznawcze
Jeśli masz żądania dotyczące funkcji lub pomysły dotyczące ulepszeń, daj nam znać w naszej [witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
