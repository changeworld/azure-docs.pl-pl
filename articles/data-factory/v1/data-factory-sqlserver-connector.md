---
title: Przenoszenie danych do i z programu SQL Server | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z bazy danych programu SQL Server działa lokalnie lub w maszynie Wirtualnej platformy Azure przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1f1a2ec3bdf77e6f4f433c88b470e108b734fc4c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Przenoszenie danych do i z lokalnej instalacji programu SQL Server lub na IaaS (Azure VM) przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-sqlserver-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-sql-server.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika programu SQL Server w wersji 2](../connector-sql-server.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przeniesienia danych z lokalną bazą danych programu SQL Server. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z bazy danych programu SQL Server** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **bazą danych programu SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Ta obsługa łącznika programu SQL Server kopiowanie danych z i do następujących wersji wystąpienia obsługiwana lokalnie lub w IaaS platformy Azure przy użyciu zarówno uwierzytelnianie SQL i uwierzytelnianie systemu Windows: SQL Server 2016, programu SQL Server 2014, programu SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Włączenie łączności
Koncepcje i kroki niezbędne do łączenia z programu SQL Server obsługiwana lokalnie lub na maszynach wirtualnych Azure IaaS (infrastruktury jako — usługa) są takie same. W obu przypadkach należy użyć bramy zarządzania danymi dla połączenia.

Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Konfigurowanie wystąpienia bramy jest jako warunek wstępny dla łączenia z programem SQL Server.

Gdy możesz zainstalować bramę na tym samym komputerze lokalnym lub wystąpienie maszyny Wirtualnej w chmurze, co program SQL Server w celu zapewnienia lepszej wydajności, zaleca się zainstalować je na oddzielnych komputerach. Taki bramy i serwera SQL na oddzielnych komputerach zmniejsza rywalizacji.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania przenoszenia danych z lokalną bazą danych programu SQL Server przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Na przykład jeśli kopiujesz dane z bazy danych programu SQL Server do magazynu obiektów blob platformy Azure, Utwórz dwa połączone usługi, aby połączyć bazy danych programu SQL Server z kontem magazynu platformy Azure z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla bazy danych programu SQL Server, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 
3. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić tabeli SQL w bazie danych programu SQL Server, zawierający dane wejściowe. I Utwórz innego elementu dataset, aby określić folder, w którym przechowywane są dane skopiowane z bazy danych programu SQL Server i kontener obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla bazy danych programu SQL Server, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz SqlSource jako źródło i BlobSink jako zbiorniku dla działania kopiowania. Podobnie są kopiowane z magazynu obiektów Blob Azure do bazy danych programu SQL Server, należy użyć BlobSource i SqlSink w przypadku działania kopiowania. Właściwości działania kopiowania specyficzne dla bazy danych programu SQL Server, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Aby uzyskać przykłady z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z lokalną bazą danych programu SQL Server, zobacz [przykłady JSON](#json-examples-for-copying-data-from-and-to-sql-server) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do programu SQL Server: 

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Tworzenie połączonej usługi typu **OnPremisesSqlServer** do połączenia z lokalną bazą danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis specyficzne dla lokalnej usługi SQL Server połączone elementy JSON.

Poniższa tabela zawiera opis specyficzne dla usługi SQL Server połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type: **OnPremisesSqlServer**. |Yes |
| Parametry połączenia |Określ connectionString informacje wymagane do połączenia z lokalną bazą danych programu SQL Server, przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Yes |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych programu SQL Server. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **domainname\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |

Można szyfrować poświadczeń przy użyciu **AzureRmDataFactoryEncryptValue nowy** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Przykłady
**JSON dla przy użyciu uwierzytelniania programu SQL**

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
**JSON dla przy użyciu uwierzytelniania systemu Windows**

Brama zarządzania danymi zostanie spersonifikować określone konto użytkownika nawiązać połączenia z lokalną bazą danych programu SQL Server. 

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
W przykładach, użyto zestawu danych typu **SqlServerTable** do reprezentowania tabeli w bazie danych programu SQL Server.  

Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów zestawu danych (SQL Server, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **SqlServerTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych serwera SQL, odnoszący się do połączonej usługi. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Jeśli przenosisz dane z bazy danych programu SQL Server, w przypadku działania kopiowania, aby ustawić typ źródła **SqlSource**. Podobnie jeśli przenosisz dane do bazy danych programu SQL Server, należy ustawić typ ujścia w działanie kopiowania do **SqlSink**. Ta sekcja zawiera listę obsługiwanych przez SqlSource i SqlSink właściwości.

Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden parametr wejściowy i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

### <a name="sqlsource"></a>SqlSource
Gdy źródło w działaniu kopiowania jest typu **SqlSource**, są dostępne w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. Może odwoływać się wiele tabel z bazy danych odwołuje się zestaw danych wejściowych. Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: Wybierz z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatniej instrukcji SQL musi być instrukcji SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono dla SqlSource, odbywa się działanie kopii tego zapytania względem źródła danych programu SQL Server można pobrać danych.

Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select w celu uruchomienia bazy danych programu SQL Server. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

> [!NOTE]
> Jeśli używasz **sqlReaderStoredProcedureName**, należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, jednak wykonywać na tej tabeli.

### <a name="sqlsink"></a>SqlSink
**SqlSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |Zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy) sekcji. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz [powtarzalne kopiowania](#repeatable-copy) sekcji. |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury przechowywanej, która definiuje sposób dotyczą źródła danych do tabeli docelowej, np. czy upserts lub Przekształcanie przy użyciu logiki biznesowej. <br/><br/>Należy pamiętać, będzie tej procedury składowanej **wywoływane na partię**. Jeśli chcesz wykonać operację, która tylko jest uruchamiane jeden raz i nie ma nic do wykonania z źródło danych, np. Usuń/obcięcia należy użyć `sqlWriterCleanupScript` właściwości. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |
| sqlWriterTableType |Należy określić nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Przykłady JSON do kopiowania danych do programu SQL Server
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Poniższe przykłady pokazują, jak skopiować dane do i z programu SQL Server i magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Przykład: Kopiowanie danych z programu SQL Server do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli programu SQL Server do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Usługi SQL Server połączone**
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
**Azure Blob połączoną usługą magazynu**

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

Przykładzie przyjęto założenie, utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych. Kwerendy można na wiele tabel w ramach tej samej bazy danych za pomocą jednego zestawu danych, ale pojedynczej tabeli muszą być używane do typeProperty tableName zestawu danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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
**W potoku z działanie kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania tych zestawów danych wejściowych i wyjściowych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

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
W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła danych programu SQL Server można pobrać danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry). SqlReaderQuery można odwoływać się wiele tabel w bazie danych odwołuje się zestaw danych wejściowych. Nie jest ograniczona do tabeli, ustawić jako typeProperty tableName zestawu danych.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select w celu uruchomienia bazy danych programu SQL Server. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

Zobacz [źródła Sql](#sqlsource) sekcji i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listę obsługiwanych przez SqlSource i BlobSink właściwości.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Przykład: Kopiowanie danych z obiektu Blob Azure do programu SQL Server
Poniższy przykład przedstawia:

1. Połączonej usługi typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlSink](#sql-server-copy-activity-type-properties).

Kopie próbki szeregów czasowych dane z usługi Azure blob do programu SQL Server tabeli co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Usługi SQL Server połączone**

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
**Azure Blob połączoną usługą magazynu**

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
**Azure wejściowego zestawu danych obiektów Blob**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu korzysta rok, miesiąc i dzień część czas rozpoczęcia, a nazwa pliku godzina część czas rozpoczęcia. "external": ustawienie "prawda" usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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
**Zestaw danych wyjściowych programu SQL Server**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w programie SQL Server. Tworzenie tabeli w programie SQL Server z taką samą liczbę kolumn, zgodnie z oczekiwaniami pliku Blob CSV zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

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
**W potoku z działanie kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania tych zestawów danych wejściowych i wyjściowych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **SqlSink**.

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
1. Skonfiguruj program SQL Server na akceptowanie połączeń zdalnych. Uruchom **programu SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwera**i kliknij przycisk **właściwości**. Wybierz **połączeń** z listy i wyboru **zezwolenie na zdalne połączenia z serwerem**.

    ![Włącz połączenia zdalne](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Zobacz [konfigurowania dostępu zdalnego opcji konfiguracji serwera](https://msdn.microsoft.com/library/ms191464.aspx) szczegółowy opis kroków.
2. Uruchom **programu SQL Server Configuration Manager**. Rozwiń węzeł **konfigurację sieci programu SQL Server** dla tego wystąpienia, a następnie wybierz **protokoły dla elementu MSSQLSERVER**. Powinny pojawić się protokołów w prawym okienku. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i klikając **włączyć**.

    ![Włącz protokół TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zobacz [włączyć lub wyłączyć protokół sieciowy serwera](https://msdn.microsoft.com/library/ms191294.aspx) szczegółowe informacje i alternatywny sposób włączania protokołu TCP/IP.
3. W tym samym oknie, kliknij dwukrotnie **TCP/IP** można uruchomić **właściwości protokołu TCP/IP** okna.
4. Przełącz się do **adresów IP** kartę. Przewiń w dół, aby znaleźć **IPWszystkie** sekcji. Należy zanotować ** TCP Port ** (domyślnie jest **1433**).
5. Utwórz **reguły zapory systemu Windows** na maszynie, aby zezwolić na ruch przychodzący za pośrednictwem tego portu.  
6. **Sprawdź połączenie**: Aby połączyć się z serwerem SQL przy użyciu w pełni kwalifikowanej nazwy, użyj programu SQL Server Management Studio z innego komputera. Na przykład: "<machine>.<domain>. Corp.<company>.com, 1433. "

   > [!IMPORTANT]

   > Zobacz [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md) Aby uzyskać szczegółowe informacje.
   >
   > Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład, w którym kopiuje dane z tabeli źródłowej z żadnej kolumny tożsamości do tabeli docelowej z kolumny tożsamości.

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

Zwróć uwagę, że tabela docelowa ma kolumny tożsamości.

**Źródło zestawu danych JSON definicji**

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
**Docelowy zestaw danych JSON definicji**

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

Należy zauważyć, że jako tabela źródłowa i docelowa mają różne schemat (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu, należy określić **struktury** właściwości w definicji zestawu danych docelowego, który nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywołaj procedurę składowaną z zbiornika SQL
Zobacz [wywołaj procedurę składowaną dla obiekt sink SQL w przypadku działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) artykule przykład wywoływania procedury składowanej z obiektu sink SQL w działaniu kopiowania potoku.

## <a name="type-mapping-for-sql-server"></a>Mapowanie typu dla programu SQL server
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, wykonuje działanie kopiowania automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych do i z programu SQL server, następujące mapowania są używane z typu SQL typ architektury .NET i na odwrót.

Mapowanie jest taka sama jak mapowanie typu danych serwera SQL dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET framework |
| --- | --- |
| bigint |Int64 |
| Binarne |Byte[] |
| bitowe |Wartość logiczna |
| char |Ciąg, Char] |
| data |DateTime |
| Data/godzina |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atrybut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Podwójnej precyzji |
| Obraz |Byte[] |
| int |Int32 |
| oszczędność pieniędzy |Decimal |
| nchar |Ciąg, Char] |
| ntext |Ciąg, Char] |
| numeryczne |Decimal |
| nvarchar |Ciąg, Char] |
| rzeczywiste |Kawaler/panna |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Obiekt * |
| Tekst |Ciąg, Char] |
| time |TimeSpan |
| sygnatura czasowa |Byte[] |
| tinyint |Bajtów |
| uniqueidentifier |Identyfikator GUID |
| varbinary |Byte[] |
| varchar |Ciąg, Char] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Źródło mapowania na obiekt sink kolumn
Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Kopiuj powtarzalne
Podczas kopiowania danych do bazy danych serwera SQL, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Aby zamiast tego przeprowadzić UPSERT, zobacz [Repeatable zapisu SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artykułu. 

Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
