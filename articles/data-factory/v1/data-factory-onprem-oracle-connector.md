---
title: Kopiowanie danych do lub z bazy danych Oracle przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych Oracle database, która jest lokalnie przy użyciu usługi Azure Data Factory.
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
ms.openlocfilehash: 10535e75a32a9f95e759340cf14d693f43639473
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856845"
---
# <a name="copy-data-to-or-from-on-premises-oracle-using-azure-data-factory"></a>Kopiowanie danych do i z oprogramowania Oracle w środowisku lokalnym za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-onprem-oracle-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-oracle.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik Oracle w wersji 2](../connector-oracle.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory można przenieść dane z lokalnej bazy danych Oracle. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z bazy danych Oracle** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **z bazą danych Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie z lokalnymi źródłami bazy danych Oracle przy użyciu bramy zarządzania danymi. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i [przenoszenie danych ze środowiska lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy potoku danych Przenoszenie danych.

Brama jest wymagana, nawet wtedy, gdy programu Oracle znajduje się w maszynie Wirtualnej IaaS platformy Azure. Można zainstalować bramy w tej samej maszyny Wirtualnej IaaS jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Ten łącznik Oracle obsługuje dwie wersje sterowników:

- **Sterownik firmy Microsoft dla bazy danych Oracle (zalecane)**: począwszy od bramą zarządzania danymi w wersji 2.7, sterownik Oracle jest instalowany automatycznie wraz z bramy, dzięki czemu nie trzeba dodatkowo obsługi sterowników do firmy Microsoft poprawnie ustanowić połączenia Oracle i może również wystąpić podniesienia wydajności kopiowania przy użyciu tego sterownika. Poniższe wersje programu Oracle bazy danych są obsługiwane:
    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!NOTE]
> Serwer proxy Oracle nie jest obsługiwana.

> [!IMPORTANT]
> Sterownik firmy Microsoft dla oprogramowania Oracle aktualnie obsługuje tylko kopiowania danych z bazy danych Oracle, ale bez zapisywania do bazy danych Oracle. I zwróć uwagę, że możliwości połączenia testów na karcie dane diagnostyczne bramy zarządzania danych nie obsługuje tego sterownika. Alternatywnie można użyć Kreatora kopiowania, aby zweryfikować połączenie.
>

- **Dostawca danych programu Oracle dla platformy .NET:** możesz również użyć dostawca danych programu Oracle do skopiowania danych z/do oprogramowania Oracle. Ten składnik jest objęta [Oracle Data Access składników dla Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Na komputerze, na którym zainstalowano bramę, należy zainstalować odpowiednią wersję (32/64-bitowe). [Dostawca danych programu Oracle .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) dostęp do bazy danych Oracle Database 10 g wersji 2 lub nowszego.

    Jeśli wybierzesz opcję "XCopy instalacja", wykonaj czynności opisane w pliku readme.htm. Zalecamy wybierz Instalatora przy użyciu interfejsu użytkownika (innych niż XCopy jeden).

    Po zainstalowaniu dostawcy, **ponowne uruchomienie** usługi host bramy zarządzania danymi na komputerze przy użyciu usług apletu (lub) Menedżera konfiguracji bramy zarządzania danych.  

Jeśli używasz kreatora kopiowania do utworzenia potoku kopiowania typ sterownika będzie określana automatycznie. Sterownik firmy Microsoft będzie używany domyślnie, chyba że używana wersja bramy jest niższa niż w wersji 2.7 lub wybierz Oracle jako ujścia.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnej bazy danych Oracle przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Można również użyć następujących narzędzi do utworzenia potoku: **witryny Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków. 
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z bazy danych Oralce do usługi Azure blob storage, utworzysz dwie połączone usługi, aby połączyć swoje bazy danych Oracle database i konto usługi Azure storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla oprogramowania Oracle, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić tabelę w bazie danych programu Oracle, która zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić kontener obiektów blob oraz folder, który przechowuje dane skopiowane z bazy danych Oracle. Dla właściwości zestawu danych, które są specyficzne dla oprogramowania Oracle, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć OracleSource jako źródła i BlobSink jako obiekt sink dla działania kopiowania. Podobnie jeśli kopiujesz z usługi Azure Blob Storage do bazy danych Oracle Database, używasz BlobSource i elementu OracleSink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla bazy danych Oracle database, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych. 

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z bazy danych Oracle w środowisku lokalnym, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-oracle-database) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla usługi połączonej bazy danych Oracle.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesOracle** |Yes |
| driverType | Określ, który sterownik do użycia w celu skopiowania danych z/do bazy danych Oracle. Dozwolone wartości to **Microsoft** lub **ODP** (ustawienie domyślne). Zobacz [obsługiwanych wersji i instalacji](#supported-versions-and-installation) sekcji Szczegóły sterownika. | Nie |
| Parametry połączenia | Podaj informacje wymagane do nawiązania połączenia wystąpienia bazy danych programu Oracle dla właściwości connectionString. | Yes |
| gatewayName | Nazwa bramy, który jest używany do łączenia się z serwerem Oracle w środowisku lokalnym |Yes |

**Przykład: za pomocą sterownika Microsoft:**

>[!TIP]
>Jeśli napotkasz błąd powiedzenie "ORA 01025: parametr zaległej płatności za przedmiot poza zakresem" i usługi oprogramowania Oracle jest wersji 8i, Dodaj `WireProtocolMode=1` parametry połączenia i spróbuj ponownie.

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

**Przykład: użycie ODP sterownika**

Zapoznaj się [tej lokacji](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) formatów dozwolonych.

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
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Oracle, obiektów blob platformy Azure, usługa Azure table itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. W sekcji typeProperties zestawu danych typu OracleTable ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle, których połączona usługa przywołuje. |Nie (Jeśli **oracleReaderQuery** z **OracleSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

### <a name="oraclesource"></a>OracleSource
W działaniu kopiowania, gdy źródłem jest typu **OracleSource** następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable <br/><br/>Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: Wybierz * z MyTable |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

### <a name="oraclesink"></a>OracleSink
**Elementu OracleSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |przedział czasu<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 100) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. |Nazwa kolumny kolumny z typem danych binary(32). |Nie |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Przykłady JSON do kopiowania danych z bazy danych Oracle
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak skopiować dane z i do bazy danych Oracle z usługi Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Przykład: Kopiowanie danych z bazy danych Oracle do obiektów Blob platformy Azure

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako źródło i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z tabeli w bazie danych Oracle w środowisku lokalnym do obiektu blob co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości użytemu w przykładzie w dokumentacji w sekcjach poniżej przykładów.

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

**Połączona usługa Azure Blob storage:**

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

Przykład przyjęto założenie, utworzono tabelę "MyTable" Oracle i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godziny, interval: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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

**Potok za pomocą działania kopiowania:**

Potoku zawierającego działanie kopiowania jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych, która jest zaplanowane do uruchomienia co godzinę. W definicji JSON potok **źródła** ustawiono typ **OracleSource** i **ujścia** ustawiono typ **BlobSink**.  Zapytanie SQL określony za pomocą **oracleReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do bazy danych Oracle
Ten przykład pokazuje, jak skopiować dane z usługi Azure Blob Storage do bazy danych Oracle w środowisku lokalnym. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.  

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako źródło [elementu OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z obiektu blob do tabeli w bazie danych Oracle w środowisku lokalnym co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości użytemu w przykładzie w dokumentacji w sekcjach poniżej przykładów.

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

**Połączona usługa Azure Blob storage:**
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

**Usługa Azure wejściowy zestaw danych obiektów Blob**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godziny, interval: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" w usłudze Data Factory informuje, że ta tabela jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

**Wyjściowy zestaw danych Oracle:**

Przykład założono, że utworzono tabelę "MyTable" Oracle. Tworzenie tabeli w Oracle z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

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

**Potok za pomocą działania kopiowania:**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **elementu OracleSink**.  

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
### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework Data Provider

Zapoznaj się z poniższymi **komunikat o błędzie**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Możliwe przyczyny:**

1. .NET Framework Data Provider for Oracle nie został zainstalowany.
2. .NET Framework Data Provider for Oracle została zainstalowana na .NET Framework 2.0, a nie znajduje się w folderach programu .NET Framework 4.0.

**Rozpoznawanie/rozwiązania:**

1. Jeśli nie zainstalowano dostawcy .NET dla Oracle, [go zainstalować](http://www.oracle.com/technetwork/topics/dotnet/downloads/) i ponów próbę wykonania tego scenariusza.
2. Jeśli otrzymasz komunikat o błędzie, nawet po zainstalowaniu dostawcy, wykonaj następujące czynności:
   1. Otwórz maszyny konfiguracji programu .NET 2.0 z folderu: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Wyszukaj **dostawca danych programu Oracle dla platformy .NET**, i powinno być możliwe odnaleźć wpisu, jak pokazano w poniższym przykładzie w obszarze **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Dostawca danych programu oracle dla platformy .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Skopiuj ten wpis do pliku machine.config w następującym folderze v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, a następnie Zmień wersję do 4.xxx.x.x.
4. "< Ścieżka instalacji ODP.NET > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" można zainstalować w globalnej pamięci podręcznej zestawów (GAC), uruchamiając `gacutil /i [provider path]`. ## wskazówki dotyczące rozwiązywania problemów

### <a name="problem-2-datetime-formatting"></a>Problem 2: formatowanie daty/godziny

Zapoznaj się z poniższymi **komunikat o błędzie**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Rozpoznawanie/rozwiązania:**

Może być konieczne dostosowanie ciągu zapytania w swoje działania kopiowania, oparte na konfiguracji dat w bazie danych programu Oracle, jak pokazano w następującym przykładzie (przy użyciu funkcji to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapowanie typu na oprogramowanie Oracle
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania wykonuje automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych z bazy danych Oracle, następujące mapowania są używane z typu danych Oracle na typ architektury .NET i na odwrót.

| Typ danych Oracle | Typ danych .NET framework |
| --- | --- |
| BPLIK |Byte[] |
| OBIEKT BLOB |Byte[]<br/>(obsługiwane tylko w Oracle 10g i wyższych, gdy za pomocą sterownika Microsoft) |
| CHAR |Ciąg |
| CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli dokładności > 28) |
| LICZBA CAŁKOWITA |Decimal, ciąg (jeśli dokładności > 28) |
| INTERWAŁU ROKU DO MIESIĄCA |Int32 |
| INTERWAŁU DNIA DO SEKUNDY |Przedział czasu |
| DŁUGI |Ciąg |
| DŁUGI NIEPRZETWORZONE |Byte[] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| NUMER |Decimal, ciąg (jeśli dokładności > 28) |
| NVARCHAR2 |Ciąg |
| NIEPRZETWORZONE |Byte[] |
| ROWID |Ciąg |
| ZNACZNIK CZASU: |DateTime |
| SYGNATURA CZASOWA PRZY UŻYCIU LOKALNEJ STREFY CZASOWEJ |DateTime |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |DateTime |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typ danych **INTERWAŁU roku do miesiąca** i **INTERWAŁU dnia do sekundy** nie są obsługiwane, gdy za pomocą sterownika Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
