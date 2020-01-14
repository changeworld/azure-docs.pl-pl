---
title: Kopiowanie danych z/do File Storage platformy Azure
description: Dowiedz się, jak skopiować dane z usługi Azure File Storage do ujścia obsługiwanych magazynów danych (lub) źródłowych obsługiwanych magazynów danych do usługi Azure File Storage przy użyciu usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2019
ms.openlocfilehash: 5a4202859ea50543784df1733fc34b1feb71f17f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893059"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Kopiowanie danych z lub do usługi Azure File Storage przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób kopiowania danych do i z usługi Azure File Storage. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure File Storage jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W szczególności ten łącznik usługi Azure File Storage obsługuje kopiowania plików jako — jest lub analizowania/Generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure File Storage.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku pliku połączoną usługę Azure Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **AzureFileStorage**. | Tak |
| host | Określa punkt końcowy usługi Azure File Storage jako: <br/>-Przy użyciu interfejsu użytkownika: Określ `\\<storage name>.file.core.windows.net\<file service name>`<br/>-Using JSON: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`. | Tak |
| userid | Określ użytkownika, który ma uzyskać dostęp do usługi Azure File Storage jako: <br/>-Przy użyciu interfejsu użytkownika: Określ `AZURE\<storage name>`<br/>-Using JSON: `"userid": "AZURE\\<storage name>"`. | Tak |
| hasło | Określ klucz dostępu do magazynu. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Brak źródła tak dla ujścia |

>[!IMPORTANT]
> - Aby skopiować dane do usługi Azure File Storage przy użyciu środowiska Azure Integration Runtime jawnie [tworzenie środowiska Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją przechowywania plików i kojarzenie w połączonej usłudze, w poniższym przykładzie.
> - Aby skopiować dane z i do usługi Azure File Storage przy użyciu własne środowisko IR spoza platformy Azure, pamiętaj, aby otworzyć wychodzącego portu TCP 445 w sieci lokalnej.

>[!TIP]
>Podczas tworzenia przy użyciu interfejsu użytkownika usługi ADF można znaleźć konkretny wpis "Azure File Storage" do tworzenia połączonej usługi, który poniżej generuje typ `FileServer` obiektu.

**Przykład:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Azure File Storage w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość Type w obszarze `location` w elemencie DataSet musi być ustawiona na wartość **FileServerLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługi Azure File Storage źródła i ujścia.

### <a name="azure-file-storage-as-source"></a>Usługa Azure File Storage jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Azure File Storage w obszarze Ustawienia `storeSettings` w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **FileServerReadSettings**. | Tak                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi pod daną folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli nie określono `fileName` w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSettings",
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

### <a name="azure-file-storage-as-sink"></a>Usługa Azure File Storage jako obiekt sink

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane przez usługę Azure File Storage w obszarze Ustawienia `storeSettings` w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **FileServerWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | recursive | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (puste, Użyj domyślnego) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (puste, Użyj domyślnego) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="recursive-and-copybehavior-examples"></a>przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 + file3 + File4 + plik 5 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 zawartości są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa File1<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **udziału plików** |Tak |
| folderPath | Ścieżka do folderu. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany, dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: RootFolder/subfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak |
| fileName | **Filtr nazwy lub symbol wieloznaczny** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]* ", np." Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to " *[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]* ", np." MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu są obsługiwane następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z część z folderem i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z część z folderem i **fileName** z filtr z symbolami wieloznacznymi.

>[!NOTE]
>Jeśli właściwość "obiektu fileFilter" była używana w filtrze plików, nadal jest obsługiwany jako — jest, gdy są sugerowane nowej możliwości filtrowania, dodane do "nazwa_pliku" idąc dalej.

**Przykład:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Model źródłowy starszego działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **FileSystemSource** |Tak |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. Należy pamiętać podczas cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynu opartego na pliku, pusty folder/podrzędnych — folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Model ujścia starszej aktywności kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type ujścia działania kopiowania: **FileSystemSink** |Tak |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scaloną nazwą pliku będzie określona nazwa; w przeciwnym razie zostanie automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
