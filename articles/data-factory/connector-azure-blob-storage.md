---
title: Kopiowanie danych do i z magazynu obiektów Blob platformy Azure przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanej źródłowej magazyny danych do magazynu obiektów Blob platformy Azure lub z magazynu obiektów Blob do zbiornika obsługiwanych magazynów danych, przy użyciu fabryki danych.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 1d5b73657a00968ce073e1cb1ea72a716e6a2703
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615968"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z magazynu obiektów Blob platformy Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-azure-blob-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-azure-blob-storage.md)

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure można skopiować danych do i z magazynu obiektów Blob platformy Azure. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 fabryki danych, która jest ogólnie dostępna, zobacz [łącznika magazynu obiektów Blob w wersji 1](v1/data-factory-azure-blob-connector.md).


## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować z dowolnego obsługiwane źródła danych magazynu danych do magazynu obiektów Blob. Możesz również skopiować dane z magazynu obiektów Blob do żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md) tabeli.

W szczególności obsługuje ten łącznik magazynu obiektów Blob:

- Kopiowanie obiektów blob do i z kont magazynu ogólnego przeznaczenia Azure i magazynu obiektów blob hot superpaska. 
- Kopiowania obiektów blob, używając klucza konta i usługi udostępniane uwierzytelnienia podpisu dostępu.
- Kopiowanie obiektów blob z bloku, Dołącz lub stronicowe obiekty BLOB i kopiowanie danych tylko blokowe obiekty BLOB. Usługa Azure Premium Storage nie jest obsługiwany jako zbiorniku, ponieważ nie jest obsługiwana przez stronicowych obiektów blob.
- Kopiowanie obiektów blob, ponieważ jest lub analizowania ani generowania obiektów blob z [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do magazynu obiektów Blob.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

### <a name="use-an-account-key"></a>Użyj klucza konta

Połączoną usługą magazynu można utworzyć przy użyciu klucza konta. Zapewnia fabryka danych z globalnego dostępu do magazynu. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AzureStorage**. |Yes |
| Parametry połączenia | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych w sieci prywatnej) można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>Użyj uwierzytelniania sygnatury dostępu współdzielonego usługi

Możesz również utworzyć połączoną usługą magazynu przy użyciu sygnatury dostępu współdzielonego. Fabryka danych zapewnia ograniczony/czas-powiązane z dostęp do określonego/all zasobów (kontener/obiektów blob) w magazynie.

Sygnatury dostępu współdzielonego umożliwiają dostęp delegowany do zasobów na koncie magazynu. Sygnatury dostępu współdzielonego umożliwia przyznanie klienta ograniczone uprawnienia do obiektów na koncie magazynu w wyznaczonym czasie. Nie trzeba udostępniać klucze dostępu do Twojego konta. Sygnatury dostępu współdzielonego to identyfikator URI, który obejmuje w jego parametrów zapytania, wszystkie informacje niezbędne do uwierzytelniony dostęp do zasobów magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko Przekaż sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatur dostępu współużytkowanego: zrozumienie modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Fabryka danych obsługuje obecnie tylko sygnatur dostępu usług udostępnionych, ale nie sygnatur dostępu udostępnionego konta. Aby uzyskać więcej informacji o tych dwóch typów i sposób ich tworzenia, zobacz [rodzaje sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). Dostępu współdzielonego adres URL sygnatury wygenerowane z portalu Azure lub Eksploratora usługi Storage platformy Azure jest sygnatury dostępu współdzielonego konta, które nie jest obsługiwane.

> [!TIP]
> Można wykonywać następujące polecenia programu PowerShell, aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu. Zastąp symbole zastępcze i przyznanie odpowiedniego uprawnienia.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania sygnatury dostępu współdzielonego usługi, są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AzureStorage**. |Yes |
| sasUri | Określ sygnatury dostępu współdzielonego identyfikator URI do zasobów magazynu obiektów blob, kontenera lub tabeli. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Używając środowiska uruchomieniowego integracji Azure lub środowiska uruchomieniowego integracji Self-hosted (Jeśli w magazynie danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Po utworzeniu identyfikatora URI sygnatury dostępu współdzielonego, należy wziąć pod uwagę następujące kwestie:

- Ustaw uprawnienia odczytu/zapisu w odpowiedniej obiekty zależności używania połączonej usługi (Odczyt, zapis, Odczyt/zapis) w fabryce danych.
- Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do magazynu obiektów nie wygasa w aktywnym okresie potoku.
- Identyfikator URI utworzony na poziomie prawo kontenera/obiektów blob lub tabeli oparte na potrzeby. Identyfikator URI sygnatury dostępu współdzielonego do obiektu blob umożliwia fabryki danych można uzyskać dostępu do tego konkretnego obiektu blob. Identyfikator URI sygnatury dostępu współdzielonego do kontenera magazynu obiektów Blob umożliwia fabryki danych do iteracji obiektów blob w tym kontenerze. Aby zapewnić dostęp do obiektów więcej lub mniej później lub zaktualizować sygnatury dostępu współdzielonego identyfikatora URI, pamiętaj, aby zaktualizować połączonej usługi o nowy identyfikator URI.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych z magazynu obiektów Blob.

Aby skopiować dane do i z magazynu obiektów Blob, ustaw właściwość Typ zestawu danych do **AzureBlob**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwości typu zestawu danych musi mieć ustawioną **AzureBlob**. |Yes |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów blob. Filtr symbolu wieloznacznego nie jest obsługiwane. Przykładem jest myblobcontainer/myblobfolder /. |Yes |
| fileName | **Nazwa lub symbol wieloznaczny filtr** dla blob(s) w obszarze określonej "folderPath". Jeśli nie określisz wartości dla tej właściwości zestawu danych wskazuje wszystkie obiekty BLOB w tym folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (wielu znaków) i `?` (pojedynczy znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` ucieczki Jeśli nazwę rzeczywisty plik ma symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie jest podany w zbiornika działania działanie kopiowania automatycznie generuje nazwę obiektu blob z następującego wzorca: "*danych. [ Identyfikator GUID uruchomienia działania]. [Identyfikator GUID Jeżeli FlattenHierarchy]. [format skonfigurowanie]. [skonfigurowanie kompresji]* ". Przykładem jest "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Nie |
| Format | Jeśli chcesz skopiować pliki jako między magazynów opartych na plikach (kopia binarnego), Pomiń sekcji format w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości w **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](supported-file-formats-and-compression-codecs.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), i [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Nie (tylko w przypadku scenariusza kopiowania binarny) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty BLOB w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczego obiektu blob o podanej nazwie, określ **folderPath** z częścią folderu i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw obiekty BLOB w folderze, określ **folderPath** z częścią folderu i **fileName** filtru symbolu wieloznacznego. 

**Przykład:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło magazynu obiektów Blob i odbioru.

### <a name="blob-storage-as-a-source-type"></a>Magazyn obiektów blob jako typ źródła

Aby skopiować dane z magazynu obiektów Blob, należy ustawić typ źródła w przypadku działania kopiowania do **BlobSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **BlobSource**. |Yes |
| Cykliczne | Wskazuje, czy dane są odczytywane rekursywnie z podfoldery lub tylko określonego folderu. Należy pamiętać, że gdy rekursywny ma ustawioną wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink.<br/>Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Typ ujścia magazynu obiektów blob

Aby skopiować dane do magazynu obiektów Blob, należy ustawić typ ujścia w działaniu kopiowania do **BlobSink**. Następujące właściwości są obsługiwane w **zbiornika** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania **BlobSink**. |Yes |
| copyBehavior | Określa zachowanie kopiowania, gdy źródłem jest pliki z magazynu danych.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszym poziomem folderu docelowego. Pliki docelowe mają nazwy wygenerowana automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli zostanie określona nazwa pliku lub obiektu blob, nazwa scalony plik jest określona nazwa. W przeciwnym razie jest automatycznie generowaną nazwą pliku. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Przykłady cyklicznego i copyBehavior

W tej sekcji opisano efekty operacji kopiowania dla różnych kombinacji wartości cyklicznej i copyBehavior.

| Cykliczne | copyBehavior | Struktura folderów źródła | Wynikowa docelowego |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony z tej samej struktury jako źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + File5 zawartości są scalane w jeden plik o nazwie wygenerowana automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie generowaną nazwą Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowana automatycznie. automatycznie generowaną nazwą Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
