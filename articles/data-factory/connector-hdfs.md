---
title: Kopiowanie danych z systemu plików HDFS przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła systemu plików HDFS do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 606cc1904f6dea7d5829e4a5fe4e259432d7fb18
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929351"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiowanie danych z systemu plików HDFS przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hdfs-connector.md)
> * [Bieżąca wersja](connector-hdfs.md)

W tym artykule opisano sposób kopiowania danych z serwera HDFS. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik systemu plików HDFS jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Ten łącznik systemu plików HDFS obsługuje tylko następujące:

- Kopiowanie plików przy użyciu **systemu Windows** (Kerberos) lub uwierzytelniania **anonimowego** .
- Kopiowanie plików przy użyciu protokołu **webhdfs** lub **wbudowanej obsługi pomocą distcp** .
- Kopiowanie plików jako — jest lub analizowania/Generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Upewnij się, że Integration Runtime ma dostęp do **wszystkich** elementów [nazwa węzła Server]: [nazwa węzła port] i [serwery węzłów danych]: [port węzła danych] klastra Hadoop. Domyślny [nazwa portu węzła] to 50070, a domyślny [port węzła danych] to 50075.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla systemu plików HDFS.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi HDFS są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **HDFS**. | Tak |
| url |Adres URL systemu plików HDFS |Tak |
| authenticationType | Dozwolone wartości to: **Anonymous**lub **Windows**. <br><br> Aby skorzystać z **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z [tą sekcją](#use-kerberos-authentication-for-hdfs-connector) , aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika dla uwierzytelniania systemu Windows. W przypadku uwierzytelniania Kerberos Określ `<username>@<domain>.com`. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (w przypadku uwierzytelniania systemu Windows) |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład: używanie uwierzytelniania anonimowego**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: używanie uwierzytelniania systemu Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Następujące właściwości są obsługiwane w systemie plików HDFS w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość Type w obszarze `location` w elemencie DataSet musi być ustawiona na wartość **HdfsLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

>[!NOTE]
>Następujący model zestawu danych jest nadal obsługiwany w przypadku zapewnienia zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu wymienionego w powyższej sekcji, co przechodzenie do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **udziału plików** |Tak |
| folderPath | Ścieżka do folderu. Filtr symboli wieloznacznych jest obsługiwany, dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: RootFolder/subfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak |
| fileName |  **Filtr nazwy lub symbol wieloznaczny** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z część z folderem i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z część z folderem i **fileName** z filtr z symbolami wieloznacznymi.

**Przykład:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło systemu plików HDFS.

### <a name="hdfs-as-source"></a>System plików HDFS jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu plików HDFS w obszarze Ustawienia `storeSettings` w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **HdfsReadSetting**. | Tak                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi pod daną folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli nie określono `fileName` w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| distcpSettings | Grupa właściwości przy użyciu systemu HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżer zasobów przędzy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu używana do przechowywania skryptu poleceń temp pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
| distcpOptions | Dodatkowe opcje dostępne dla polecenia pomocą distcp. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="legacy-source-model"></a>Starszy model źródłowy

>[!NOTE]
>Następujący model źródłowy kopiowania jest nadal obsługiwany w przypadku zapewnienia zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu wymienionego powyżej, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **HdfsSource** |Tak |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. Należy pamiętać podczas cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynu opartego na pliku, pusty folder/podrzędnych — folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |
| distcpSettings | Grupa właściwości przy użyciu systemu HDFS pomocą distcp. | Nie |
| resourceManagerEndpoint | Punkt końcowy Menedżer zasobów przędzy | Tak, jeśli używasz pomocą distcp |
| tempScriptPath | Ścieżka folderu używana do przechowywania skryptu poleceń temp pomocą distcp. Plik skryptu jest generowany przez Data Factory i zostanie usunięty po zakończeniu zadania kopiowania. | Tak, jeśli używasz pomocą distcp |
| distcpOptions | Dodatkowe opcje dostępne dla polecenia pomocą distcp. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład: Źródło HDFS w działaniu kopiowania przy użyciu pomocą distcp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Dowiedz się więcej na temat korzystania z programu pomocą distcp w celu wydajnego kopiowania danych z systemu plików HDFS z kolejnej sekcji.

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName             | recursive | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (puste, Użyj domyślnego) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (puste, Użyj domyślnego) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Używanie pomocą distcp do kopiowania danych z systemu plików HDFS

[Pomocą distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) jest natywnym narzędziem wiersza polecenia usługi Hadoop do wykonywania kopii rozproszonej w klastrze usługi Hadoop. Po uruchomieniu polecenia pomocą distcp najpierw zostanie wystawiona lista wszystkich plików do skopiowania, utworzenie kilku zadań mapy w klastrze usługi Hadoop, a każde zadanie mapy spowoduje skopiowanie danych binarnych ze źródła do ujścia.

Obsługa działań kopiowania przy użyciu pomocą distcp do kopiowania plików jako-do obiektów blob platformy Azure (w tym [kopii etapowej](copy-activity-performance.md)) lub Azure Data Lake Store, w którym to przypadku można w pełni wykorzystać możliwości klastra zamiast uruchamiać je na samodzielnym Integration Runtime. Zapewni to lepszą przepływność kopiowania, zwłaszcza jeśli klaster jest bardzo wydajny. W oparciu o konfigurację w Azure Data Factory, działanie Copy automatycznie konstruuje polecenie pomocą distcp, przesłać je do klastra usługi Hadoop i monitorować stan kopiowania.

### <a name="prerequisites"></a>Wymagania wstępne

Aby użyć pomocą distcp do kopiowania plików z systemem HDFS do obiektu blob platformy Azure (w tym kopii przygotowanej) lub Azure Data Lake Store upewnij się, że klaster usługi Hadoop spełnia wymagania poniżej:

1. Usługi MapReduce i przędzy są włączone.
2. Wersja przędzy to 2,5 lub nowsza.
3. Serwer HDFS jest zintegrowany z docelowym magazynem danych — obiektem blob platformy Azure lub Azure Data Lake Store:

    - System plików obiektów blob platformy Azure jest natywnie obsługiwany od wersji Hadoop 2,7. Wystarczy określić ścieżkę jar w konfiguracji ENV usługi Hadoop.
    - System plików Azure Data Lake Store jest spakowany od platformy Hadoop 3.0.0-alpha1. Jeśli klaster usługi Hadoop jest niższy niż ta wersja, należy ręcznie zaimportować powiązane z usługą ADLS pakiety jar (Azure-datalake-Store. jar) do klastra z tego [miejsca](https://hadoop.apache.org/releases.html)i określić ścieżkę jar w konfiguracji ENV usługi Hadoop.

4. Przygotuj folder tymczasowy w systemie plików HDFS. Ten folder tymczasowy służy do przechowywania skryptu powłoki pomocą distcp, więc zajmie miejsce na poziomie KB.
5. Upewnij się, że konto użytkownika podane w połączonej usłudze HDFS ma uprawnienie do przesyłania aplikacji w ramach przędzy; b) mają uprawnienia do tworzenia podfolderu, plików do odczytu i zapisu w powyższym folderze tymczasowym.

### <a name="configurations"></a>Konfiguracje

Zobacz sekcję pomocą distcp powiązane konfiguracje i przykłady w obszarze [HDFS jako źródło](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla łącznika HDFS

Dostępne są dwie opcje konfigurowania środowiska lokalnego w taki sposób, aby korzystały z uwierzytelniania Kerberos w ramach łącznika systemu plików HDFS. Możesz wybrać ten, który lepiej pasuje do Twojego przypadku.
* Opcja 1: [Przyłącz się do samodzielnej maszyny Integration Runtime w obszarze Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opcja 1: Przyłącz się do samodzielnej maszyny Integration Runtime w obszarze Kerberos

#### <a name="requirements"></a>Wymagania

* Samodzielna maszyna Integration Runtime musi przyłączyć obszar Kerberos i nie może dołączać do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na samodzielnym komputerze Integration Runtime:**

1.  Uruchom narzędzie **Ksetup** , aby skonfigurować serwer i obszar KDC protokołu Kerberos.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar Kerberos różni się od domeny systemu Windows. Można to osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera KDC w następujący sposób. Zastąp *REALM.com* własnymi obszarami w miarę potrzeb.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    Po wykonaniu tych dwóch poleceń **ponownie uruchom** maszynę.

2.  Sprawdź konfigurację przy użyciu polecenia **Ksetup** . Dane wyjściowe powinny wyglądać następująco:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W Azure Data Factory:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** razem z nazwą główną i hasłem protokołu Kerberos w celu nawiązania połączenia ze źródłem danych systemu plików HDFS. Sprawdź sekcję [Właściwości połączonej usługi HDFS](#linked-service-properties) w szczegółach konfiguracji.

### <a name="kerberos-mutual-trust"></a>Opcja 2: Włącz wzajemne zaufanie między domeną systemu Windows i obszarem Kerberos

#### <a name="requirements"></a>Wymagania

*   Samodzielna maszyna Integration Runtime musi przyłączyć się do domeny systemu Windows.
*   Musisz mieć uprawnienia do aktualizowania ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> W razie konieczności Zastąp REALM.COM i AD.COM w poniższym samouczku do własnego obszaru i kontrolera domeny.

**Na serwerze KDC:**

1. Edytuj konfigurację centrum dystrybucji kluczy w pliku **krb5. conf** , aby umożliwić zaufanie centrum dystrybucji kluczy do domeny systemu Windows, odwołując się do następującego szablonu konfiguracji. Domyślnie konfiguracja znajduje się w lokalizacji **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   Po skonfigurowaniu należy **ponownie uruchomić** usługę centrum dystrybucji kluczy.

2. Przygotuj podmiot zabezpieczeń o nazwie **KRBTGT/obszaru. COM\@AD.com** na serwerze KDC przy użyciu następującego polecenia:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. W pliku konfiguracji usługi **Hadoop. Security. auth_to_local** HDFS Dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenia **Ksetup** , aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanów relację zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] jest hasłem dla głównego elementu **KRBTGT/obszaru. COM\@AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżer serwera > zasady grupy Zarządzanie > domeny > zasady grupy obiektów > domyślnych lub aktywnych zasad domeny i Edytuj.

    2. W oknie podręcznym **Edytor zarządzania zasadami grupy** przejdź do pozycji konfiguracja komputera > zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasady lokalne > opcje zabezpieczeń i skonfiguruj **zabezpieczenia sieci: Skonfiguruj typy szyfrowania dozwolone dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, który ma być używany podczas nawiązywania połączenia z centrum dystrybucji kluczy. Zazwyczaj można po prostu wybrać wszystkie opcje.

        ![Typy szyfrowania dla protokołu Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Użyj polecenia **Ksetup** , aby określić algorytm szyfrowania, który ma być używany w konkretnym obszarze.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między kontem domeny i podmiotem zabezpieczeń protokołu Kerberos, aby użyć podmiotu zabezpieczeń Kerberos w domenie systemu Windows.

    1. Uruchom narzędzia administracyjne > **Active Directory Użytkownicy i komputery**.

    2. Skonfiguruj funkcje zaawansowane, klikając pozycję **wyświetl** > **funkcje zaawansowane**.

    3. Znajdź konto, do którego chcesz utworzyć mapowania, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij kartę **nazwy Kerberos** .

    4. Dodaj podmiot zabezpieczeń z obszaru.

        ![Zamapuj tożsamość zabezpieczeń](media/connector-hdfs/map-security-identity.png)

**Na samodzielnym komputerze Integration Runtime:**

* Aby dodać wpis obszaru, uruchom następujące polecenia **Ksetup** .

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W Azure Data Factory:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** razem z kontem domeny lub podmiotem zabezpieczeń protokołu Kerberos, aby nawiązać połączenie ze źródłem danych HDFS. Sprawdź sekcję [Właściwości połączonej usługi HDFS](#linked-service-properties) w szczegółach konfiguracji.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
