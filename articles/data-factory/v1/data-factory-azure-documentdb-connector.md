---
title: Przenoszenie danych do/z usługi Azure Cosmos DB
description: Dowiedz się, jak przenosić dane do/z kolekcji usługi Azure Cosmos DB przy użyciu usługi Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260516"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Przenoszenie danych do i z usługi Azure Cosmos DB przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-documentdb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure Cosmos DB w wersji 2](../connector-azure-cosmos-db.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do/z usługi Azure Cosmos DB (SQL API). Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Można skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do usługi Azure Cosmos DB lub z usługi Azure Cosmos DB do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> Łącznik usługi Azure Cosmos DB obsługuje tylko interfejs API SQL.

Aby skopiować dane w stanie—jest do/z plików JSON lub innej kolekcji usługi Cosmos DB, zobacz [Importowanie/eksportowanie dokumentów JSON](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z usługi Azure Cosmos DB przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z usługi Cosmos DB, zobacz [sekcję przykładów JSON](#json-examples) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Cosmos DB:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej usługi Azure Cosmos DB.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **DocumentDb** |Tak |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z bazą danych usługi Azure Cosmos DB. |Tak |

Przykład:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zapoznaj się z artykułem [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **DocumentDbCollection** ma następujące właściwości.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| Collectionname |Nazwa kolekcji dokumentów usługi Cosmos DB. |Tak |

Przykład:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schemat według fabryki danych
W przypadku magazynów danych wolnych od schematu, takich jak usługa Azure Cosmos DB, usługa Data Factory wywnioskuje schemat w jeden z następujących sposobów:

1. Jeśli określisz strukturę danych przy użyciu właściwości **structure** w definicji zestawu danych, usługa Fabryka danych honoruje tę strukturę jako schemat. W takim przypadku jeśli wiersz nie zawiera wartości dla kolumny, zostanie dla niego podana wartość null.
2. Jeśli struktura danych nie zostanie określona przy użyciu właściwości **structure** w definicji zestawu danych, usługa Data Factory wywnioskuje schemat przy użyciu pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji kopiowania brakuje niektórych kolumn.

W związku z tym dla źródeł danych bez schematu najlepszym rozwiązaniem jest określenie struktury danych przy użyciu właściwości **structure.**

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków.](data-factory-create-pipelines.md) Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania zajmuje tylko jedno dane wejściowe i generuje tylko jedno dane wyjściowe.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania i w przypadku działania kopiowania różnią się w zależności od typów źródeł i pochłaniaczy.

W przypadku działania Kopiowania, gdy źródło jest typu **DocumentDbCollectionSource,** w sekcji **typeProperties** dostępne są następujące właściwości:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ kwerendę do odczytu danych. |Ciąg zapytania obsługiwany przez usługę Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Znak specjalny wskazujący, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Usługa Azure Cosmos DB to magazyn NoSQL dla dokumentów JSON, w którym dozwolone są struktury zagnieżdżone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczanie hierarchii za pośrednictwem nestingSeparator, który jest "". w powyższych przykładach. Za pomocą separatora działanie kopiowania wygeneruje obiekt "Name" z trzema elementami podrzędnymi First, Middle i Last, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

**DocumentDbCollectionSink** obsługuje następujące właściwości:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Specjalny znak w nazwie kolumny źródłowej wskazujący, że potrzebny jest zagnieżdżony dokument. <br/><br/>Na przykład `Name.First` powyżej: w tabeli danych wyjściowych tworzy następującą strukturę JSON w dokumencie usługi Cosmos DB:<br/><br/>"Nazwa": {<br/>    "Pierwszy": "John"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure Cosmos DB do tworzenia dokumentów.<br/><br/>Można dostosować wydajność podczas kopiowania danych do/z usługi Cosmos DB przy użyciu tej właściwości. Można oczekiwać lepszej wydajności po zwiększeniu writeBatchSize, ponieważ więcej równoległych żądań do usługi Cosmos DB są wysyłane. Jednak należy unikać ograniczania przepustowości, które mogą rzucać komunikat o błędzie: "Szybkość żądania jest duża".<br/><br/>O ograniczeniu jest wiele czynników, w tym wielkość dokumentów, liczba terminów w dokumentach, zasady indeksowania kolekcji docelowej itp. W przypadku operacji kopiowania można użyć lepszej kolekcji (np. S3), aby mieć jak najwięcej dostępnej przepływności (2500 jednostek żądań na sekundę). |Liczba całkowita |Nie (domyślnie: 5) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

## <a name="importexport-json-documents"></a>Importowanie/eksportowanie dokumentów JSON
Za pomocą tego złącza Cosmos DB można łatwo

* Importuj dokumenty JSON z różnych źródeł do usługi Cosmos DB, w tym usługi Azure Blob, usługi Azure Data Lake, lokalnego systemu plików lub innych magazynów opartych na plikach obsługiwanych przez usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Cosmos DB do różnych magazynów opartych na plikach.
* Migrowanie danych między dwiema kolekcjami usługi Cosmos DB — w stanie jednakowym.

Aby uzyskać taką kopię niezależną od schematu,
* Korzystając z kreatora kopiowania, zaznacz opcję **"Eksportuj jako jest do plików JSON lub kolekcji usługi Cosmos DB".**
* Korzystając z edycji JSON, nie należy określać sekcji "struktura" w usłudze Cosmos DB dataset(s) ani właściwości "nestingSeparator" w usłudze Cosmos DB source/sink w działaniu kopiowania. Aby zaimportować/wyeksportować do plików JSON, w zestawie danych magazynu plików określ typ formatu jako "JsonFormat", config "filePattern" i pomiń ustawienia formatu odpoczynku, zobacz sekcję [formatu JSON,](data-factory-supported-file-and-compression-formats.md#json-format) aby znaleźć szczegóły.

## <a name="json-examples"></a>Przykłady JSON
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z usługi Azure Cosmos DB i usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure Cosmos DB do obiektu Blob platformy Azure
Poniższa próbka pokazuje:

1. Połączona usługa typu [DocumentDb](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [DocumentDbCollectionSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopie dane w usłudze Azure Cosmos DB do obiektu Blob platformy Azure. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Usługa połączona z magazynem obiektów Blob platformy Azure:**

```JSON
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
**Zestaw danych wejściowych bazy danych usługi Azure Document DB:**

W przykładzie przyjęto założenie, że masz kolekcję o nazwie **Osoba** w bazie danych usługi Azure Cosmos DB.

Ustawienie "zewnętrzne": "true" i określanie informacji o zasadach externalData usługi Azure Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są kopiowane do nowego obiektu blob co godzinę ze ścieżką dla obiektu blob odzwierciedlającą określony datetime z ziarnistością godzin.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Przykładowy dokument JSON w kolekcji osoby w bazie danych usługi Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Usługa Cosmos DB obsługuje wykonywanie zapytań o dokumenty przy użyciu składni podobnej do języka SQL w stosunku do hierarchicznych dokumentów JSON.

Przykład:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Poniższy potok kopiuje dane z kolekcji osoby w bazie danych usługi Azure Cosmos DB do obiektu blob platformy Azure. W ramach działania kopiowania określono wejściowe i wyjściowe zestawy danych.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Przykład: Kopiowanie danych z obiektu blob platformy Azure do usługi Azure Cosmos DB
Poniższa próbka pokazuje:

1. Połączona usługa typu DocumentDb.
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu DocumentDbCollection.
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i DocumentDbCollectionSink.

Przykładowy kopie dane z obiektu blob platformy Azure do usługi Azure Cosmos DB. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z magazynem obiektów Blob platformy Azure:**

```JSON
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
**Usługa połączona z usługą Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Zestaw danych wejściowych obiektów Blob platformy Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Zestaw danych wyjściowych usługi Azure Cosmos DB:**

Przykładowy kopie dane do kolekcji o nazwie "Osoba".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Poniższy potok kopiuje dane z obiektu blob platformy Azure do kolekcji person w usłudze Cosmos DB. W ramach działania kopiowania określono wejściowe i wyjściowe zestawy danych.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Jeśli przykładowe dane wejściowe obiektu blob są

```
1,John,,Doe
```
Następnie wyjściowy JSON w usłudze Cosmos DB będzie następujący:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Usługa Azure Cosmos DB to magazyn NoSQL dla dokumentów JSON, w którym dozwolone są struktury zagnieżdżone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczanie hierarchii za pośrednictwem **nestingSeparator**, który jest "". w tym przykładzie. Za pomocą separatora działanie kopiowania wygeneruje obiekt "Name" z trzema elementami podrzędnymi First, Middle i Last, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli.

## <a name="appendix"></a>Dodatek
1. **Pytanie:** Czy działanie kopiowania obsługuje aktualizację istniejących rekordów?

    **Odpowiedź:** №.
2. **Pytanie:** Jak ponowna próby kopiowania do usługi Azure Cosmos DB dotyczą już skopiowanych rekordów?

    **Odpowiedź:** Jeśli rekordy mają pole "ID", a operacja kopiowania próbuje wstawić rekord o tym samym identyfikatorze, operacja kopiowania zgłasza błąd.
3. **Pytanie:** Czy usługa Data Factory obsługuje [zakres lub partycjonowanie danych oparte na skrótach?](../../cosmos-db/sql-api-partition-data.md)

    **Odpowiedź:** №.
4. **Pytanie:** Czy mogę określić więcej niż jedną kolekcję usługi Azure Cosmos DB dla tabeli?

    **Odpowiedź:** №. W tej chwili można określić tylko jedną kolekcję.

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
