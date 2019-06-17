---
title: Przenoszenie danych do i z usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane do/z kolekcji usługi Azure Cosmos DB przy użyciu usługi Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bda3df3ce869d7717f572f72c38472e7eae4a0ef
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60567218"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Przenoszenie danych do i z usługi Azure Cosmos DB przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-azure-documentdb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure Cosmos DB w wersji 2](../connector-azure-cosmos-db.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z usługi Azure Cosmos DB (interfejs API SQL). Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z obsługiwanego źródłowego magazynu danych do usługi Azure Cosmos DB lub z usługi Azure Cosmos DB do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła lub ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli.

> [!IMPORTANT]
> Łącznik usługi Azure Cosmos DB obsługuje tylko interfejsu API SQL.

Aby skopiować dane jako — jest do i z plików JSON lub innej kolekcji usługi Cosmos DB, zobacz [dokumentów JSON Import/Export](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure Cosmos DB przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Cosmos DB, zobacz [JSON przykłady](#json-examples) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonego w bazie danych Cosmos DB:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi Azure Cosmos DB, połączone elementy JSON.

| **Property** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type |Właściwość type musi być równa: **Baza danych DocumentDb** |Yes |
| connectionString |Określ informacje potrzebne do łączenia z bazą danych Azure Cosmos DB. |Tak |

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
Pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych można znaleźć [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **DocumentDbCollection** ma następujące właściwości.

| **Property** | **Opis** | **Wymagane** |
| --- | --- | --- |
| collectionName |Nazwa kolekcji dokumentu Cosmos DB. |Tak |

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
### <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory
Dla magazynów danych bez schematu, takiej jak Azure Cosmos DB usługa Data Factory wnioskuje schemat w jednym z następujących sposobów:

1. Jeśli określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługa Data Factory honoruje tej struktury Schema. W tym przypadku jeśli wiersz zawiera wartość dla kolumny, wartość null zostanie podana dla niego.
2. Jeśli nie określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych, których usługa Data Factory wnioskuje schemat przy użyciu pierwszego wiersza danych. W tym przypadku jeśli pierwszy wiersz zawiera pełny schemat, niektóre kolumny będzie brakować w wyniku operacji kopiowania.

W związku z tym, w przypadku źródeł danych bez schematu, najlepszym rozwiązaniem jest i określasz strukturę danych przy użyciu **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań można znaleźć [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden zestaw danych wejściowych i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony zależą od każdy typ działania, a w przypadku działania kopiowania się różnić w zależności od typów źródła i ujścia.

W przypadku działania kopiowania, gdy źródłem jest typu **DocumentDbCollectionSource** następujące właściwości są dostępne w **typeProperties** sekcji:

| **Property** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie, które można odczytać danych. |Zapytanie ciągu obsługiwany przez usługę Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne, aby wskazać, że dokument jest zagnieżdżona |Dowolny znak. <br/><br/>Usługa Azure Cosmos DB jest magazyn danych NoSQL dla dokumentów JSON, gdzie struktury zagnieżdżone są dozwolone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczają hierarchii za pomocą nestingSeparator, czyli "." w powyższych przykładach. Separatorem, działanie kopiowania generuje obiekt "Name", z trzema elementami podrzędnymi elementami najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

**DocumentDbCollectionSink** obsługuje następujące właściwości:

| **Property** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Znaki specjalne w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu jest wymagana. <br/><br/>Na przykład powyżej: `Name.First` w danych wyjściowych tabeli tworzy następującą strukturę JSON w dokumencie usługi Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure Cosmos DB do tworzenia dokumentów.<br/><br/>Podczas kopiowania danych z usługi Cosmos DB przy użyciu tej właściwości można dostosować wydajność. Lepszą wydajność można oczekiwać, gdy zwiększasz writeBatchSize, ponieważ więcej równoległych żądań do usługi Cosmos DB są wysyłane. Jednak należy unikać ograniczania przepustowości, która może zgłosić komunikat o błędzie: "Liczba żądań jest duży."<br/><br/>Ograniczanie zadecyduje o wiele czynników, w tym rozmiar dokumentów, liczbę warunków w dokumentach, indeksowanie zasady kolekcji docelowej, itd. Dla operacji kopiowania umożliwia lepsze kolekcji (np. S3) najbardziej w dostępne przepływności (2500 żądanie jednostek na sekundę). |Integer |Nie (domyślne: 5) |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

## <a name="importexport-json-documents"></a>Dokumenty JSON importu/eksportu
Za pomocą tego łącznika usługi Cosmos DB, możesz z łatwością

* Importuj dokumentów JSON z różnych źródeł do usługi Cosmos DB, w tym obiektów Blob platformy Azure, Azure Data Lake, System plików w środowisku lokalnym lub inne magazyny oparte na plikach, obsługiwane przez usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Cosmos DB do różnych magazynów opartych na plikach.
* Migrowanie danych między dwie kolekcje usługi Cosmos DB jako-to.

Aby osiągnąć takie kopiowania niezależnej od schematu
* Korzystając z Kreatora kopiowania, sprawdź **"Eksportuj jako — pliki w formacie JSON lub kolekcję usługi Cosmos DB"** opcji.
* Gdy przy użyciu formatu JSON edycję, nie należy określać w sekcji "strukturę" w zestawach danych usługi Cosmos DB ani właściwości "nestingSeparator" usługi Cosmos DB źródła/ujścia w działaniu kopiowania. Importuj z / eksportowanie do plików JSON, w pliku zestawu danych magazynu należy określić typ formatu jako "JsonFormat" konfiguracji "filePattern" i pominąć ustawienia formatu rest, zobacz [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format) sekcją Szczegóły na temat.

## <a name="json-examples"></a>Przykłady JSON
W poniższych przykładach udostępniono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak kopiować dane do i z usługi Azure Cosmos DB i Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure Cosmos DB do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [DocumentDb](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [DocumentDbCollectionSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane w usłudze Azure Cosmos DB, do obiektów Blob platformy Azure. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure Cosmos DB połączoną usługę:**

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
**Połączona usługa Azure Blob storage:**

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
**Usługa Azure Documentdb wejściowy zestaw danych:**

Przykład zakłada się, masz kolekcję o nazwie **osoby** w bazie danych Azure Cosmos DB.

Ustawienie "external": "true" i określając externalData informacje o zasadach usługi Azure Data Factory, w tabeli zewnętrznej dla fabryki danych i nie są generowane przez działanie w usłudze data factory.

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

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są kopiowane do nowego obiektu blob co godzinę o ścieżce dla obiektu blob odzwierciedlający określonej daty/godziny z dokładnością do godziny.

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
Przykładowy dokument JSON w kolekcji osoby w bazie danych Cosmos DB:

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
Usługa cosmos DB obsługuje tworzenie zapytań dla dokumentów przy użyciu składni SQL przez hierarchiczną dokumentów JSON.

Przykład:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Następujący potok kopiuje dane z kolekcji osoby w bazie danych Azure Cosmos DB do obiektu blob platformy Azure. Jako część działania kopiowania danych wejściowych i wyjściowych zestawów danych zostały określone.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Przykład: Kopiowanie danych z obiektów Blob platformy Azure do usługi Azure Cosmos DB
Poniższy przykład przedstawia:

1. Połączonej usługi typu bazy danych DocumentDb.
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu DocumentDbCollection.
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i DocumentDbCollectionSink.

Przykład kopiuje dane z usługi Azure blob do usługi Azure Cosmos DB. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure Blob storage:**

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
**Usługa Azure Cosmos DB połączoną usługę:**

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
**Usługa Azure wejściowy zestaw danych obiektów Blob:**

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
**Usługa Azure Cosmos DB wyjściowy zestaw danych:**

Przykład kopiuje dane do kolekcji o nazwie "Person".

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
Następujący potok kopiuje dane z obiektu Blob platformy Azure, do kolekcji osoby w usłudze Cosmos DB. Jako część działania kopiowania danych wejściowych i wyjściowych zestawów danych zostały określone.

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
Jeśli jako przykładowe dane wejściowe obiektu blob

```
1,John,,Doe
```
Następnie dane wyjściowe JSON w usłudze Cosmos DB będzie jako:

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
Usługa Azure Cosmos DB jest magazyn danych NoSQL dla dokumentów JSON, gdzie struktury zagnieżdżone są dozwolone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczają hierarchii za pomocą **nestingSeparator**, czyli "." w tym przykładzie. Separatorem, działanie kopiowania generuje obiekt "Name", z trzema elementami podrzędnymi elementami najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli.

## <a name="appendix"></a>Dodatek
1. **Pytanie:** Powoduje obsługi działania kopiowania aktualizacji istniejących rekordów?

    **Odpowiedź:** Nie.
2. **Pytanie:** Sposób ponawiania kopii w usłudze Azure Cosmos DB postępowania z rekordami już skopiowane?

    **Odpowiedź:** Jeśli operacja kopiowania podejmie próbę wstawienia rekordu o tym samym identyfikatorze rekordy mają pola "ID", operacja kopiowania zgłasza błąd.
3. **Pytanie:** Obsługuje usługi Data Factory [zakresu lub partycjonowanie danych bazujących na skrótach](../../cosmos-db/sql-api-partition-data.md)?

    **Odpowiedź:** Nie.
4. **Pytanie:** Można określić więcej niż jedną kolekcję usługi Azure Cosmos DB dla tabeli?

    **Odpowiedź:** Nie. W tym momencie można określić tylko jedną kolekcję.

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
