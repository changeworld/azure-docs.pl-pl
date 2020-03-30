---
title: Wyszukiwanie zawartości magazynu obiektów Blob platformy Azure
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować usługę Azure Blob Storage i wyodrębniać tekst z dokumentów za pomocą usługi Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067643"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Jak indeksować dokumenty w usłudze Azure Blob Storage za pomocą usługi Azure Cognitive Search

W tym artykule pokazano, jak używać usługi Azure Cognitive Search do indeksowania dokumentów (takich jak pliki PDF, dokumenty pakietu Microsoft Office i kilka innych popularnych formatów) przechowywanych w magazynie obiektów Blob platformy Azure. Po pierwsze wyjaśniono podstawy konfigurowania i konfigurowania indeksatora obiektów blob. Następnie oferuje głębszą eksplorację zachowań i scenariuszy, które mogą wystąpić.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów
Indeksator obiektów blob może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurowanie indeksowania obiektów blob
Indeksatora usługi Azure Blob Storage można skonfigurować przy użyciu:

* [Portal Azure](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) usługi Azure Cognitive Search
* Zestaw [SDK](https://aka.ms/search-sdk) usługi Azure Cognitive Search .NET

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pól) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>

W tym miejscu zademonstrujemy przepływ przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Źródło danych określa, które dane mają być indeksowane, poświadczenia potrzebne do uzyskania dostępu do danych i zasady umożliwiające efektywną identyfikację zmian w danych (nowe, zmodyfikowane lub usunięte wiersze). Źródło danych może być używane przez wiele indeksatorów w tej samej usłudze wyszukiwania.

W przypadku indeksowania obiektów blob źródło danych musi mieć następujące wymagane właściwości:

* **nazwa** jest unikatową nazwą źródła danych w usłudze wyszukiwania.
* **musi** być `azureblob`.
* **poświadczenia** udostępnia parametry połączenia konta `credentials.connectionString` magazynu jako parametr. Zobacz [Jak określić poświadczenia](#Credentials) poniżej, aby uzyskać szczegółowe informacje.
* **kontener** określa kontener na koncie magazynu. Domyślnie wszystkie obiekty BLOB w kontenerze są możliwe do pobrania. Jeśli chcesz indeksować tylko obiekty blob w określonym katalogu wirtualnym, można określić ten katalog przy użyciu opcjonalnego parametru **zapytania.**

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

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Tworzenie źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak określić poświadczenia ####

Poświadczenia kontenera obiektów blob można podać na jeden z następujących sposobów:

- **Parametry połączenia konta magazynu pełnego dostępu:** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` ciąg połączenia można uzyskać z witryny Azure Portal, przechodząc do bloku konta magazynu > Ustawienia > klucze (dla klasycznych kont magazynu) lub Ustawienia > klucze programu Access (dla kont magazynu Usługi Azure Resource Manager).
- Ciąg połączenia **sygnatury dostępu współdzielonego (SAS) konta magazynu:** `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` sygnatury dostępu współdzielonego powinna mieć uprawnienia listy i odczytu kontenerów i obiektów (w tym przypadku obiektów blob).
-  **Podpis dostępu współdzielonego kontenera:** `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` Sygnatury dostępu współdzielonego powinna mieć listę i uprawnienia do odczytu w kontenerze.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Korzystanie z podpisów dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Jeśli używasz poświadczeń sygnatury dostępu Współdzielonego, należy okresowo aktualizować poświadczenia źródła danych z odnowionymi podpisami, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu Współdzielonego `Credentials provided in the connection string are invalid or have expired.`wygasną, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobnym do .  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z przeszukiwalnym `content` polem do przechowywania tekstu wyodrębnionego z obiektów blob:   

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

### <a name="step-3-create-an-indexer"></a>Krok 3: Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i udostępnia harmonogram automatyzacji odświeżania danych.

Po utworzeniu indeksu i źródła danych można przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na "PT2H"). Aby uruchomić indeksator co 30 minut, należy ustawić interwał na "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalny — jeśli pominięto, indeksator uruchamia się tylko raz podczas jego tworzenia. Jednak można uruchomić indeksatora na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z [programem Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji na temat definiowania harmonogramów indeksatora, zobacz [Jak zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Jak usługa Azure Cognitive Search indeksuje obiekty blob

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex)indeksatora, indeksator obiektów blob może indeksować tylko metadane magazynu (przydatne tylko wtedy, gdy zależy Ci tylko na metadanych i nie trzeba indeksować zawartości obiektów blob), metadanych magazynu i zawartości lub zarówno metadanych, jak i zawartości tekstowej. Domyślnie indeksator wyodrębnia metadane i zawartość.

> [!NOTE]
> Domyślnie obiekty BLOB ze strukturą zawartości, takich jak JSON lub CSV są indeksowane jako pojedynczy fragment tekstu. Jeśli chcesz indeksować obiekty BLOB JSON i CSV w sposób strukturalny, zobacz [Indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) i [indeksowanie obiektów BLOB csv,](search-howto-index-csv-blobs.md) aby uzyskać więcej informacji.
>
> Dokument złożony lub osadzony (taki jak archiwum ZIP lub dokument programu Word z osadzoną pocztą e-mail programu Outlook zawierającą załączniki) jest również indeksowany jako pojedynczy dokument.

* Zawartość tekstowa dokumentu jest wyodrębniona w polu `content`ciągu o nazwie .

> [!NOTE]
> Usługa Azure Cognitive Search ogranicza ilość tekstu wyodrębnianych w zależności od warstwy cenowej: 32 000 znaków dla warstwy bezpłatnej, 64 000 dla basic, 4 miliony dla standardu, 8 milionów dla standardu S2 i 16 milionów dla standardu S3. Ostrzeżenie jest zawarte w odpowiedzi stanu indeksatora dla obciętych dokumentów.  

* Właściwości metadanych określone przez użytkownika obecne w obiekcie blob, jeśli istnieją, są wyodrębniane dosłownie. Należy zauważyć, że wymaga to pola, które ma być zdefiniowane w indeksie o takiej samej nazwie jak klucz metadanych obiektu blob. Na przykład, jeśli obiekt blob ma `Sensitivity` klucz `High`metadanych z `Sensitivity` wartością, należy zdefiniować pole o `High`nazwie w indeksie wyszukiwania i zostanie wypełnione wartością .
* Standardowe właściwości metadanych obiektów blob są wyodrębniane w następujących polach:

  * **nazwa\_magazynu\_metadanych** (Edm.String) - nazwa pliku obiektu blob. Jeśli na przykład masz obiekt blob /my-container/my-folder/subfolder/resume.pdf, wartość `resume.pdf`tego pola to .
  * **ścieżka\_przechowywania\_metadanych** (Edm.String) — pełny identyfikator URI obiektu blob, w tym konto magazynu. Na przykład: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **typ\_zawartości\_\_magazynu metadanych** (Edm.String) - typ zawartości określony przez kod użyty do przekazania obiektu blob. Na przykład `application/octet-stream`.
  * **ostatnio\_\_zmodyfikowany magazyn\_metadanych** (Edm.DateTimeOffset) — ostatnio zmodyfikowany znacznik czasu dla obiektu blob. Usługa Azure Cognitive Search używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystkiego po początkowym indeksowaniu.
  * **rozmiar\_magazynu\_metadanych** (Edm.Int64) - rozmiar obiektu blob w bajtach.
  * **zawartość\_\_magazynu\_metadanych md5** (Edm.String) — skrót MD5 zawartości obiektu blob, jeśli jest dostępny.
  * **token\_sas\_\_magazynu metadanych** (Edm.String) — tymczasowy token Sygnatury dostępu Współdzielonego, który może być używany przez [umiejętności niestandardowe,](cognitive-search-custom-skill-interface.md) aby uzyskać dostęp do obiektu blob. Ten token nie powinien być przechowywany do późniejszego użycia, ponieważ może wygasnąć.

* Właściwości metadanych specyficzne dla każdego formatu dokumentu są wyodrębniane w polach wymienionych [w tym miejscu](#ContentSpecificMetadata).

Nie trzeba definiować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — wystarczy przechwycić właściwości potrzebne dla aplikacji.

> [!NOTE]
> Często nazwy pól w istniejącym indeksie będą się różnić od nazw pól generowanych podczas wyodrębniania dokumentów. **Mapowania pól** można użyć do mapowania nazw właściwości dostarczonych przez usługę Azure Cognitive Search na nazwy pól w indeksie wyszukiwania. Poniżej zobaczysz przykład mapowań pól.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentów i mapowań pól
W usłudze Azure Cognitive Search klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole kluczowe typu Edm.String. Pole klucza jest wymagane dla każdego dokumentu, który jest dodawany do indeksu (w rzeczywistości jest to jedyne wymagane pole).  

Należy dokładnie rozważyć, które wyodrębnione pole powinno być mapowane do pola kluczowego dla indeksu. Kandydatami są:

* **nazwa\_magazynu\_metadanych** — może to być wygodny kandydat, ale należy pamiętać, że 1) nazwy mogą nie być unikatowe, ponieważ mogą istnieć obiekty blob o tej samej nazwie w różnych folderach i 2) nazwa może zawierać znaki, które są nieprawidłowe w kluczach dokumentu, takie jak myślniki. Nieprawidłowe znaki można mieć `base64Encode` na uwadze za pomocą [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction) — jeśli to zrobisz, pamiętaj o kodowaniu kluczy dokumentu podczas przekazywania ich w wywołaniach interfejsu API, takich jak wyszukiwanie. (Na przykład w .NET można użyć [Metody UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) w tym celu).
* **ścieżka\_przechowywania\_metadanych** - użycie pełnej ścieżki zapewnia `/` unikatowość, ale ścieżka zdecydowanie zawiera znaki [nieprawidłowe w kluczu dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak wyżej, masz możliwość kodowania kluczy `base64Encode` za pomocą [funkcji](search-indexer-field-mappings.md#base64EncodeFunction).
* Jeśli żadna z powyższych opcji nie działa dla Ciebie, można dodać właściwość metadanych niestandardowych do obiektów blob. Ta opcja wymaga jednak procesu przekazywania obiektów blob, aby dodać tę właściwość metadanych do wszystkich obiektów blob. Ponieważ klucz jest wymaganą właściwością, wszystkie obiekty BLOB, które nie mają tej właściwości, nie zostaną zindeksowane.

> [!IMPORTANT]
> Jeśli nie ma jawnego mapowania dla pola klucza w `metadata_storage_path` indeksie, usługa Azure Cognitive Search automatycznie używa jako klucza, a base-64 koduje wartości klucza (druga opcja powyżej).
>
>

W tym przykładzie wybierzmy `metadata_storage_name` pole jako klucz dokumentu. Załóżmy również, że indeks ma `key` pole `fileSize` klucza o nazwie i pole do przechowywania rozmiaru dokumentu. Aby wysuwać elementy zgodnie z potrzebami, podczas tworzenia lub aktualizowania indeksatora należy określić następujące mapowania pól:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby połączyć to wszystko razem, oto jak można dodać mapowania pól i włączyć base-64 kodowanie kluczy dla istniejącego indeksatora:

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
> Aby dowiedzieć się więcej o mapowaniach pól, zobacz [ten artykuł](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Kontrolowanie, które obiekty blob są indeksowane
Można kontrolować, które obiekty BLOB są indeksowane, a które są pomijane.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indeksuj tylko obiekty blob z określonymi rozszerzeniami plików
Można indeksować tylko obiekty blob z rozszerzeniami nazw `indexedFileNameExtensions` plików, które określisz przy użyciu parametru konfiguracji indeksatora. Wartość jest ciągiem zawierającym oddzieloną przecinkami listę rozszerzeń plików (z wiodącą kropką). Na przykład, aby indeksować tylko . PDF i . Obiekty BLOBX, wykonaj tę te te procesy:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Wykluczanie obiektów blob z określonymi rozszerzeniami plików
Obiekty blob z określonymi rozszerzeniami nazw plików `excludedFileNameExtensions` można wykluczyć z indeksowania przy użyciu parametru konfiguracji. Wartość jest ciągiem zawierającym oddzieloną przecinkami listę rozszerzeń plików (z wiodącą kropką). Na przykład, aby indeksować wszystkie obiekty blob z wyjątkiem tych z . PNG i . rozszerzenia JPEG, wykonaj tę umowę:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Jeśli `indexedFileNameExtensions` oba `excludedFileNameExtensions` i parametry są obecne, `indexedFileNameExtensions`usługa Azure `excludedFileNameExtensions`Cognitive Search najpierw patrzy na , a następnie na . Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wykluczone z indeksowania.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrolowanie, które części obiektu blob są indeksowane

Można kontrolować, które części obiektów blob `dataToExtract` są indeksowane przy użyciu parametru konfiguracji. Może to zająć następujące wartości:

* `storageMetadata`- określa, że indeksowane są tylko [standardowe właściwości obiektu blob i metadane określone przez użytkownika.](../storage/blobs/storage-properties-metadata.md)
* `allMetadata`- określa, że metadane magazynu i [metadane specyficzne dla typu zawartości](#ContentSpecificMetadata) wyodrębnione z zawartości obiektu blob są indeksowane.
* `contentAndMetadata`- określa, że wszystkie metadane i zawartość tekstową wyodrębnione z obiektu blob są indeksowane. Jest to wartość domyślna.

Na przykład, aby indeksować tylko metadane magazynu, należy użyć:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Używanie metadanych obiektów blob do kontrolowania sposobu indeksowanie obiektów blob

Parametry konfiguracji opisane powyżej dotyczą wszystkich obiektów blob. Czasami można kontrolować sposób *indeksowane poszczególnych obiektów blob.* Można to zrobić, dodając następujące właściwości i wartości metadanych obiektu blob:

| Nazwa właściwości | Wartość właściwości | Wyjaśnienie |
| --- | --- | --- |
| AzureSearch_Skip |"to prawda" |Nakazuje indeksatorowi obiektów blob, aby całkowicie pominąć obiekt blob. Nie podjęto próby wyodrębnienia metadanych ani zawartości. Jest to przydatne, gdy określony obiekt blob kończy się niepowodzeniem wielokrotnie i przerywa proces indeksowania. |
| AzureSearch_SkipContent |"to prawda" |Jest to `"dataToExtract" : "allMetadata"` równoważne ustawienie opisane [powyżej](#PartsOfBlobToIndex) o zakresie do określonego obiektu blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Radzenie sobie z błędami

Domyślnie indeksator obiektów blob zatrzymuje się, gdy tylko napotka obiekt blob z nieobsługiwał typ zawartości (na przykład obraz). Oczywiście można użyć `excludedFileNameExtensions` parametru, aby pominąć niektóre typy zawartości. Jednak może być konieczne indeksowane obiekty BLOB bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować indeksowanie po napotkaniu nieobsługiwałego `false`typu zawartości, ustaw parametr konfiguracji na: `failOnUnsupportedContentType`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

W przypadku niektórych obiektów blob usługa Azure Cognitive Search nie może określić typu zawartości lub nie może przetworzyć dokumentu innego obsługiwanego typu zawartości. Aby zignorować ten tryb `failOnUnprocessableDocument` błędu, ustaw parametr konfiguracji na false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Usługa Azure Cognitive Search ogranicza rozmiar obiektów blob, które są indeksowane. Limity te są udokumentowane w [limitach usług w usłudze Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Przewymiarowane obiekty blob są domyślnie traktowane jako błędy. Jednak nadal można indeksować metadane magazynu ponadgabarytowych obiektów blob, jeśli ustawisz `indexStorageMetadataOnlyForOversizedDocuments` parametr konfiguracji na true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Można również kontynuować indeksowanie, jeśli błędy zdarzają się w dowolnym momencie przetwarzania, podczas analizowania obiektów blob lub podczas dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić parametry `maxFailedItems` i `maxFailedItemsPerBatch` konfigurację na żądane wartości. Przykład:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie indeksowania przyrostowego i usuwania

Po skonfigurowaniu indeksatora obiektów blob do uruchamiania zgodnie z harmonogramem, ponownie indeksuje tylko `LastModified` zmienionych obiektów blob, zgodnie z ustaleniami sygnatury czasowej obiektu blob.

> [!NOTE]
> Nie trzeba określać zasady wykrywania zmian — indeksowanie przyrostowe jest włączone automatycznie.

Aby obsługiwać usuwanie dokumentów, należy użyć metody "usuwania nietrwałego". Jeśli usuniesz obiekty blob wprost, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania.

Istnieją dwa sposoby implementacji metody usuwania nietrwałego. Oba są opisane poniżej.

### <a name="native-blob-soft-delete-preview"></a>Usuwanie nietrwałe obiektu blob natywne (podgląd)

> [!IMPORTANT]
> Obsługa usuwania nietrwałego obiektu blob natywnego jest w wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ta funkcja zapewnia [interfejs API REST w wersji 2019-05-06-Preview.](https://docs.microsoft.com/azure/search/search-api-preview) Obecnie nie ma obsługi portalu ani SDK .NET.

> [!NOTE]
> Podczas korzystania z natywnych zasad usuwania nietrwałego obiektu blob klucze dokumentu dla dokumentów w indeksie musi być właściwość obiektu blob lub metadane obiektu blob.

W tej metodzie użyjesz [natywnej funkcji usuwania nietrwałego obiektu blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) oferowanej przez magazyn obiektów Blob platformy Azure. Jeśli natywne usuwanie nietrwałe obiektów blob jest włączone na koncie magazynu, źródło danych ma natywny zestaw zasad usuwania nietrwałego, a indeksator znajdzie obiekt blob, który został przeniesiony do stanu nietrwałego usuniętego, indeksator usunie ten dokument z indeksu. Natywna zasada usuwania nietrwałego obiektu blob nie jest obsługiwana podczas indeksowania obiektów blob z usługi Azure Data Lake Storage Gen2.

Wykonaj następujące czynności:
1. Włącz [natywne usuwanie nietrwałe dla magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). Zaleca się ustawienie zasad przechowywania na wartość, która jest znacznie wyższa niż harmonogram interwału indeksatora. W ten sposób, jeśli istnieje problem z uruchomieniem indeksatora lub jeśli masz dużą liczbę dokumentów do indeksowania, istnieje dużo czasu dla indeksatora, aby ostatecznie przetworzyć miękkie usunięte obiekty blob. Indeksatory usługi Azure Cognitive Search usunie dokument z indeksu tylko wtedy, gdy przetwarza obiekt blob, gdy jest w stanie nietrwałego usuniętego.
1. Skonfiguruj natywną zasadę wykrywania usuwania nieumiejętnego obiektu blob w źródle danych. Przykład przedstawiono poniżej. Ponieważ ta funkcja jest w wersji zapoznawczej, należy użyć interfejsu API rest w wersji zapoznawczej.
1. Uruchom indeksatora lub ustaw indeksatora do uruchomienia zgodnie z harmonogramem. Gdy indeksator uruchamia i przetwarza obiekt blob dokument zostanie usunięty z indeksu.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Ponowne indeksowanie nieusuniętych obiektów blob

Jeśli usuniesz obiekt blob z magazynu obiektów Blob platformy Azure z włączonym natywnym usuwaniem nietrwałym na koncie magazynu, obiekt blob przejdzie do nietrwale usuniętego stanu, co daje możliwość cofnięcia usunięcia tego obiektu blob w okresie przechowywania. Gdy źródło danych usługi Azure Cognitive Search ma natywną zasadę usuwania nietrwałego obiektu blob, a indeksator przetwarza nietrwale usunięty obiekt blob, usunie ten dokument z indeksu. Jeśli ten obiekt blob jest później cofnięte indeksator nie zawsze reindex tego obiektu blob. Dzieje się tak, ponieważ indeksator określa, które obiekty BLOB do indeksowania na podstawie sygnatury czasowej `LastModified` obiektu blob. Gdy nietrwałe usunięte obiektu blob jest cofnięte jego `LastModified` sygnatura czasowa nie `LastModified` zostanie zaktualizowana, więc jeśli indeksator już przetworzone obiekty BLOB z sygnaturami czasowymi nowsze niż nieusunięty obiekt blob nie będzie ponownie indeksować undeleted obiektu blob. Aby upewnić się, że nieusunięty obiekt blob jest ponownie indeksowany, `LastModified` należy zaktualizować sygnaturę czasową obiektu blob. Jednym ze sposobów, aby to zrobić jest ponowne wymazyowanie metadanych tego obiektu blob. Nie trzeba zmieniać metadanych, ale ponowne ich ponowne aktualizowanie metadanych zaktualizuje sygnaturę czasową `LastModified` obiektu blob, dzięki czemu indeksator wie, że musi ponownie indeksować ten obiekt blob.

### <a name="soft-delete-using-custom-metadata"></a>Usuwanie nietrwałe przy użyciu niestandardowych metadanych

W tej metodzie użyjesz metadanych obiektu blob, aby wskazać, kiedy dokument powinien zostać usunięty z indeksu wyszukiwania.

Wykonaj następujące czynności:

1. Dodaj parę klucz-wartość metadanych niestandardowych do obiektu blob, aby wskazać w usłudze Azure Cognitive Search, że jest logicznie usuwany.
1. Skonfiguruj zasady wykrywania kolumn o nieudzielenia łajdakiem w źródle danych. Przykład przedstawiono poniżej.
1. Gdy indeksator przetworzył obiekt blob i usunął dokument z indeksu, można usunąć obiekt blob dla magazynu obiektów Blob platformy Azure.

Na przykład następująca zasada uważa obiekt blob za usunięty, `IsDeleted` jeśli ma `true`właściwość metadanych o wartości:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }

#### <a name="reindexing-undeleted-blobs"></a>Ponowne indeksowanie nieusuniętych obiektów blob

Jeśli ustawisz zasady wykrywania kolumn usuwania nietrwałego w źródle danych, a następnie dodaj niestandardowe metadane do obiektu blob z wartością znacznika, a następnie uruchomisz indeksator, indeksator usunie ten dokument z indeksu. Jeśli chcesz ponownie zakediiować ten dokument, po prostu zmień wartość nietrwałych metadanych usuwania nietrwałego dla tego obiektu blob i uruchom ponownie indeksatora.

## <a name="indexing-large-datasets"></a>Indeksowanie dużych zestawów danych

Indeksowanie obiektów blob może być procesem czasochłonnym. W przypadkach, gdy masz miliony obiektów blob do indeksowania, można przyspieszyć indeksowanie przez partycjonowanie danych i przy użyciu wielu indeksatorów do przetwarzania danych równolegle. Oto jak to skonfigurować:

- Partycjonowanie danych na wiele kontenerów obiektów blob lub folderów wirtualnych
- Skonfiguruj kilka źródeł danych usługi Azure Cognitive Search, po jednym na kontener lub folder. Aby wskazać folder obiektów blob, użyj parametru: `query`

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Utwórz odpowiedni indeksator dla każdego źródła danych. Wszystkie indeksatory można wskazać ten sam indeks wyszukiwania docelowego.  

- Jedna jednostka wyszukiwania w usłudze może uruchomić jeden indeksator w danym momencie. Tworzenie wielu indeksatorów, jak opisano powyżej jest przydatne tylko wtedy, gdy faktycznie działają równolegle. Aby uruchomić wiele indeksatorów równolegle, skaluj w poziomie usługi wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Na przykład jeśli usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 partycje x 3 repliki), a następnie 6 indeksatorów można uruchomić jednocześnie, co powoduje sześciokrotny wzrost przepływności indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [Skalowanie poziomów zasobów dla obciążeń zapytań i indeksowania w usłudze Azure Cognitive Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indeksowanie dokumentów wraz z powiązanymi danymi

Można "zmontować" dokumenty z wielu źródeł w indeksie. Na przykład można scalić tekst z obiektów blob z innymi metadanymi przechowywanymi w usłudze Cosmos DB. Można nawet użyć interfejsu API indeksowania wypychającego wraz z różnymi indeksatorami do tworzenia dokumentów wyszukiwania z wielu części. 

Aby to zadziałało, wszystkie indeksatory i inne składniki muszą uzgodnić klucz dokumentu. Aby uzyskać dodatkowe informacje na ten temat, zobacz [Indeksowanie wielu źródeł danych platformy Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Aby uzyskać szczegółowe informacje, zobacz ten artykuł [zewnętrzny: Łączenie dokumentów z innymi danymi w usłudze Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indeksowanie zwykłego tekstu 

Jeśli wszystkie obiekty blob zawierają zwykły tekst w tym samym kodowaniu, można znacznie poprawić wydajność indeksowania przy użyciu **trybu analizy tekstu**. Aby użyć trybu analizy tekstu, `parsingMode` ustaw `text`właściwość konfiguracj na:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Domyślnie zakłada `UTF-8` się kodowanie. Aby określić inne kodowanie, należy użyć właściwości `encoding` konfiguracji: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości
W poniższej tabeli podsumowano przetwarzanie wykonane dla każdego formatu dokumentu i opisano właściwości metadanych wyodrębnione przez usługę Azure Cognitive Search.

| Format dokumentu / typ zawartości | Właściwości metadanych określonego typu zawartości | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (tekst/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rozłącz znaczniki HTML i wyodrębnij tekst |
| PDF (aplikacja/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami (z wyłączeniem obrazów) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| DOC (aplikacja/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| DOCM (aplikacja/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| WORD XML (aplikacja/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Rozłącz znaczniki XML i wyodrębnij tekst |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Rozłącz znaczniki XML i wyodrębnij tekst |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| XLS (aplikacja/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| XLSM (aplikacja/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| PPT (aplikacja/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| PPTM (aplikacja/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnij tekst, łącznie z załącznikami. `metadata_message_to_email`i `metadata_message_cc_email` `metadata_message_bcc_email` są kolekcjami ciągów, pozostałe pola są ciągami.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| ODS (arkusz kalkulacyjny application/vnd.oasis.opendocument.) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| ODP (aplikacja/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Wyodrębnianie tekstu wraz z osadzonymi dokumentami |
| ZIP (aplikacja/zamek błyskawiczny) |`metadata_content_type` |Wyodrębnianie tekstu ze wszystkich dokumentów w archiwum |
| GZ (aplikacja/gzip) |`metadata_content_type` |Wyodrębnianie tekstu ze wszystkich dokumentów w archiwum |
| EPUB (aplikacja/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Wyodrębnianie tekstu ze wszystkich dokumentów w archiwum |
| XML (aplikacja/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Rozłącz znaczniki XML i wyodrębnij tekst |
| JSON (aplikacja/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Wyodrębnij tekst<br/>UWAGA: Jeśli chcesz wyodrębnić wiele pól dokumentu z obiektu blob JSON, zobacz [Indeksowanie obiektów blob JSON, aby](search-howto-index-json-blobs.md) uzyskać szczegółowe informacje |
| EML (wiadomość/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnianie tekstu wraz z załącznikami |
| RTF (aplikacja/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Wyodrębnij tekst|
| Zwykły tekst (tekst/zwykły) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Wyodrębnij tekst|


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam poprawić usługę Azure Cognitive Search
Jeśli masz prośby o ulepszenia lub pomysły dotyczące ulepszeń, poinformuj nas o tym na naszej [stronie UserVoice.](https://feedback.azure.com/forums/263029-azure-search/)
