---
title: Przenoszenie danych do i z programu SQL Server
description: Dowiedz się, jak przenieść dane do/z bazy danych programu SQL Server, która jest lokalna lub w maszynie Wirtualnej platformy Azure przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265768"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Przenoszenie danych do i z programu SQL Server lokalnie lub na platformie IaaS (Azure VM) przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-sqlserver-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sql-server.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik programu SQL Server w wersji 2](../connector-sql-server.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do/z lokalnej bazy danych programu SQL Server. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane z **bazy danych programu SQL Server** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane można kopiować z następujących magazynów danych **do bazy danych programu SQL Server:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Ten łącznik programu SQL Server obsługuje kopiowanie danych z/do następujących wersji wystąpień hostowanych lokalnie lub w usłudze Azure IaaS przy użyciu uwierzytelniania SQL i uwierzytelniania systemu Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Włączanie łączności
Pojęcia i kroki potrzebne do nawiązania połączenia z programem SQL Server hostowanym lokalnie lub na maszynach wirtualnych usługi Azure IaaS (Infrastructure-as-a-Service) są takie same. W obu przypadkach należy użyć bramy zarządzania danymi dla łączności.

Zobacz [przenoszenie danych między lokalizacjami lokalnymi a chmurą,](data-factory-move-data-between-onprem-and-cloud.md) aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy. Konfigurowanie wystąpienia bramy jest warunkiem wstępnym połączenia z programem SQL Server.

Chociaż można zainstalować bramę na tym samym komputerze lokalnym lub wystąpieniu maszyny wirtualnej w chmurze jako SQL Server dla lepszej wydajności, zaleca się zainstalowanie ich na oddzielnych komputerach. Posiadanie bramy i programu SQL Server na oddzielnych komputerach zmniejsza rywalizację o zasoby.

## <a name="getting-started"></a>Wprowadzenie
Potoku można utworzyć z działaniem kopiowania, które przenosi dane do/z lokalnej bazy danych programu SQL Server przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z bazy danych programu SQL Server do magazynu obiektów blob platformy Azure utworzysz dwie połączone usługi, aby połączyć bazę danych programu SQL Server i konto magazynu platformy Azure z fabryką danych. Właściwości usługi połączone, które są specyficzne dla bazy danych programu SQL Server, zobacz [sekcję właściwości usługi połączonej.](#linked-service-properties)
3. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić tabelę SQL w bazie danych programu SQL Server zawierającą dane wejściowe. I należy utworzyć inny zestaw danych, aby określić kontener obiektów blob i folder, w który przechowuje dane skopiowane z bazy danych programu SQL Server. Właściwości zestawu danych, które są specyficzne dla bazy danych programu SQL Server, zobacz sekcję [właściwości zestawu danych.](#dataset-properties)
4. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć SqlSource jako źródło i BlobSink jako ujście dla działania kopiowania. Podobnie w przypadku kopiowania z usługi Azure Blob Storage do bazy danych programu SQL Server, należy użyć BlobSource i SqlSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla bazy danych programu SQL Server, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z lokalnej bazy danych programu SQL Server, zobacz [sekcję przykłady JSON](#json-examples-for-copying-data-from-and-to-sql-server) tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla programu SQL Server:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Utworzysz połączaną usługę typu **OnPremisesSqlServer,** aby połączyć lokalną bazę danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla lokalnej usługi połączonej programu SQL Server.

Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej programu SQL Server.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu powinna być ustawiona na: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ connectionString informacje potrzebne do nawiązania połączenia z lokalną bazą danych programu SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych programu SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **\\nazwa_domeny .** |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować przy użyciu polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w ciągu połączenia, jak pokazano w poniższym przykładzie **(Właściwości EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Samples
**JSON do korzystania z uwierzytelniania SQL**

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
**JSON do korzystania z uwierzytelniania systemu Windows**

Brama zarządzania danymi będzie personifikować określone konto użytkownika, aby połączyć się z lokalną bazą danych programu SQL Server.

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
W przykładach użyto zestawu danych typu **SqlServerTable** do reprezentowania tabeli w bazie danych programu SQL Server.

Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (SQL Server, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **SqlServerTable** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych programu SQL Server, do których odwołuje się usługa połączona. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Jeśli przenosisz dane z bazy danych programu SQL Server, typ źródła w działaniu kopiowania jest ustawiany na **SqlSource**. Podobnie w przypadku przenoszenia danych do bazy danych programu SQL Server typ ujścia w działaniu kopiowania jest ustawiony na **SqlSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez SqlSource i SqlSink.

Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania zajmuje tylko jedno dane wejściowe i generuje tylko jedno dane wyjściowe.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

### <a name="sqlsource"></a>Źródło sqlsource
Gdy źródło w działaniu kopiowania jest typu **SqlSource,** następujące właściwości są dostępne w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: wybierz * z MyTable. Może odwoływać się do wielu tabel z bazy danych, do których odwołuje się wejściowy zestaw danych. Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: wybierz z MyTable. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla SqlSource, działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych programu SQL Server, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia kwerendy wybierającej do uruchomienia względem bazy danych programu SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Korzystając z **programu sqlReaderStoredProcedureName,** nadal należy określić wartość właściwości **tableName** w zestawie danych JSON. Nie ma jednak żadnych weryfikacji wykonywane dla tej tabeli.

### <a name="sqlsink"></a>SqlSink (w języku sql)
**SqlSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie dane określonego plasterka, że są czyszczone. Aby uzyskać więcej informacji, zobacz [sekcję kopiowania powtarzalnego.](#repeatable-copy) |Instrukcja kwerendy. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny działania kopiowania, aby wypełnić automatycznie wygenerowanym identyfikatorem plasterka, który jest używany do czyszczenia danych określonego plasterka podczas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz [sekcję kopiowania powtarzalnego.](#repeatable-copy) |Nazwa kolumny z typem danych binarnym(32). |Nie |
| nazwa sqlWriterStoredProcedureName |Nazwa procedury składowanej, która określa sposób stosowania danych źródłowych do tabeli docelowej, na przykład do wykonywania upserts lub przekształcania przy użyciu własnej logiki biznesowej. <br/><br/>Uwaga ta procedura **składowana będzie wywoływana na partię**. Jeśli chcesz wykonać operację, która działa tylko raz i nie ma nic wspólnego z `sqlWriterCleanupScript` danymi źródłowymi, np. |Nazwa procedury składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Określ nazwę typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Przykłady JSON do kopiowania danych z i do programu SQL Server
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Poniższe przykłady pokazują, jak skopiować dane do i z programu SQL Server i usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Przykład: Kopiowanie danych z programu SQL Server do obiektu blob platformy Azure
W poniższej próbce przedstawiono:

1. Połączona usługa typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem Kopiowania, który używa [SqlSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych z tabeli programu SQL Server do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszej kolejności skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona z programem SQL Server**
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
**Usługa połączona z magazynem obiektów Blob platformy Azure**

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
**Zestaw danych wejściowych programu SQL Server**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w programie SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych. Można wysyłać kwerendy za pośrednictwem wielu tabel w tej samej bazie danych przy użyciu pojedynczego zestawu danych, ale dla zestawu danych musi być używana pojedyncza tabelaName typeProperty zestawu danych.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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
**Potok z działaniem Kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z tych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

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
W tym przykładzie **sqlReaderQuery** jest określony dla SqlSource. Działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych programu SQL Server, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry). SqlReaderQuery może odwoływać się do wielu tabel w bazie danych, do których odwołuje się wejściowy zestaw danych. Nie jest ograniczona tylko do tabeli ustawionej jako tabela zestawu danychName typeProperty.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia kwerendy wybierającej do uruchomienia względem bazy danych programu SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz sekcji [Źródło sql](#sqlsource) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) listę właściwości obsługiwanych przez SqlSource i BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Przykład: Kopiowanie danych z obiektu blob platformy Azure do programu SQL Server
W poniższej próbce przedstawiono:

1. Połączona usługa typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z copy działania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i SqlSink.

Przykładowy kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli programu SQL Server co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z programem SQL Server**

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
**Usługa połączona z magazynem obiektów Blob platformy Azure**

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
**Zestaw danych wejściowych obiektów Blob platformy Azure**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca i dnia czasu rozpoczęcia i nazwy pliku, która używa części godzinowej godziny rozpoczęcia. "external": ustawienie "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest wytwarzany przez działanie w fabryce danych.

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
**Wyjściowy zestaw danych programu SQL Server**

Przykładowy kopiuje dane do tabeli o nazwie "MyTable" w programie SQL Server. Utwórz tabelę w programie SQL Server z taką samą liczbą kolumn, jak oczekujesz, że plik CSV obiektów blob będzie zawierał. Nowe wiersze są dodawane do tabeli co godzinę.

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
**Potok z działaniem Kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z tych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **SqlSink**.

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
1. Skonfiguruj program SQL Server tak, aby akceptował połączenia zdalne. Uruchom **program SQL Server Management Studio**, kliknij prawym przyciskiem myszy **serwer**i kliknij polecenie **Właściwości**. Wybierz **pozycję Połączenia** z listy i zaznacz pole wyboru **Zezwalaj na połączenia zdalne z serwerem**.

    ![Włącz połączenia zdalne](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Szczegółowe kroki można [znaleźć w temacie Konfigurowanie opcji konfiguracji serwera dostępu zdalnego.](https://msdn.microsoft.com/library/ms191464.aspx)
2. Uruchom **program SQL Server Configuration Manager**. Rozwiń **węzeł Konfiguracja sieci programu SQL Server** dla odpowiedniego wystąpienia i wybierz pozycję **Protokoły dla serwera MSSQLSERVER**. Protokoły powinny być widoczne w prawym okienku. Włącz protokół TCP/IP, klikając prawym przyciskiem myszy **TCP/IP** i klikając polecenie **Włącz**.

    ![Włączanie protokołu TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Szczegółowe informacje i alternatywne sposoby włączania protokołu TCP/IP można znaleźć w części [Włącz lub wyłączanie protokołu sieciowego serwera.](https://msdn.microsoft.com/library/ms191294.aspx)
3. W tym samym oknie kliknij dwukrotnie pozycję **TCP/IP,** aby uruchomić okno **Właściwości TCP/IP.**
4. Przełącz się na kartę **Adresy IP.** Przewiń w dół, aby wyświetlić sekcję **IPAll.** Zanotuj **port TCP**(wartość domyślna to **1433**).
5. Utwórz **regułę Zapory systemu Windows** na komputerze, aby zezwolić na ruch przychodzący przez ten port.
6. **Sprawdź połączenie:** Aby połączyć się z programem SQL Server przy użyciu w pełni kwalifikowanej nazwy, użyj programu SQL Server Management Studio z innego komputera. Na przykład:\<\>" maszyna . \<domena\>.corp.\<firma\>.com,1433."

   > [!IMPORTANT]
   > 
   > Szczegółowe informacje można znaleźć w aplikacji [Move data between on-premises sources and the cloud with the cloud gateway.](data-factory-move-data-between-onprem-and-cloud.md)
   > 
   > Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
W tej sekcji przedstawiono przykład, który kopiuje dane z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródło:**

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

Należy zauważyć, że tabela docelowa ma kolumnę tożsamości.

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
**Definicja JSON docelowego zestawu danych**

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

Należy zauważyć, że jako źródło i tabela docelowa mają inny schemat (obiekt docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić **właściwość struktury** w definicji docelowego zestawu danych, która nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL
Zobacz [Wywoływanie procedury składowanej dla ujścia SQL w artykule działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) na przykład wywoływania procedury składowanej z ujścia SQL w działaniu kopiowania potoku.

## <a name="type-mapping-for-sql-server"></a>Mapowanie typów dla serwera SQL
Jak wspomniano w [artykule działania przenoszenia danych,](data-factory-data-movement-activities.md) copy działania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem 2-etapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do & z serwera SQL, następujące mapowania są używane z typu SQL do typu .NET i odwrotnie.

Mapowanie jest takie samo jak mapowanie typów danych programu SQL Server dla ADO.NET.

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET Framework |
| --- | --- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |Ciąg, Char[] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Wartość dziesiętna |Wartość dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Liczba zmiennoprzecinkowa |Double |
| image |Bajt[] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |Ciąg, Char[] |
| Ntext |Ciąg, Char[] |
| numeryczne |Wartość dziesiętna |
| nvarchar |Ciąg, Char[] |
| rzeczywiste |Single |
| Rowversion |Bajt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| Sql_variant |Obiekt * |
| tekst |Ciąg, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Bajt[] |
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtarzalna kopia
Podczas kopiowania danych do bazy danych programu SQL Server działanie kopiowania domyślnie dołącza dane do tabeli ujścia. Zamiast tego należy wykonać program UPSERT, zobacz powtarzalny zapis w artykule [SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
