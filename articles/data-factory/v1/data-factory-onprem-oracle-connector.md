---
title: Kopiowanie danych do lub z bazy danych Oracle przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do lub z bazą danych Oracle w środowisku lokalnym za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ff7f92d1d13966be5d17f37210bef961f64faf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462415"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiowanie danych do i z oprogramowania Oracle w środowisku lokalnym za pomocą usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-onprem-oracle-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-oracle.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącą wersję usługi Azure Data Factory, zobacz [łącznik Oracle w wersji 2](../connector-oracle.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do lub z bazą danych Oracle w środowisku lokalnym. Artykuł opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md), który ma ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Dane można kopiować *z bazy danych Oracle* się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych *z bazą danych Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Data Factory obsługuje łączenie z lokalnymi źródłami bazy danych Oracle przy użyciu bramy zarządzania danymi. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) Aby dowiedzieć się więcej na temat bramy zarządzania danymi. Aby uzyskać instrukcje krok po kroku dotyczące sposobu konfigurowania bramy w potoku danych, aby przenieść dane, zobacz [przenoszenie danych ze środowiska lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md).

Brama jest wymagana, nawet wtedy, gdy programu Oracle znajduje się w infrastrukturze platformy Azure jako usługa (IaaS) maszyny Wirtualnej. Można zainstalować bramy w tej samej maszyny Wirtualnej IaaS jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów, które są związane połączenie i bramy, zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji

Ten łącznik Oracle obsługuje dwie wersje sterowników:

- **Sterownik firmy Microsoft dla bazy danych Oracle (zalecane)**: Począwszy od bramą zarządzania danymi w wersji 2.7 sterownik firmy Microsoft dla oprogramowania Oracle jest automatycznie instalowany z bramą. Nie potrzebujesz zainstalować lub zaktualizować sterownik do ustanowienia połączenia Oracle. Mogą także występować podniesienia wydajności kopiowania przy użyciu tego sterownika. Obsługiwane są następujące wersje baz danych Oracle:
  - R1 Oracle 12c (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Serwer proxy Oracle nie jest obsługiwane.

    > [!IMPORTANT]
    > Obecnie sterownik firmy Microsoft dla bazy danych Oracle obsługuje tylko kopiowanie danych z bazy danych Oracle. Sterownik nie obsługuje zapisywania do bazy danych Oracle. Możliwość połączenia testowego w ramach bramy zarządzania danymi **diagnostyki** karta nie obsługuje tego sterownika. Alternatywnie można użyć Kreatora kopiowania, aby zweryfikować połączenie.
    >

- **Dostawca danych programu Oracle dla platformy .NET**: Dostawca danych programu Oracle można użyć do skopiowania danych z lub do bazy danych Oracle. Ten składnik jest objęta [Oracle Data Access składników dla Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Odpowiedniej wersji (32-bitowy lub 64-bitowych) należy zainstalować na komputerze, na którym zainstalowano bramę. [Dostawca danych programu Oracle .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) mają dostęp do bazy danych Oracle Database 10 g w wersji 2 i nowsze wersje.

    Jeśli wybierzesz **instalacji XCopy**, wykonaj kroki, które są opisane w pliku readme.htm. Zalecane jest wybranie opcji Instalatora, która ma interfejs użytkownika (a nie Instalator XCopy).

    Po zainstalowaniu dostawcy należy ponownie uruchomić usługę hosta bramy zarządzania danymi na komputerze przy użyciu Menedżera konfiguracji bramy zarządzania danych lub apletu usługi.

Jeśli używasz kreatora kopiowania do utworzenia potoku kopiowania typ sterownika jest autodetermined. Sterownik firmy Microsoft jest używany domyślnie, chyba że używana wersja bramy jest starsza niż wersja 2.7, lub wybierz Oracle jako obiekt sink.

## <a name="get-started"></a>Rozpoczęcie pracy

Można utworzyć potok, który zawiera działania kopiowania. Potok przenosi dane z lokalnej bazy danych Oracle lub przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie Kreatora kopiowania. Zobacz [samouczka: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także jedną z następujących narzędzi do tworzenia potoku: **witryny Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **usługi Azure Resource Manager Szablon**, **interfejsu API platformy .NET**, lub **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku dotyczące sposobu tworzenia potoku, który zawiera działania kopiowania.

Czy używasz narzędzi lub interfejsów API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z bazy danych Oracle do usługi Azure Blob storage, Utwórz dwie połączone usługi, aby połączyć swoje bazy danych Oracle database i konto usługi Azure storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla oprogramowania Oracle, zobacz [właściwości usługi połączonej](#linked-service-properties).
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W przykładzie w poprzednim kroku utworzysz zestaw danych, aby określić tabelę w bazie danych programu Oracle, która zawiera dane wejściowe. Możesz utworzyć inny zestaw danych, aby określić kontener obiektów blob oraz folder, który przechowuje dane skopiowane z bazy danych Oracle. Dla właściwości zestawu danych, które są specyficzne dla oprogramowania Oracle, zobacz [właściwości zestawu danych](#dataset-properties).
4. Tworzenie **potoku** zawierającego działanie kopiowania, które przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyjesz **OracleSource** jako źródło i **BlobSink** jako obiekt sink dla działania kopiowania. Podobnie, jeśli są kopiowane z usługi Azure Blob storage do bazy danych Oracle, możesz użyć **BlobSource** i **elementu OracleSink** w działaniu kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla bazy danych Oracle, zobacz [właściwości działania kopiowania](#copy-activity-properties). Aby uzyskać szczegółowe informacje o sposobie używania magazynu danych jako źródła lub ujścia wybierz link dla magazynu danych w poprzedniej sekcji.

Korzystając z kreatora, definicje JSON dotyczące jednostek usługi Data Factory, te są tworzone automatycznie dla Ciebie: połączone usługi, zestawy danych i potoku. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady, które mają definicje JSON dotyczące jednostek usługi Data Factory, które służy do kopiowania danych do lub z bazą danych Oracle w środowisku lokalnym, zobacz przykłady JSON.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które służą do definiowania jednostek usługi Data Factory.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

W poniższej tabeli opisano elementy JSON, które są specyficzne dla usługi połączonej bazy danych Oracle:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |**Typu** właściwość musi być równa **OnPremisesOracle**. |Yes |
| driverType | Określ, który sterownik do użycia w celu skopiowania danych z lub z bazą danych Oracle. Dozwolone wartości to **Microsoft** i **ODP** (ustawienie domyślne). Zobacz [obsługiwanych wersji i instalacji](#supported-versions-and-installation) Szczegóły sterownika. | Nie |
| connectionString | Podaj informacje potrzebne do nawiąż połączenie z wystąpieniem bazy danych programu Oracle dla **connectionString** właściwości. | Yes |
| gatewayName | Nazwa bramy, który służy do łączenia się z serwerem Oracle w środowisku lokalnym. |Yes |

**Przykład: Za pomocą sterownika Microsoft**

> [!TIP]
> Jeśli zostanie wyświetlony błąd, który mówi "ORA 01025: Parametr zaległej płatności za przedmiot poza zakresem"i usługi Oracle jest w wersji 8i, Dodaj `WireProtocolMode=1` parametry połączenia i spróbuj ponownie:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Przykład: Za pomocą sterownika ODP**

Aby dowiedzieć się więcej na temat dopuszczalnych formatów, zobacz [dostawca danych programu Oracle dla platformy .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md).

Sekcje pliku JSON zestawu danych, takie jak struktury, dostępność i zasady, są podobne dla wszystkich typów w zestawie danych (na przykład w przypadku bazy danych Oracle, usługi Azure Blob storage i Azure Table storage).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **OracleTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle, których połączona usługa przywołuje. |Nie (Jeśli **oracleReaderQuery** lub **OracleSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenia potoków](data-factory-create-pipelines.md).

Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Właściwości działania kopiowania różnią się w zależności od typu źródła i ujścia.

### <a name="oraclesource"></a>OracleSource

W działaniu kopiowania, gdy źródłem jest **OracleSource** , następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład "Wybierz \* z **MyTable**". <br/><br/>Jeśli nie zostanie określony, to instrukcja SQL jest wykonywana: "Wybierz \* z **MyTable**" |Nie<br />(Jeśli **tableName** z **dataset** zostanie określona) |

### <a name="oraclesink"></a>OracleSink

**Elementu OracleSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania dla partii wstawić na zakończenie przed upływem limitu czasu operacji. |**timespan**<br/><br/> Przykład: 00:30:00 (30 minut) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie wartość **writeBatchSize**. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 100) |
| sqlWriterCleanupScript |Określa kwerendę dla działania kopiowania, które można wykonać, aby po oczyszczeniu danych określonego wycinka. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określa nazwę kolumny działanie kopiowania w celu wypełnienia odpowiadającym wycinek wygenerowany automatycznie. Wartość **sliceIdentifierColumnName** służy do czyszczenia danych określonego wycinka, gdy ponowne uruchomienie. |Nazwa kolumny, która ma typ danych kolumny **binary(32)**. |Nie |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Przykłady JSON do kopiowania danych z bazy danych Oracle

W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładach pokazano, jak skopiować dane z lub z bazą danych Oracle i do / z usługi Azure Blob storage. Jednak można skopiować danych do dowolnego ujścia, na liście [obsługiwane magazyny danych i formatów](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

**Przykład: Kopiowanie danych z bazy danych Oracle do usługi Azure Blob storage**

Przykład obejmuje następujących jednostek usługi Data Factory:

* Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako źródło i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z tabeli w bazie danych Oracle w środowisku lokalnym do obiektu blob co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w przykładzie zobacz sekcje, które należy wykonać przykłady.

**Usługi połączone programu Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Obiekt Blob połączona usługa Azure storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Wejściowy zestaw danych Oracle**

Przykład założono, że utworzono tabelę o nazwie **MyTable** Oracle. Zawiera kolumnę o nazwie **timestampcolumn** danych szeregów czasowych.

Ustawienie **zewnętrznych**: **true** usługi Data Factory informuje, że zestaw danych jest zewnętrzne w stosunku do usługi data factory i że zestaw danych nie jest generowany przez działanie w usłudze data factory.

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

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob, co godzinę (**częstotliwość**: **godzinę**, **interwał**: **1**). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i godzinę część czasu rozpoczęcia.

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

**Potok z działaniem kopiowania**

Potoku zawierającego działanie kopiowania, który został skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i zaplanowane co godzinę. W definicji JSON potok **źródła** ustawiono typ **OracleSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, które należy określić za pomocą **oracleReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Przykład: Kopiowanie danych z usługi Azure Blob storage do bazy danych Oracle**

Ten przykład pokazuje, jak skopiować dane z konta usługi Azure Blob storage do bazy danych Oracle w środowisku lokalnym. Jednak może kopiować dane *bezpośrednio* z dowolnego źródła w [obsługiwane magazyny danych i formatów](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

Przykład obejmuje następujących jednostek usługi Data Factory:

* Połączonej usługi typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) zawierającego działanie kopiowania, które używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako źródło [elementu OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z obiektu blob do tabeli w bazie danych Oracle w środowisku lokalnym co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w przykładzie zobacz sekcje, które należy wykonać przykłady.

**Usługi połączone programu Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Obiekt Blob połączona usługa Azure storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Wejściowy zestaw danych obiektów blob platformy Azure**

Dane są pobierane z nowy obiekt blob co godzinę (**częstotliwość**: **godzinę**, **interwał**: **1**). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień część czasu rozpoczęcia. Nazwa pliku używa godzinę część czas rozpoczęcia. Ustawienie **zewnętrznych**: **true** usługi Data Factory informuje, że ta tabela jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

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

**Oracle wyjściowy zestaw danych**

Przykład przyjęto założenie, utworzono tabelę o nazwie **MyTable** Oracle. Tworzenie tabeli w Oracle z taką samą liczbę kolumn, których oczekujesz, że plik CSV obiektów blob, który będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

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

**Potok z działaniem kopiowania**

Potoku zawierającego działanie kopiowania, który został skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **elementu OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

**komunikat o błędzie**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Możliwe przyczyny**

* .NET Framework Data Provider for Oracle nie został zainstalowany.
* .NET Framework Data Provider for Oracle została zainstalowana na .NET Framework 2.0 i nie zostanie odnaleziony w folderach programu .NET Framework 4.0.

**Rozdzielczość**

* Jeśli nie zainstalowano dostawcy .NET dla Oracle, [go zainstalować](https://www.oracle.com/technetwork/topics/dotnet/downloads/), a następnie ponów próbę wykonania tego scenariusza.
* Jeśli widzisz komunikat o błędzie, nawet po zainstalowaniu dostawcy, wykonaj następujące czynności:
    1. Otwórz plik konfiguracji maszyny dla platformy .NET w wersji 2.0 z folderu < dysk systemowy\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Wyszukaj **dostawca danych programu Oracle dla platformy .NET**. Powinno być możliwe odnaleźć wpisu, jak pokazano w poniższym przykładzie w obszarze **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Skopiuj ten wpis do pliku machine.config w następującym folderze .NET 4.0: < dysk systemowy\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Następnie należy zmienić wersję na 4.xxx.x.x.
* Zainstaluj < ścieżka zainstalowane ODP.NET\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll w globalnej pamięci podręcznej zestawów (GAC), uruchamiając **gacutil /i [ścieżka dostawcy]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Formatowanie daty/godziny

**komunikat o błędzie**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Rozdzielczość**

Może być konieczne dostosowanie ciągu zapytania w swoje działania kopiowania, oparte na konfiguracji dat w bazie danych programu Oracle. Oto przykład (przy użyciu **to_date** funkcji):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapowanie typu na oprogramowanie Oracle

Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md), działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejście dwuetapowe:

1. Przekonwertować na typ architektury .NET typy natywne źródła.
2. Przekonwertować typ architektury .NET na typ ujścia natywnych.

Podczas przenoszenia danych z bazy danych Oracle, następujące mapowania są używane z typu danych Oracle na typ architektury .NET i na odwrót:

| Typ danych Oracle | Typ danych .NET framework |
| --- | --- |
| BPLIK |Byte[] |
| BLOB |Byte[]<br/>(obsługiwane wyłącznie w Oracle 10g i nowszych wersjach po użyciu sterownika Microsoft) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli dokładności > 28) |
| INTEGER |Decimal, ciąg (jeśli dokładności > 28) |
| INTERWAŁU ROKU DO MIESIĄCA |Int32 |
| INTERWAŁU DNIA DO SEKUNDY |TimeSpan |
| DŁUGI |String |
| DŁUGI NIEPRZETWORZONE |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMER |Decimal, ciąg (jeśli dokładności > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| SYGNATURA CZASOWA |DateTime |
| SYGNATURA CZASOWA PRZY UŻYCIU LOKALNEJ STREFY CZASOWEJ |DateTime |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |DateTime |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Typy danych **INTERWAŁU roku do miesiąca** i **INTERWAŁU dnia do sekundy** nie są obsługiwane w przypadku używania sterownik firmy Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn

Aby dowiedzieć się więcej na temat mapowania kolumn w zestawie danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych

Podczas kopiowania danych z magazynów danych relacyjnych, pamiętać powtarzalności Aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ręcznie ponownie uruchomić wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, tak, aby wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie, albo ręcznie lub przez zasady ponawiania, upewnij się, że te same dane jest odczyt niezależnie od tego, jak wiele razy wycinek jest uruchamiany. Aby uzyskać więcej informacji, zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Można także poznasz różne sposoby, aby zoptymalizować ją.
