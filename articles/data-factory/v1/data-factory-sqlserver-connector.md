---
title: Przenoszenie danych do i z programu SQL Server | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych programu SQL Server, która działa lokalnie lub w Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1e70611d438678ded1260dd00a04960c798fdde5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836221"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Przenoszenie danych do i z programu SQL Server w środowisku lokalnym lub w usłudze IaaS (maszyna wirtualna Azure), za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-sqlserver-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sql-server.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik programu SQL Server w wersji 2](../connector-sql-server.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory można przenieść dane z lokalnej bazy danych programu SQL Server. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z bazy danych programu SQL Server** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **do bazy danych programu SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Ten łącznik programu SQL Server Obsługa kopiowanie danych z i do następujących wersji wystąpieniu hostowanej lokalnie lub w usłudze Azure IaaS przy użyciu zarówno uwierzytelnianie SQL i uwierzytelnianie Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Włączanie połączenia
Koncepcje i kroki niezbędne do łączenia z programu SQL Server hostowanego lokalnie lub na maszynach wirtualnych Azure IaaS (infrastruktura as-a-Service) są takie same. W obu przypadkach należy użyć bramy zarządzania danymi dla łączności.

Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Konfigurowanie wystąpienia bramy jest to wymaganie wstępne dotyczące łączenia z programem SQL Server.

Gdy bramy można zainstalować na tym samym komputerze w środowisku lokalnym lub wystąpienie maszyny Wirtualnej w chmurze, co program SQL Server w celu zapewnienia lepszej wydajności, zaleca się instalowania ich na oddzielnych komputerach. Posiadanie bramy i serwera SQL na oddzielnych komputerach powoduje zmniejszenie rywalizacji o zasoby.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnej bazy danych programu SQL Server przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z bazy danych programu SQL Server do usługi Azure blob storage, utworzysz dwie połączone usługi, aby połączyć bazy danych programu SQL Server i konta usługi Azure storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla bazy danych programu SQL Server, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
3. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić tabelę SQL w bazie danych programu SQL Server, który zawiera dane wejściowe. I utwórz inny zestaw danych, aby określić kontener obiektów blob oraz folder, który przechowuje dane skopiowane z bazy danych programu SQL Server. Aby uzyskać właściwości zestawu danych, które są specyficzne dla bazy danych programu SQL Server, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć SqlSource jako źródła i BlobSink jako obiekt sink dla działania kopiowania. Podobnie jeśli kopiujesz z usługi Azure Blob Storage do bazy danych programu SQL Server, użyjesz BlobSource i SqlSink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla bazy danych SQL Server, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykłady z definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnej bazy danych programu SQL Server, zobacz [JSON przykłady](#json-examples-for-copying-data-from-and-to-sql-server) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do programu SQL Server:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Tworzenie połączonej usługi typu **OnPremisesSqlServer** połączyć z lokalnej bazy danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla lokalnej połączonej usługi SQL Server.

Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi SQL Server.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa: **OnPremisesSqlServer**. |Yes |
| connectionString |Określ informacje o parametry połączenia potrzebne do połączenia z bazą danych programu SQL Server w środowisku lokalnym przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. |Yes |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych programu SQL Server w środowisku lokalnym. |Yes |
| username |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania Windows. Przykład: **nazwa_domeny\\username**. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |

Umożliwia ona szyfrowanie poświadczeń za pomocą **New AzDataFactoryEncryptValue** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Przykłady
**Kod JSON przy użyciu uwierzytelniania SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**Plik JSON do korzystania z uwierzytelniania Windows**

Brama zarządzania danymi spersonifikuje określonego konta użytkownika do łączenia z bazą danych programu SQL Server w środowisku lokalnym.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Przykłady, jak używać zestawu danych typu **SqlServerTable** do reprezentowania tabeli w bazie danych programu SQL Server.

Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (SQL Server, usługi Azure blob, tabela platformy Azure, itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **SqlServerTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Server, który połączona usługa przywołuje. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Jeśli chcesz przenieść dane z bazy danych programu SQL Server, należy ustawić typ źródła w działaniu kopiowania, aby **SqlSource**. Podobnie, jeśli dane są przenoszone do bazy danych programu SQL Server, należy ustawić typ ujścia w działaniu kopiowania, aby **SqlSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez SqlSource i SqlSink.

Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

### <a name="sqlsource"></a>SqlSource
Gdy źródłowego w działaniu kopiowania jest typu **SqlSource**, następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. Może odwoływać się do wielu tabel z bazy danych odwołuje się zestaw danych wejściowych. Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: Wybierz jedną z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono SqlSource uruchomieniu działania kopiowania to zapytanie względem źródła danych programu SQL Server, można pobrać danych.

Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia wybrane zapytanie, aby uruchomić względem bazy danych programu SQL Server. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Kiedy używasz **sqlReaderStoredProcedureName**, nadal należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, mimo że wykonano zadania względem tej tabeli.

### <a name="sqlsink"></a>SqlSink
**SqlSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy) sekcji. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy) sekcji. |Nazwa kolumny kolumny z typem danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, który definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy wykonuje operację UPSERT lub przekształcenie za pomocą z własną logiką biznesową. <br/><br/>Należy pamiętać, tę procedurę składowaną będzie **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic wspólnego z źródła danych, np. Usuń/truncate, użyj `sqlWriterCleanupScript` właściwości. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Należy określić nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Przykłady JSON do kopiowania danych do programu SQL Server
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Poniższe przykłady pokazują, jak można skopiować danych do i z programu SQL Server i usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Przykład: Kopiowanie danych z programu SQL Server do obiektu Blob platformy Azure
Poniższy przykład pokazuje:

1. Połączonej usługi typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy skrypt kopiuje dane szeregów czasowych z tabeli programu SQL Server do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Połączonej usługi SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Wejściowy zestaw danych programu SQL Server**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych. Można tworzyć zapytania za pośrednictwem wielu tabel w ramach tej samej bazy danych przy użyciu jednego zestawu danych, ale pojedynczej tabeli musi być używany dla typeProperty tableName typizowanego zestawu danych.

Ustawienie "external": "true" informuje usługa Data Factory, zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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
**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania tych danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **SqlSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **SqlReaderQuery** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła danych programu SQL Server, można pobrać danych. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry). SqlReaderQuery można odwoływać się do wielu tabel w bazie danych odwołuje się zestaw danych wejściowych. Nie jest ograniczona do tabeli, Ustaw jako typeProperty tableName typizowanego zestawu danych.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia wybrane zapytanie, aby uruchomić względem bazy danych programu SQL Server. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz [źródła Sql](#sqlsource) sekcji i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listy właściwości obsługiwanych przez SqlSource i BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do programu SQL Server
Poniższy przykład pokazuje:

1. Połączonej usługi typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i SqlSink.

Kopiuje przykładowe szeregów czasowych danych z usługi Azure blob do programu SQL Server tabeli co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączonej usługi SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Usługa Azure wejściowy zestaw danych obiektów Blob**

Dane są pobierane z nowy obiekt blob co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i dzień godziny rozpoczęcia, a nazwa pliku używa godzinę część czas rozpoczęcia. "external": ustawienia "true" w usłudze Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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
**Program SQL Server wyjściowy zestaw danych**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w programie SQL Server. Tworzenie tabeli w programie SQL Server z taką samą liczbę kolumn, zgodnie z oczekiwaniami plik obiektów Blob CSV będzie zawierał. Nowe wiersze są dodawane do tabeli, co godzinę.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Potok z działaniem kopiowania**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania tych danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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

## <a name="troubleshooting-connection-issues"></a>Rozwiązywanie problemów z połączeniem
1. Skonfiguruj program SQL Server do akceptowania połączeń zdalnych. Uruchom **SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwera**i kliknij przycisk **właściwości**. Wybierz **połączeń** na liście i sprawdź **Zezwalaj na połączenia zdalne z serwerem**.

    ![Włącz połączenia zdalne](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Zobacz [konfigurowania dostępu zdalnego opcji konfiguracji serwera](https://msdn.microsoft.com/library/ms191464.aspx) szczegółowy opis kroków.
2. Uruchom **Menedżera konfiguracji SQL Server**. Rozwiń **konfiguracja sieci programu SQL Server** dla tego wystąpienia, a wybierz **protokoły dla MSSQLSERVER**. Powinien zostać wyświetlony protokołów, w okienku po prawej stronie. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i klikając **Włącz**.

    ![Włącz protokół TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx) szczegółowe informacje i alternatywnych sposobów włączania protokołu TCP/IP.
3. W tym samym oknie, kliknij dwukrotnie **TCP/IP** można uruchomić **właściwości protokołu TCP/IP** okna.
4. Przełącz się do **adresów IP** kartę. Przewiń w dół, zobacz **IPWszystkie** sekcji. Zanotuj **TCP Port**(wartość domyślna to **1433**).
5. Tworzenie **reguły zapory Windows** na maszynie, aby zezwolić na przychodzący ruch za pośrednictwem tego portu.
6. **Weryfikowanie połączenia**: Aby połączyć się z serwerem SQL przy użyciu w pełni kwalifikowanej nazwy, należy użyć programu SQL Server Management Studio na innej maszynie. Na przykład: "\<maszyny\>.\< domeny\>. corp.\<firmy\>.com, 1433. "

   > [!IMPORTANT]
   > 
   > Zobacz [przenoszenie danych między źródłami lokalnymi i w chmurze przy użyciu bramy zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md) Aby uzyskać szczegółowe informacje.
   > 
   > Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.


## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład, który kopiuje dane z tabeli źródłowej, za pomocą żadnej kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródłowa:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela docelowa:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Należy zauważyć, że tabela docelowa ma kolumny tożsamości.

**Definicja JSON zestawu danych źródłowych**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definicja JSON zestawu danych docelowego**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić **struktury** właściwości w definicji zestawu danych docelowego nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywołaj procedurę składowaną z SQL ujścia
Zobacz [wywoływanie procedury składowanej SQL ujścia w działaniu kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) artykułu, na przykład wywołanie procedury składowanej z SQL ujścia w działaniu kopiowania w potoku.

## <a name="type-mapping-for-sql-server"></a>Mapowania typów dla programu SQL server
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do i z programu SQL server, następujące mapowania są używane z typu SQL na typ architektury .NET i na odwrót.

Mapowanie jest taka sama jak mapowanie typu danych serwera SQL dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ .NET framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| tekst |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mapowania źródła do ujścia kolumn
Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtarzalne kopiowania
Podczas kopiowania danych do bazy danych serwera SQL, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby zamiast tego przeprowadzić UPSERT, zobacz [Repeatable zapisu SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artykułu.

Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
