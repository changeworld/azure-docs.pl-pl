---
title: Kopiowanie danych do/z programem Oracle przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych Oracle bazy danych lokalnych przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e9ed70de6d72026b8e3469417c53d6923a8a85e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021478"
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Kopiowanie danych z bazy danych Oracle lokalnymi przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-onprem-oracle-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-oracle.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika Oracle w wersji 2](../connector-oracle.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przeniesienia danych z lokalną bazą danych Oracle. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z bazy danych Oracle** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **z bazą danych Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Fabryka danych obsługuje łączenie z lokalnych źródeł Oracle przy użyciu bramy zarządzania danymi. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i [przenoszenia danych z lokalnymi do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych Przenoszenie danych.

Wymagana jest brama, nawet jeśli programu Oracle znajduje się w maszynie Wirtualnej platformy Azure IaaS. Można zainstalować bramę na tej samej maszyny Wirtualnej IaaS do przechowywania danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Ten łącznik Oracle obsługuje dwie wersje sterowników:

- **Sterownik firmy Microsoft dla programu Oracle (zalecane)**: począwszy od brama zarządzania danymi w wersji 2.7, sterownik Oracle jest instalowany automatycznie wraz z bramą, dzięki czemu nie trzeba dodatkowo obsługi sterowników do firmy Microsoft Ustanów łączność z programem Oracle, i może również wystąpić lepszą wydajność kopiowania za pomocą tego sterownika. Poniżej wersji Oracle bazy danych są obsługiwane:
    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!NOTE]
> Serwer proxy Oracle nie jest obsługiwane.

> [!IMPORTANT]
> Sterownik firmy Microsoft dla programu Oracle aktualnie obsługuje tylko kopiowanie danych z programem Oracle, ale bez zapisywania do bazy danych Oracle. I należy pamiętać, że możliwości połączenia testów na karcie diagnostyki bramy zarządzania danych nie obsługuje tego sterownika. Alternatywnie służy Kreator kopiowania Aby zweryfikować połączenie.
>

- **Dostawca danych programu Oracle dla platformy .NET:** można również użyć dostawcy danych programu Oracle można skopiować danych z i do programu Oracle. Ten składnik jest uwzględniona w [Oracle danych dostęp do składników dla systemu Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Na komputerze, na którym zainstalowano bramę, należy zainstalować odpowiednią wersję (32/64-bitowe). [Dostawca danych programu Oracle .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) możesz uzyskać dostępu do bazy danych Oracle 10 GB/s w wersji 2 lub nowszej.

    Jeśli zostanie wybrana opcja "Instalacja XCopy", wykonaj czynności opisane w pliku readme.htm. Firma Microsoft zaleca się wybranie Instalatora przy użyciu interfejsu użytkownika (z systemem innym niż — XCopy jeden).

    Po zainstalowaniu dostawcy, **ponowne uruchomienie** usługa hosta bramy zarządzania danymi na tym komputerze za pomocą usługi w aplecie (lub) Menedżera konfiguracji bramy zarządzania danymi.  

Użycie Kreatora kopiowania do tworzenia potoku kopiowania, typ sterownika będzie ustalona automatycznie. Sterownik Microsoft zostanie użyty domyślnie, chyba że używana wersja bramy jest niższa niż 2.7 lub wybierz Oracle jako obiekt sink.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania przenoszenia danych z lokalną bazą danych Oracle przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Na przykład jeśli kopiujesz dane z bazy danych Oralce do magazynu obiektów blob platformy Azure, Utwórz dwa połączone usługi, aby połączyć z bazą danych Oracle z kontem magazynu platformy Azure z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla Oracle, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji.
3. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić tabeli w bazie danych programu Oracle, który zawiera dane wejściowe. I Utwórz innego elementu dataset, aby określić folder, w którym przechowywane są dane skopiowane z bazą danych Oracle i kontener obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla Oracle, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz OracleSource jako źródło i BlobSink jako zbiorniku dla działania kopiowania. Podobnie są kopiowane z magazynu obiektów Blob Azure do bazy danych Oracle, należy użyć BlobSource i OracleSink w przypadku działania kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla baz danych programu Oracle, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładów z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z lokalną bazą danych Oracle, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-oracle-database) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi Oracle połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OnPremisesOracle** |Yes |
| driverType | Określ sterowniku można skopiować danych z/do bazy danych programu Oracle. Dozwolone wartości to **Microsoft** lub **ODP** (ustawienie domyślne). Zobacz [obsługiwanych wersji i instalacji](#supported-versions-and-installation) sekcji Szczegóły sterownika. | Nie |
| Parametry połączenia | Podaj informacje wymagane do połączenia z wystąpieniem bazy danych programu Oracle dla właściwości connectionString. | Yes |
| gatewayName | Nazwa bramy, która służy do łączenia się z serwerem Oracle lokalnej |Yes |

**Przykład: za pomocą sterownika Microsoft:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Przykład: za pomocą sterownika ODP**

Zapoznaj się [tej lokacji](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) dozwolone formaty.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Oracle, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu OracleTable ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych programu Oracle, odnoszący się do połączonej usługi. |Nie (Jeśli **oracleReaderQuery** z **OracleSource** jest określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden parametr wejściowy i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

### <a name="oraclesource"></a>OracleSource
W przypadku działania kopiowania, gdy źródłem jest typu **OracleSource** następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable <br/><br/>Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: Wybierz * z MyTable |Nie (Jeśli **tableName** z **dataset** jest określona) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |zakres czasu<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 100) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Przykłady JSON kopiowania danych do i z bazą danych Oracle
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych z/do bazy danych programu Oracle z magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Przykład: Kopiowanie danych z programu Oracle do obiektów Blob platformy Azure

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako źródło i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako obiekt sink.

Przykład kopiuje dane z tabeli w bazie danych programu Oracle lokalnego do obiektu blob co godzinę. Aby uzyskać więcej informacji na różne właściwości używane w przykładowej dokumentacji w sekcjach poniżej próbek.

**Oracle połączone usługi:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Magazyn obiektów Blob Azure połączonej usługi:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Wejściowy zestaw danych Oracle:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w oprogramowaniu Oracle i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**W potoku z działania kopiowania:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **OracleSource** i **zbiornika** ustawiono typ **BlobSink**.  Zapytanie SQL określony za pomocą **oracleReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Przykład: Kopiowanie danych z obiektu Blob Azure do programu Oracle
W tym przykładzie pokazano, jak skopiować dane z magazynu obiektów Blob platformy Azure z bazą danych Oracle lokalnymi. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.  

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako źródło [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako obiekt sink.

Przykład kopiuje dane z obiektu blob do tabeli w bazie danych programu Oracle lokalnymi co godzinę. Aby uzyskać więcej informacji na różne właściwości używane w przykładowej dokumentacji w sekcjach poniżej próbek.

**Oracle połączone usługi:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Magazyn obiektów Blob Azure połączonej usługi:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Azure wejściowego zestawu danych obiektów Blob**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" usługi fabryka danych informuje, że w tej tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Oracle wyjściowy zestaw danych:**

Przykładzie przyjęto założenie, że w Oracle utworzono tabelę "MyTable". Tworzenie tabeli w oprogramowaniu Oracle z taką samą liczbę kolumn zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**W potoku z działania kopiowania:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
### <a name="problem-1-net-framework-data-provider"></a>Problem 1: Dostawca danych programu .NET Framework

Zobacz następujące tematy **komunikat o błędzie**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Możliwe przyczyny:**

1. .NET Framework Data Provider for Oracle nie został zainstalowany.
2. .NET Framework Data Provider for Oracle została zainstalowana w .NET Framework 2.0 i nie znajduje się w folderze programu .NET Framework 4.0.

**Rozdzielczość/obejście problemu:**

1. Jeśli nie zainstalowano dostawcy .NET dla Oracle, [go zainstalować](http://www.oracle.com/technetwork/topics/dotnet/downloads/) i ponów próbę wykonania tego scenariusza.
2. Jeśli zostanie wyświetlony komunikat o błędzie nawet po zainstalowaniu dostawcy, wykonaj następujące czynności:
   1. Otwórz konfiguracji komputera programu .NET 2.0 z folderu: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Wyszukaj **dostawca danych programu Oracle dla platformy .NET**, i można znaleźć wpisu, jak pokazano w poniższym przykładzie w obszarze **system.dane** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Dostawca danych programu oracle dla platformy .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Skopiuj ten wpis w pliku machine.config w następującym folderze v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, a następnie zmień tę wersję 4.xxx.x.x.
4. Zainstaluj "\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll < ścieżka instalacji ODP.NET >" w globalnej pamięci podręcznej zestawów (GAC), uruchamiając `gacutil /i [provider path]`. ## porady dotyczące rozwiązywania problemów

### <a name="problem-2-datetime-formatting"></a>Problem 2: formatowanie daty/godziny

Zobacz następujące tematy **komunikat o błędzie**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Rozdzielczość/obejście problemu:**

Może być konieczne dostosowanie ciągu zapytania w Twojej aktywności kopiowania oparte na konfiguracji daty w bazie danych programu Oracle, jak pokazano w poniższym przykładzie (funkcja to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapowanie typu dla Oracle
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania przeprowadza automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych z bazy danych Oracle, następujące mapowania są używane z typem danych Oracle typ architektury .NET i na odwrót.

| Typ danych Oracle | Typ danych .NET framework |
| --- | --- |
| BPLIK |Byte[] |
| OBIEKT BLOB |Byte[]<br/>(obsługiwana tylko na Oracle 10 GB/s i wyższe, gdy za pomocą sterownika Microsoft) |
| CHAR |Ciąg |
| CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli precyzja > 28) |
| LICZBA CAŁKOWITA |Decimal, ciąg (jeśli precyzja > 28) |
| INTERWAŁ ROK, MIESIĄC |Int32 |
| INTERWAŁ DZIEŃ NA SEKUNDĘ |Zakres czasu |
| DŁUGA |Ciąg |
| LONG RAW |Byte[] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| NUMER |Decimal, ciąg (jeśli precyzja > 28) |
| NVARCHAR2 |Ciąg |
| NIEPRZETWORZONE |Byte[] |
| ROWID |Ciąg |
| ZNACZNIK CZASU |DateTime |
| SYGNATURA CZASOWA Z LOKALNEJ STREFIE CZASOWEJ |DateTime |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |DateTime |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typ danych **INTERWAŁ rok na miesiąc** i **na dzień INTERWAŁU drugie** nie są obsługiwane, gdy za pomocą sterownika Microsoft.

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
