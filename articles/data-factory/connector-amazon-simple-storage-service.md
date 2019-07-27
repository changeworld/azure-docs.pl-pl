---
title: Kopiowanie danych z Amazon Simple Storage Service (S3), za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Amazon Simple Storage Service (S3) do magazynów danych ujścia obsługiwane za pomocą usługi Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 088dfdbfbadfa43dc2bd161f56f0e2a6dbb94bb7
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311999"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiowanie danych z Amazon Simple Storage Service przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
>
> * [Wersja 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Bieżąca wersja](connector-amazon-simple-storage-service.md)

W tym artykule opisano sposób kopiowania danych z Amazon Simple Storage Service (Amazon S3). Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Amazon S3 jest obsługiwane w przypadku następujących działań:

- [Działanie kopiowania, które](copy-activity-overview.md) z [obsługiwane źródło/ujście macierzy](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W szczególności ten łącznik Amazon S3 obsługuje kopiowania plików jako — jest lub analizowania plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md). Używa ona [AWS podpisu w wersji 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) do uwierzytelniania żądań do S3.

>[!TIP]
>Ten łącznik Amazon S3 można użyć do kopiowania danych z **dowolnego dostawcy magazynu zgodny S3** np. [usłudze Google Cloud Storage](connector-google-cloud-storage.md). Określ odpowiedni adres URL usługi w konfiguracji usługi połączonej.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z usługi Amazon S3, upewnij się, że przyznano następujące uprawnienia:

- **Do wykonania działania kopiowania:** : `s3:GetObject` i `s3:GetObjectVersion` Amazon S3 obiektu operacji.
- **Do tworzenia fabryki danych graficznego interfejsu użytkownika**: `s3:ListAllMyBuckets` i `s3:ListBucket` / `s3:GetBucketLocation` Amazon S3 zasobnika operacji Ponadto wymagane są uprawnienia, dla operacji, takich jak połączenie testowe i Przeglądaj/Przejdź ścieżki plików. Jeśli nie chcesz przyznać te uprawnienia, Pomiń Testuj połączenie na stronie tworzenia połączonej usługi i określ ścieżkę bezpośrednio w ustawieniach zestawu danych.

Aby uzyskać szczegółowe informacje o pełną listę uprawnień Amazon S3, zobacz [określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do Amazon S3.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługi Amazon S3 połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AmazonS3**. | Yes |
| accessKeyId | Identyfikator klucza dostępu do kluczy tajnych. |Yes |
| secretAccessKey | Sam klucz dostępu do kluczy tajnych. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| serviceUrl | Określ niestandardowy punkt końcowy S3, jeśli kopiujesz danych od dostawcy magazynu zgodny S3 innych niż Usługa Amazon S3 oficjalnych. Na przykład, aby skopiować dane z usługi Google Cloud Storage, należy określić `https://storage.googleapis.com`. | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!TIP]
>Określ niestandardowy adres URL usługi S3, jeśli kopiujesz dane z magazynu zgodnego z S3, innych niż Usługa Amazon S3 oficjalnych.

>[!NOTE]
>Ten łącznik wymaga klucze dostępu dla konta zarządzania tożsamościami i Dostępem w celu skopiowania danych z usługi Amazon S3. [Tymczasowe poświadczeń zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie jest obsługiwane.
>

Oto przykład:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. 

- Dla **Parquet i format tekstu rozdzielanego**, można znaleźć [zestawu danych formatu Parquet i tekst rozdzielany](#parquet-and-delimited-text-format-dataset) sekcji.
- Dla innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innych zestawu danych w formacie](#other-format-dataset) sekcji.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet i zestaw danych, format tekstu rozdzielanego

Aby skopiować dane z usługi Amazon S3 w **Parquet lub format tekstu rozdzielanego**, można znaleźć [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na format oparty na zestawie danych i obsługiwane Ustawienia. Następujące właściwości są obsługiwane w przypadku Amazon S3 w ramach `location` ustawienia w formacie na podstawie zestawu danych:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość type w obszarze `location` w zestawie danych musi być równa **AmazonS3Location**. | Yes      |
| bucketName | Nazwa zasobnika S3.                                          | Yes      |
| folderPath | Ścieżka do folderu, w ramach danego pakietu. Jeśli chcesz używać symboli wieloznacznych, do folderu filtru, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w ramach danego zasobnika + folderPath. Jeśli chcesz użyć symboli wieloznacznych, aby odfiltrować pliki, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| version | Wersja obiektu S3, jeśli jest włączone w wersji S3. Jeśli nie zostanie określony, będzie można pobrać najnowszą wersję. |Nie |

> [!NOTE]
> **AmazonS3Object** typ zestawu danych w formacie Parquet/tekstu opisane w następnej sekcji nadal jest obsługiwany jako — jest dla działania kopiowania/wyszukiwania/GetMetadata dla zgodności z poprzednimi wersjami, ale nie działa w przypadku mapowania przepływu danych. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Innym formacie zestawu danych

Aby skopiować dane z usługi Amazon S3 w **format ORC/Avro/JSON/dane binarne**, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **AmazonS3Object** |Yes |
| bucketName | Nazwa zasobnika S3. Filtr z symbolami wieloznacznymi nie jest obsługiwana. |Tak w przypadku działania kopiowania/Lookup, nie na działaniu GetMetadata |
| key | **Filtr nazwę lub symbolu wieloznacznego** S3 klucza obiektu pod określony przedział. Dotyczy tylko kiedy "prefiks" nie określono właściwości. <br/><br/>Filtr z symbolami wieloznacznymi jest obsługiwany dla część nazwy pliku i część z folderem. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"key": "rootfolder/subfolder/*.csv"`<br/>— Przykład 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zobacz przykład więcej w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). Użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu/pliku symboli wieloznacznych lub ten znak ucieczki wewnątrz. |Nie |
| prefix | Prefiks klucza obiektu S3. Zostaną zaznaczone obiekty, których klucze rozpoczynały od tego prefiksu. Ma zastosowanie tylko wtedy, gdy nie określono właściwości "key". |Nie |
| version | Wersja obiektu S3, jeśli jest włączone w wersji S3. Jeśli nie zostanie określony, będzie można pobrać najnowszą wersję. |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólnej wydajności przenoszenia danych będzie mieć wpływ na włączenie tego ustawienia, gdy użytkownik chce pliku filtru z ogromne ilości plików. <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólnej wydajności przenoszenia danych będzie mieć wpływ na włączenie tego ustawienia, gdy użytkownik chce pliku filtru z ogromne ilości plików. <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **Optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **bucketName** dla przedziału i **prefiks** dla część z folderem.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **bucketName** dla przedziału i **klucz** części, a także plik nazwy folderu.<br>Aby skopiować podzestaw plików w folderze, podaj **bucketName** dla przedziału i **klucz** dla folderu filtru część oraz symbol wieloznaczny.

**Przykład: użycie prefiksu**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

**Przykład: przy użyciu klucza i wersji (opcjonalne)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako źródło

- Na potrzeby kopiowania z **Parquet i format tekstu rozdzielanego**, można znaleźć [Parquet i źródło format tekstu rozdzielanego](#parquet-and-delimited-text-format-source) sekcji.
- Na potrzeby kopiowania z innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innego formatu źródła](#other-format-source) sekcji.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet i źródło format tekstu rozdzielanego

Aby skopiować dane z usługi Amazon S3 w **Parquet lub format tekstu rozdzielanego**, można znaleźć [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat źródła działania kopiowania oparta na format i Obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku Amazon S3 w ramach `storeSettings` ustawienia źródła kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AmazonS3ReadSetting**. | Yes                                                         |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                                          |
| prefix                   | Prefiks klucza obiektu S3 w ramach danego pakietu skonfigurowane w zestawie danych do obiektów źródła filtru. Zostaną zaznaczone obiekty, których klucze rozpoczynały od tego prefiksu. <br>Ma zastosowanie tylko wtedy, gdy `wildcardFolderPath` i `wildcardFileName` nie są określone właściwości. | Nie                                                          |
| wildcardFolderPath       | Ścieżka folderu przy użyciu symboli wieloznacznych w ramach danego pakietu skonfigurowane w zestawie danych do filtru źródła folderów. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Nie                                                          |
| wildcardFileName         | Nazwa pliku przy użyciu symboli wieloznacznych w ramach danego zasobnika + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Tak, jeśli `fileName` w zestawie danych i `prefix` nie są określone |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany. | Nie                                                          |
| modifiedDatetimeEnd      | Wartość taka sama jak powyżej.                                               | Nie                                                          |
| maxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie                                                          |

> [!NOTE]
> Dla formatu Parquet/rozdzielany tekst **FileSystemSource** źródło działania kopiowania typu opisane w następnej sekcji nadal jest obsługiwany jako — jest zgodności z poprzednimi wersjami. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Inne źródła formatu

Aby skopiować dane z usługi Amazon S3 w **format ORC/Avro/JSON/dane binarne**, następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **FileSystemSource** |Yes |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. Należy pamiętać podczas cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynu opartego na pliku, pusty folder/podrzędnych — folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |
| maxConcurrentConnections | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Folder i plik Przykłady filtrów

W tej sekcji opisano wynikowe zachowania ścieżkę i nazwę folderu z filtrami symboli wieloznacznych.

| bucket | key | recursive | Źródło folder struktury i filtrowanie wyników (pobierane są pliki wytłuszczonym drukiem)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | Zasobnik<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | Zasobnik<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | Zasobnik<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | Zasobnik<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych, które są objęte jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
