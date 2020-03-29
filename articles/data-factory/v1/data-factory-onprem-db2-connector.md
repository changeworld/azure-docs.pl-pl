---
title: Przenoszenie danych z bazy danych 2 przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z lokalnej bazy danych DB2 przy użyciu działania kopiowania usługi Azure Data Factory Copy
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931784"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Przenoszenie danych z bazy danych 2 przy użyciu działania kopiowania fabrycznego usługi Azure
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-db2-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-db2.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik DB2 w wersji 2](../connector-db2.md).


W tym artykule opisano, jak można użyć działania kopiowania w usłudze Azure Data Factory do kopiowania danych z lokalnej bazy danych DB2 do magazynu danych. Można skopiować dane do dowolnego magazynu, który jest wymieniony jako obsługiwany zlew w [artykule Działania przenoszenia danych fabryki danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) W tym temacie opiera się na fabryce danych artykuł, który przedstawia omówienie przenoszenia danych przy użyciu działania kopiowania i wyświetla listę obsługiwanych kombinacji magazynu danych. 

Usługa Data Factory obsługuje obecnie tylko przenoszenie danych z bazy danych DB2 do [obsługiwanego magazynu danych ujścia.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Przenoszenie danych z innych magazynów danych do bazy danych DB2 nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie się z lokalną bazą danych DB2 przy użyciu [bramy zarządzania danymi](data-factory-data-management-gateway.md). Aby uzyskać instrukcje krok po kroku, aby skonfigurować potok danych bramy do przenoszenia danych, zobacz [Przenieś dane z lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

Brama jest wymagana, nawet jeśli db2 jest hostowany na maszynie Wirtualnej usługi Azure IaaS. Bramę można zainstalować na tej samej maszynie wirtualnej IaaS, co magazyn danych. Jeśli brama może łączyć się z bazą danych, można zainstalować bramę na innej maszynie wirtualnej.

Brama zarządzania danymi udostępnia wbudowany sterownik DB2, więc nie trzeba ręcznie instalować sterownika do kopiowania danych z bazy danych DB2.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów z połączeniem i bramą, zobacz artykuł [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="supported-versions"></a>Obsługiwane wersje
Złącze Data Factory DB2 obsługuje następujące platformy i wersje IBM DB2 z programem SQL Access Manager w wersjach 9, 10 i 11:

* IBM DB2 dla wersji z/OS 11.1
* IBM DB2 dla wersji z/OS 10.1
* IBM DB2 dla i (AS400) w wersji 7.2
* IBM DB2 dla i (AS400) w wersji 7.1
* IBM DB2 dla systemów Linux, UNIX i Windows (LUW) w wersji 11
* IBM DB2 dla LUW w wersji 10.5
* IBM DB2 dla LUW w wersji 10.1

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "Pakiet odpowiadający żądaniu wykonania instrukcji SQL nie został znaleziony. SQLSTATE=51002 SQLCODE=-805", powodem jest niezbędny pakiet nie jest tworzony dla zwykłego użytkownika w os. Aby rozwiązać ten problem, postępuj zgodnie z poniższymi instrukcjami dotyczącymi typu serwera DB2:
> - DB2 for i (AS400): Pozwól użytkownikowi zasilcemu utworzyć kolekcję dla zwykłego użytkownika przed uruchomieniem działania kopiowania. Aby utworzyć kolekcję, użyj polecenia:`create collection <username>`
> - DB2 dla z/OS lub LUW: Użyj konta o wysokich uprawnieniach - użytkownika lub administratora, który ma władze pakietu i BIND, BINDADD, GRANT EXECUTE TO PUBLIC permissions - aby uruchomić kopię raz. Niezbędny pakiet jest tworzony automatycznie podczas kopiowania. Następnie można przełączyć się z powrotem do zwykłego użytkownika dla kolejnych uruchomień kopii.

## <a name="getting-started"></a>Wprowadzenie
Potok z działaniem kopiowania można utworzyć, aby przenieść dane z lokalnego magazynu danych DB2 przy użyciu różnych narzędzi i interfejsów API: 

- Najprostszym sposobem utworzenia potoku jest użycie Kreatora kopiowania fabryki danych platformy Azure. Aby uzyskać szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md). 
- Można również użyć narzędzi do tworzenia potoku, w tym Visual Studio, Azure PowerShell, szablon usługi Azure Resource Manager, .NET API i interfejsu API REST. Aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania, zobacz [samouczek Kopiowania działania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie połączonych usług w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz potok z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z Kreatora kopiowania, definicje JSON dla usług połączonych usługi, zestawy danych i jednostek potoku są tworzone automatycznie dla Ciebie. Podczas korzystania z narzędzi lub interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować jednostki fabryki danych przy użyciu formatu JSON. Przykład JSON: Kopiowanie danych z magazynu obiektów Blob DB2 do platformy Azure zawiera definicje JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych DB2.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek fabryki danych, które są specyficzne dla magazynu danych DB2.

## <a name="db2-linked-service-properties"></a>Właściwości usługi połączonej DB2
W poniższej tabeli wymieniono właściwości JSON, które są specyficzne dla usługi połączonej DB2.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **Typu** |Ta właściwość musi być ustawiona **na OnPremisesDb2**. |Tak |
| **Serwera** |Nazwa serwera DB2. |Tak |
| **Bazy danych** |Nazwa bazy danych DB2. |Tak |
| **Schematu** |Nazwa schematu w bazie danych DB2. W tej właściwości rozróżniana jest wielkość liter. |Nie |
| **authenticationType** |Typ uwierzytelniania, który jest używany do łączenia się z bazą danych DB2. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| **Nazwę użytkownika** |Nazwa konta użytkownika w przypadku korzystania z uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| **hasło** |Hasło do konta użytkownika. |Nie |
| **nazwa bramy** |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych DB2. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak **struktura,** **dostępność**i **zasady** dla zestawu danych JSON, są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure Blob storage, Azure Table Storage i tak dalej).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **RelationalTable**, która zawiera zestaw danych DB2, ma następującą właściwość:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w wystąpieniu bazy danych DB2, do których odwołuje się usługa połączona. W tej właściwości rozróżniana jest wielkość liter. |Nie (jeśli określono właściwość **kwerendy** działania kopiowania typu **RelationalSource)** |

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania
Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania działań kopiowania, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości działania kopiowania, takie jak **nazwa,** **opis,** **tabela danych wejściowych,** **tabela danych wyjściowych** i **zasady,** są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji **typeProperties** działania różnią się dla każdego typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł danych i pochłaniacze.

W przypadku działania kopiowania, gdy źródłem jest typ **RelationalSource** (który zawiera DB2), w sekcji **typeProperties** dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| **query** |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""` |Nie (jeśli określono właściwość **tableName** zestawu danych) |

> [!NOTE]
> W nazwach schematów i tabel rozróżniana jest wielkość liter. W instrukcji kwerendy należy ująć nazwy właściwości przy użyciu "" (cudzysłowy).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z usługi DB2 do magazynu obiektów blob platformy Azure
W tym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) W przykładzie pokazano, jak skopiować dane z bazy danych DB2 do magazynu obiektów Blob. Jednak dane mogą być kopiowane do [dowolnego typu magazynu danych obsługiwanych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu usługi Azure Data Factory Copy Activity.

W przykładzie są dostępne następujące jednostki Data Factory:

- Usługa połączona DB2 typu [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Usługa połączona z magazynem obiektów Blob platformy Azure typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa właściwości [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykładowy kopiuje dane z kwerendy spowodować bazy danych DB2 do obiektu blob platformy Azure co godzinę. Właściwości JSON, które są używane w przykładzie są opisane w sekcjach, które są zgodne z definicjami jednostki.

W pierwszym kroku zainstaluj i skonfiguruj bramę danych. Instrukcje znajdują się w [artykule Przenoszenie danych między lokalizacjami lokalnymi a chmurą.](data-factory-move-data-between-onprem-and-cloud.md)

**Usługa połączona DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Usługa połączona z magazynem obiektów Blob platformy Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Zestaw danych wejściowych DB2**

W przykładzie przyjęto założenie, że utworzono tabelę w DB2 o nazwie "MyTable", która ma kolumnę oznaczona jako "sygnatura czasowa" dla danych szeregów czasowych.

Właściwość **zewnętrzna** jest ustawiona na "true". To ustawienie informuje usługę Data Factory, że ten zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych. Należy zauważyć, że właściwość **typu** jest ustawiona na **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę, ustawiając właściwość **frequency** na "Godzina" i właściwość **interwału** na 1. Właściwość **folderPath** dla obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny godziny godziny rozpoczęcia.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok dla działania kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania określonych danych wejściowych i wyjściowych i który jest zaplanowane do uruchomienia co godzinę. W definicji JSON dla potoku typ **źródła** jest ustawiony na **RelationalSource,** a typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane z tabeli "Zamówienia".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapowanie typów dla bazy danych DB2
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typu z typu źródłowego do typu ujścia przy użyciu następującego podejścia dwuetapowego:

1. Konwertowanie z macierzystego typu źródła na typ .NET
2. Konwertowanie z typu .NET na typ ujścia macierzystego

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z typu DB2 na typ .NET:

| Typ bazy danych DB2 | Typ programu .NET Framework |
| --- | --- |
| Smallint |Int16 |
| Liczba całkowita |Int32 |
| Bigint |Int64 |
| Rzeczywiste |Single |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Wartość dziesiętna |Wartość dziesiętna |
| Podłoga dziesiętna |Wartość dziesiętna |
| Liczbowe |Wartość dziesiętna |
| Data |DateTime |
| Time |przedział_czasu |
| Znacznik czasu |DateTime |
| Xml |Bajt[] |
| Char |Ciąg |
| Varchar |Ciąg |
| LongVarChar (LongVarChar) |Ciąg |
| DB2DynArray |Ciąg |
| plików binarnych |Bajt[] |
| Varbinary |Bajt[] |
| LongVarBinary (LongVarBinary) |Bajt[] |
| Graficzny |Ciąg |
| Grafika vargraficzna |Ciąg |
| LongVarGraphic (LongVarGraphic) |Ciąg |
| Clob |Ciąg |
| Obiekt blob |Bajt[] |
| DbClob ( DbClob ) |Ciąg |
| Smallint |Int16 |
| Liczba całkowita |Int32 |
| Bigint |Int64 |
| Rzeczywiste |Single |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Wartość dziesiętna |Wartość dziesiętna |
| Podłoga dziesiętna |Wartość dziesiętna |
| Liczbowe |Wartość dziesiętna |
| Data |DateTime |
| Time |przedział_czasu |
| Znacznik czasu |DateTime |
| Xml |Bajt[] |
| Char |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się, jak mapować kolumny w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalne odczyty ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnego magazynu danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować właściwość **zasad** ponawiania próby dla zestawu danych, aby ponownie uruchomić plasterek po wystąpieniu błędu. Upewnij się, że te same dane są odczytywane bez względu na to, ile razy plasterek jest ponownie wprowadzany i niezależnie od sposobu ponownego uruchomienia plasterka. Aby uzyskać więcej informacji, zobacz [Powtarzalne odczyty ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Dowiedz się więcej o kluczowych czynnikach, które wpływają na wydajność działania kopiowania i sposobach optymalizacji wydajności w [przewodniku Wydajność i dostrajanie działania kopiowania](data-factory-copy-activity-performance.md).
