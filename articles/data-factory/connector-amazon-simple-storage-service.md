---
title: Kopiowanie danych z usługi Amazon Simple Storage Service (S3) przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o sposobie kopiowania danych z usługi Amazon Simple Storage Service (S3) do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: f8f7c33abda8d31d39051a024b9cc381c9f6b192
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387952"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiowanie danych z prostej usługi magazynu Amazon przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Bieżąca wersja](connector-amazon-simple-storage-service.md)

W tym artykule opisano sposób kopiowania danych z usługi Amazon Simple Storage Service (Amazon S3). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>Aby uzyskać informacje na temat scenariusza migracji danych z usługi Amazon S3 do magazynu Azure, Dowiedz się więcej na temat [używania Azure Data Factory do migrowania danych z usługi Amazon S3 do magazynu Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Amazon S3 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W odniesieniu do tego łącznika usługi Amazon S3 obsługuje kopiowanie plików jako pliki lub analizowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md). Używa [sygnatury AWS w wersji 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) do uwierzytelniania żądań w usłudze S3.

>[!TIP]
>Za pomocą tego łącznika Amazon S3 można kopiować dane ze **wszystkich dostawców magazynu zgodnych ze standardem S3** , np. [magazynu w chmurze Google](connector-google-cloud-storage.md). Określ odpowiedni adres URL usługi w konfiguracji połączonej usługi.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z usługi Amazon S3, upewnij się, że masz przyznane następujące uprawnienia:

- **W przypadku wykonywania działania kopiowania:** : `s3:GetObject` i `s3:GetObjectVersion` dla operacji obiektów Amazon S3.
- **W przypadku Data Factory tworzenia graficznego interfejsu użytkownika**: `s3:ListAllMyBuckets` i `s3:ListBucket` @ no__t-3 @ no__t-4 dla operacji w zasobniku usługi Amazon S3 są również wymagane w przypadku operacji takich jak połączenie testowe i przeglądanie/nawigowanie w ścieżkach plików. Jeśli nie chcesz udzielić tych uprawnień, Pomiń połączenie testowe na stronie tworzenia połączonej usługi i określ ścieżkę bezpośrednio w ustawieniach zestawu danych.

Aby uzyskać szczegółowe informacje na temat pełnej listy uprawnień usługi Amazon S3, zobacz [Określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Amazon S3.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Amazon S3 są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AmazonS3**. | Tak |
| accessKeyId | Identyfikator klucza dostępu tajnego. |Tak |
| secretAccessKey | Sam klucz dostępu tajnego. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| serviceUrl | Określ niestandardowy punkt końcowy S3, jeśli kopiujesz dane z dostawcy magazynu zgodnego ze standardem S3, innego niż Oficjalna usługa Amazon S3. Na przykład, aby skopiować dane z magazynu Google Cloud Storage, określ `https://storage.googleapis.com`. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!TIP]
>Określ niestandardowy adres URL usługi S3, jeśli kopiujesz dane z magazynu zgodnego z S3, innego niż Oficjalna usługa Amazon S3.

>[!NOTE]
>Ten łącznik wymaga kluczy dostępu dla konta usługi IAM do kopiowania danych z usługi Amazon S3. [Tymczasowe poświadczenia zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie są obsługiwane.
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

- W przypadku **Parquet, rozdzielonego tekstu, JSON, Avro i formatu binarnego**zapoznaj się z sekcją [Parquet, rozdzielaną tekstem, JSON, Avro i binarny zestaw danych](#format-based-dataset) .
- W przypadku innych formatów, takich jak **Format Orc**, zapoznaj się z sekcją [innego formatu zestawu danych](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, rozdzielony tekst, JSON, Avro i binarny zestaw danych

Aby skopiować dane z usługi Amazon S3 w **Parquet, rozdzielony tekst, JSON, Avro i format binarny**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielanym formatem tekstu](format-delimited-text.md), [formatem Avro](format-avro.md) i artykułem [binarnym](format-binary.md) w formacie zestawu danych opartym na formacie i obsługiwane Ustawienia. Następujące właściwości są obsługiwane dla usługi Amazon S3 w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość Type w obszarze `location` w zestawie danych musi być ustawiona na wartość **AmazonS3Location**. | Tak      |
| zasobnikname | Nazwa zasobnika S3.                                          | Tak      |
| folderPath | Ścieżka do folderu w danym przedziale. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w podanym zasobniku + folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| version | Wersja obiektu S3, jeśli włączono obsługę wersji S3. Jeśli nie zostanie określony, zostanie pobrana najnowsza wersja. |Nie |

> [!NOTE]
> Zestaw danych typu **AmazonS3Object** o formacie Parquet/Text wymienionym w następnej sekcji jest nadal obsługiwany w przypadku działania Copy/Lookup/GetMetadata w celu zapewnienia zgodności z poprzednimi wersjami, ale nie działa z mapowaniem przepływu danych. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

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

### <a name="other-format-dataset"></a>Inny zestaw danych formatu

Aby skopiować dane z usługi Amazon S3 w **formacie Orc**, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **AmazonS3Object** |Tak |
| zasobnikname | Nazwa zasobnika S3. Filtr symboli wieloznacznych nie jest obsługiwany. |Tak dla działania kopiowania/wyszukiwania, nie dla działania GetMetadata |
| key | **Nazwa lub filtr symboli wieloznacznych** klucza obiektu S3 w określonym przedziale. Stosuje się tylko wtedy, gdy właściwość "prefix" nie została określona. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla części folderu i nazwy pliku. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Przykład 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zobacz więcej przykładowych [przykładów w folderach i filtrach plików](#folder-and-file-filter-examples). Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu/pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| prefiks | Prefiks klucza obiektu S3. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. Stosuje się tylko wtedy, gdy właściwość "Key" nie została określona. |Nie |
| version | Wersja obiektu S3, jeśli włączono obsługę wersji S3. Jeśli nie zostanie określony, zostanie pobrana najnowsza wersja. |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale Właściwość `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie zaznaczona.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale Właściwość `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie zaznaczona.| Nie |
| Formatowanie | Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format)i sekcje [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| skompresowane | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, należy określić **zasobnik** dla zasobnika i **prefiks** dla części folderu.<br>Aby skopiować pojedynczy plik o podaną nazwę, określ wartość **dla zasobnika** i **klucza** dla części folderu oraz nazwę pliku.<br>Aby skopiować podzestaw plików w folderze, określ wartość **zasobnika** dla zasobnika i **klucza** dla części folderu oraz filtr symboli wieloznacznych.

**Przykład: używanie prefiksu**

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

**Przykład: używanie klucza i wersji (opcjonalnie)**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako źródło

- Aby skopiować z **Parquet, rozdzielony tekst, JSON, Avro i format binarny**, zapoznaj się z sekcją [Parquet, rozdzielaną tekstem, JSON, Avro i binarną](#format-based-source) .
- Aby skopiować inne formaty, takie jak **Format Orc**, zapoznaj się z sekcją [inne źródło formatu](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, rozdzielany tekstem, JSON, Avro i format binarny

Aby skopiować dane z usługi Amazon S3 w **Parquet, rozdzielony tekst, JSON, Avro i format binarny**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielonym formatem tekstowym](format-delimited-text.md), [formatem Avro](format-avro.md) i artykułem [binarnym](format-binary.md) w formacie źródłowym i obsługiwane ustawienia. Następujące właściwości są obsługiwane dla usługi Amazon S3 w obszarze Ustawienia `storeSettings` w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AmazonS3ReadSetting**. | Tak                                                         |
| rozpoznawania                | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie                                                          |
| prefiks                   | Prefiks klucza obiektu S3 w danym zasobniku skonfigurowanym w zestawie danych do filtrowania obiektów źródłowych. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. <br>Ma zastosowanie tylko wtedy, gdy nie określono właściwości `wildcardFolderPath` i `wildcardFileName`. | Nie                                                          |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi w ramach danego zasobnika skonfigurowanych w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                                          |
| wildcardFileName         | Nazwa pliku zawierająca symbole wieloznaczne w podanym zasobniku + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli `fileName` w zestawie danych i nie określono `prefix` |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale Właściwość `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie zaznaczona. | Nie                                                          |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                                          |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                                          |

> [!NOTE]
> W przypadku formatu tekstu Parquet/Unlimited Źródło działania kopiowania typu **FileSystemSource** wymienione w następnej sekcji jest nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

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

#### <a name="other-format-source"></a>Inne źródło formatowania

Aby skopiować dane z usługi Amazon S3 w **formacie Orc**, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **FileSystemSource** |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga gdy wartość cykliczna jest ustawiona na wartość true, a obiekt sink jest magazynem opartym na plikach, pusty folder/podfolder nie zostanie skopiowany/utworzony w ujścia.<br/>Dozwolone wartości to: **true** (wartość domyślna), **Fałsz** | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

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

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| porcj | key | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki pogrubione są pobierane)|
|:--- |:--- |:--- |:--- |
| porcj | `Folder*/*` | false | porcj<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| porcj | `Folder*/*` | true | porcj<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**file3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File4. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| porcj | `Folder*/*.csv` | false | porcj<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| porcj | `Folder*/*.csv` | true | porcj<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**plik1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**file3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
