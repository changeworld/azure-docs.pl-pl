---
title: Kopiowanie danych do lub z oracle przy użyciu usługi Data Factory
description: Dowiedz się, jak kopiować dane do lokalnej bazy danych Oracle lub z niej korzystając z usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265859"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiowanie danych do lub z oracle lokalnie przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-oracle-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-oracle.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Azure Data Factory, zobacz [łącznik Oracle w wersji 2](../connector-oracle.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do lub z lokalnej bazy danych Oracle. Artykuł opiera się na [działaniach przenoszenia danych](data-factory-data-movement-activities.md), który przedstawia ogólny przegląd przenoszenia danych przy użyciu działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Dane z *bazy danych Oracle* można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane można kopiować z następujących magazynów danych *do bazy danych Oracle:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Data Factory obsługuje łączenie się z lokalnymi źródłami Oracle przy użyciu bramy zarządzania danymi. Aby dowiedzieć się więcej o bramie zarządzania danymi, zobacz [Brama zarządzania danymi.](data-factory-data-management-gateway.md) Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania bramy w potoku danych w celu przenoszenia danych, zobacz [Przenoszenie danych z lokalnego do chmury](data-factory-move-data-between-onprem-and-cloud.md).

Brama jest wymagana, nawet jeśli Oracle jest hostowany w infrastrukturze platformy Azure jako maszyna wirtualna usługi (IaaS). Bramę można zainstalować na tej samej maszynie wirtualnej IaaS co magazyn danych lub na innej maszynie wirtualnej, o ile brama może łączyć się z bazą danych.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem i bramą, zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja

To złącze Oracle obsługuje dwie wersje sterowników:

- **Sterownik firmy Microsoft dla Oracle (zalecane)**: Począwszy od bramy zarządzania danymi w wersji 2.7, sterownik firmy Microsoft dla Oracle jest automatycznie instalowany z bramą. Nie trzeba instalować ani aktualizować sterownika, aby ustanowić łączność z Oracle. Można również uzyskać lepszą wydajność kopiowania przy użyciu tego sterownika. Obsługiwane są następujące wersje baz danych Oracle:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Serwer proxy Oracle nie jest obsługiwany.

    > [!IMPORTANT]
    > Obecnie sterownik Firmy Microsoft dla Oracle obsługuje tylko kopiowanie danych z Oracle. Sterownik nie obsługuje pisania do Oracle. Funkcja połączenia testowego na karcie **Diagnostyka** bramy zarządzania danymi nie obsługuje tego sterownika. Alternatywnie można użyć kreatora kopiowania, aby sprawdzić poprawność łączności.
    >

- **Dostawca danych Oracle dla platformy .NET**: Za pomocą dostawcy danych Oracle można kopiować dane z lub do Oracle. Ten składnik znajduje się w [składnikach Oracle Data Access Components dla systemu Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Zainstaluj odpowiednią wersję (32-bitową lub 64-bitową) na komputerze, na którym zainstalowana jest brama. [Dostawca danych Oracle .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) może uzyskać dostęp do bazy danych Oracle Database 10g Release 2 i nowszych wersji.

    Jeśli wybierzesz **XCopy Installation,** wykonaj kroki opisane w pliku readme.htm. Zalecamy wybranie instalatora, który ma interfejs użytkownika (a nie instalatora XCopy).

    Po zainstalowaniu dostawcy uruchom ponownie usługę hosta bramy zarządzania danymi na komputerze przy użyciu apletu Usługi lub menedżera konfiguracji bramy zarządzania danymi.

Jeśli używasz Kreatora kopiowania do tworzenia potoku kopiowania, typ sterownika jest automatycznie określony. Sterownik firmy Microsoft jest używany domyślnie, chyba że wersja bramy jest wcześniejsza niż wersja 2.7 lub użytkownik wybierze Oracle jako zlew.

## <a name="get-started"></a>Wprowadzenie

Można utworzyć potok, który ma działanie kopiowania. Potok przenosi dane do lub z lokalnej bazy danych Oracle przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie kreatora kopiowania. Zobacz [Samouczek: Tworzenie potoku za pomocą kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) do szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Można również użyć jednego z następujących narzędzi do utworzenia potoku: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager,** **.NET API**lub INTERFEJS API **REST**. Zobacz [samouczek Działania kopiowania,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku, który ma działanie kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z bazy danych Oracle do magazynu obiektów Blob platformy Azure należy utworzyć dwie połączone usługi, aby połączyć bazę danych Oracle i konto magazynu platformy Azure z fabryką danych. Aby uzyskać właściwości usługi połączone, które są specyficzne dla oracle, zobacz [Połączone właściwości usługi](#linked-service-properties).
3. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie w poprzednim kroku należy utworzyć zestaw danych, aby określić tabelę w bazie danych Oracle, która zawiera dane wejściowe. Należy utworzyć inny zestaw danych, aby określić kontener obiektów blob i folder, w który przechowuje dane skopiowane z bazy danych Oracle. Aby uzyskać właściwości zestawu danych specyficzne dla oracle, zobacz [Właściwości zestawu danych](#dataset-properties).
4. Utwórz **potok,** który ma działanie kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W poprzednim przykładzie należy użyć **OracleSource** jako źródło i **BlobSink** jako ujście dla działania kopiowania. Podobnie w przypadku kopiowania z magazynu obiektów Blob platformy Azure do bazy danych Oracle, należy użyć **BlobSource** i **OracleSink** w działania kopiowania. W przypadku właściwości działania kopiowania, które są specyficzne dla bazy danych Oracle, zobacz [Kopiowanie właściwości działania](#copy-activity-properties). Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, wybierz łącze dla magazynu danych w poprzedniej sekcji.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory są tworzone automatycznie dla Ciebie: połączone usługi, zestawy danych i potok. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów, które mają definicje JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do lub z lokalnej bazy danych Oracle, zobacz przykłady JSON.

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach JSON używanych do definiowania jednostek usługi Data Factory.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W poniższej tabeli opisano elementy JSON, które są specyficzne dla usługi połączonej Oracle:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość **typu** musi być ustawiona **na OnPremisesOracle**. |Tak |
| typ sterownika | Określ sterownik, którego ma być używany do kopiowania danych z bazy danych Oracle lub do niej. Dozwolone wartości to **Microsoft** i **ODP** (domyślnie). Szczegółowe informacje o sterowniku można znaleźć [w obszarze Obsługiwana wersja i instalacja.](#supported-versions-and-installation) | Nie |
| Parametry połączenia | Określ informacje, które są potrzebne do połączenia się z wystąpieniem bazy danych Oracle dla **connectionString** właściwości. | Tak |
| nazwa bramy | Nazwa bramy używanej do łączenia się z lokalnym serwerem Oracle. |Tak |

**Przykład: używanie sterownika firmy Microsoft**

> [!TIP]
> Jeśli widzisz błąd z napisem "ORA-01025: UPI parameter out of range", `WireProtocolMode=1` a Oracle jest w wersji 8i, dodaj do ciągu połączenia i spróbuj ponownie:

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

**Przykład: Używanie sterownika ODP**

Aby dowiedzieć się więcej o dozwolonych formatach, zobacz [Dostawca danych Oracle dla platformy .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md).

Sekcje zestawu danych JSON, takie jak struktura, dostępność i zasady, są podobne dla wszystkich typów zestawów danych (na przykład dla Oracle, Usługi Azure Blob storage i usługi Azure Table storage).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **OracleTable** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle, do których odwołuje się usługa połączona. |Nie (jeśli określono **oracleReaderQuery** lub **OracleSource)** |

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md).

Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedno dane wejściowe i generuje tylko jedno dane wyjściowe.

Właściwości, które są dostępne w sekcji **typeProperties** działania różnią się z każdego typu działania. Właściwości działania kopiowania różnią się w zależności od typu źródła i ujścia.

### <a name="oraclesource"></a>OracleSource (Źródło danych)

W copy activity, gdy źródłem jest typ **OracleSource,** następujące właściwości są dostępne w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| oracleReaderQuery (wyroczniaReaderQuery) |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład "wybierz \* z **MyTable**". <br/><br/>Jeśli nie zostanie określony, ta instrukcja SQL jest wykonywana: "wybierz \* z **MyTable**" |Nie<br />(jeśli **określono nazwa tabeli** **zestawu danych)** |

### <a name="oraclesink"></a>OracleSink (wyrocznia)

**OracleSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na operację wstawiania partii, aby zakończyć przed jej limit czasu. |**Timespan**<br/><br/> Przykład: 00:30:00 (30 minut) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie wartość **writeBatchSize**. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 100) |
| sqlWriterCleanupScript |Określa kwerendę działania kopiowania do wykonania, tak aby dane określonego plasterka zostały oczyszczone. |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określa nazwę kolumny działania kopiowania, które ma być wypełniane automatycznie generowanym identyfikatorem plasterka. Wartość **sliceIdentifierColumnName** służy do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. |Nazwa kolumny zawierającej typ danych **binarny(32)**. |Nie |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Przykłady JSON dotyczące kopiowania danych do i z bazy danych Oracle

Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Przykłady pokazują, jak skopiować dane z lub do bazy danych Oracle i do lub z magazynu obiektów Blob platformy Azure. Jednak dane mogą być kopiowane do dowolnego ujścia wymienione w [obsługiwanych magazynów danych i formatów](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

**Przykład: Kopiowanie danych z oracle do magazynu obiektów blob platformy Azure**

W przykładzie są dostępne następujące jednostki Data Factory:

* Powiązana usługa typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako źródło i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako ujście.

Przykładowy kopiuje dane z tabeli w lokalnej bazie danych Oracle do obiektu blob co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w próbce, zobacz sekcje, które należy wykonać próbki.

**Usługa połączona z Oracle**

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

**Usługa połączona z magazynem obiektów Blob platformy Azure**

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

**Zestaw danych wejściowych Oracle**

W przykładzie przyjęto założenie, że utworzono tabelę o nazwie **MyTable** w oracle. Zawiera kolumnę o nazwie **timestampcolumn** dla danych szeregów czasowych.

Ustawienie **zewnętrzne**: **true** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i że zestaw danych nie jest produkowany przez działanie w fabryce danych.

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

Dane są zapisywane w nowym obiekcie blob co godzinę **(częstotliwość:** **godzina**, **interwał**: **1**). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa roku, miesiąca, dnia i godziny w czasie rozpoczęcia.

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

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i zaplanowane do uruchamiania co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **OracleSource,** a typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL, którą określisz za pomocą właściwości **oracleReaderQuery,** wybiera dane z ostatniej godziny do skopiowania.

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

**Przykład: Kopiowanie danych z magazynu obiektów blob platformy Azure do oracle**

W tym przykładzie pokazano, jak skopiować dane z konta magazynu obiektów Blob platformy Azure do lokalnej bazy danych Oracle. Można jednak skopiować dane *bezpośrednio* z dowolnego źródła wymienione w [obsługiwanych magazynach danych i formatach](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

W przykładzie są dostępne następujące jednostki Data Factory:

* Powiązana usługa typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Wyjściowy zestaw danych](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* [Potok,](data-factory-create-pipelines.md) który ma działanie kopiowania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako źródło [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako ujście.

Przykładowy kopiuje dane z obiektu blob do tabeli w lokalnej bazie danych Oracle co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w próbce, zobacz sekcje, które należy wykonać próbki.

**Usługa połączona z Oracle**

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

**Usługa połączona z magazynem obiektów Blob platformy Azure**

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

Dane są pobierane z nowego obiektu blob co godzinę **(częstotliwość:** **godzina**, **interwał**: **1**). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa roku, miesiąca i dnia części czasu rozpoczęcia. Nazwa pliku używa części godzinnej godziny rozpoczęcia. Ustawienie **zewnętrzne**: **true** informuje usługę Data Factory, że ta tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych Oracle**

W przykładzie przyjęto założenie, że utworzono tabelę o nazwie **MyTable** w oracle. Utwórz tabelę w oracle z taką samą liczbą kolumn, które oczekujesz, że plik CSV obiektu blob będzie zawierać. Nowe wiersze są dodawane do tabeli co godzinę.

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

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i zaplanowane do uruchamiania co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource,** a typ **ujścia** jest ustawiony na **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: Dostawca danych .NET Framework

**Komunikat o błędzie**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Możliwe przyczyny**

* Dostawca danych .NET Framework dla oracle nie został zainstalowany.
* Dostawca danych .NET Framework dla oracle został zainstalowany w programie .NET Framework 2.0 i nie znajduje się w folderach .NET Framework 4.0.

**Rozdzielczość**

* Jeśli dostawca platformy .NET dla oracle nie został zainstalowany, [zainstaluj go,](https://www.oracle.com/technetwork/topics/dotnet/downloads/)a następnie ponów próbę scenariusza.
* Jeśli komunikat o błędzie jest wyświetlany nawet po zainstalowaniu dostawcy, wykonaj następujące czynności:
    1. Otwórz plik konfiguracyjny komputera dla platformy .NET 2.0\>z folderu <dysku systemowego :\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Wyszukaj **dostawcę danych Oracle dla platformy .NET**. Powinieneś być w stanie znaleźć wpis, jak pokazano w poniższym przykładzie w **obszarze system.data** > **DbProviderFactories:**`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Skopiuj ten wpis do pliku machine.config w następującym folderze\>.NET 4.0: <dysku systemowego :\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Następnie zmień wersję na 4.xxx.x.x.
* Zainstaluj <ODP.NET zainstalowaną ścieżkę\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll w globalnej pamięci podręcznej zestawów (GAC) przez uruchomienie **gacutil /i [ścieżka dostawcy]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Formatowanie daty/godziny

**Komunikat o błędzie**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Rozdzielczość**

Może być konieczne dostosowanie ciągu zapytania w działaniu kopiowania na podstawie sposobu konfigurowania dat w bazie danych Oracle. Oto przykład (przy użyciu funkcji **to_date):**

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapowanie typów dla Oracle

Jak wspomniano w [działaniach przenoszenia danych,](data-factory-data-movement-activities.md)działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia przy użyciu następującego podejścia dwuetapowego:

1. Konwertuj z natywnych typów źródeł na typ .NET.
2. Konwertuj z typu .NET na typ ujścia macierzystego.

Podczas przenoszenia danych z oracle, następujące mapowania są używane z typu danych Oracle do typu .NET i odwrotnie:

| Typ danych Oracle | Typ danych programu .NET Framework |
| --- | --- |
| Bfile |Bajt[] |
| Blob |Bajt[]<br/>(obsługiwane tylko w wersji Oracle 10g i nowszych wersjach podczas korzystania ze sterownika firmy Microsoft) |
| Char |Ciąg |
| Clob |Ciąg |
| DATE |DateTime |
| Float |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| LICZBA CAŁKOWITA |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| INTERWAŁ ROK DO MIESIĄCA |Int32 |
| INTERWAŁ OD DNIA DO SEKUNDY |przedział_czasu |
| Długi |Ciąg |
| DŁUGIE RAW |Bajt[] |
| Nchar |Ciąg |
| Nclob |Ciąg |
| Numer |Dziesiętny, Ciąg (jeśli dokładność > 28) |
| NVARCHAR2 |Ciąg |
| Raw |Bajt[] |
| Rowid |Ciąg |
| Sygnatury czasowej |DateTime |
| SYGNATURA CZASOWA Z LOKALNĄ STREFĄ CZASOWĄ |DateTime |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |DateTime |
| NIEPODPISANA GNILIZNA |Liczba |
| VARCHAR2 (VARCHAR2) |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typy danych **INTERVAL ROK DO MIESIĄCA** i **INTERWAŁ DZIEŃ NA SEKUNDĘ** nie są obsługiwane podczas korzystania ze sterownika firmy Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia

Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w ustawieniach danych](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych

Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ręcznie ponownie uruchomić plasterek. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Po ponownym uruchomieniu plasterka ręcznie lub za pomocą zasad ponawiania spróbuj, upewnij się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Aby uzyskać więcej informacji, zobacz [Powtarzalne odczytywanie ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zobacz [przewodnik wydajności i dostrajania działania kopiowania,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Możesz również dowiedzieć się o różnych sposobach optymalizacji.
