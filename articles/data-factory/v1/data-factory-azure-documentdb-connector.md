---
title: Przenoszenie danych do/z bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane do/z bazy danych Azure rozwiązania Cosmos kolekcji przy użyciu fabryki danych Azure
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3ebd704129aabecffdaa2589b8b086803a2d092
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046607"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Przenoszenie danych do i z bazy danych rozwiązania Cosmos Azure przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](data-factory-azure-documentdb-connector.md)
> * [W wersji 2 (bieżąca wersja)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [łącznika Azure DB rozwiązania Cosmos w wersji 2](../connector-azure-cosmos-db.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przeniesienia danych z bazy danych Azure rozwiązania Cosmos (interfejsu API SQL). Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania. 

Dane należy skopiować z dowolnego źródła obsługiwanych magazynu danych do bazy danych Azure rozwiązania Cosmos lub z bazy danych usługi Azure rozwiązania Cosmos żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. 

> [!IMPORTANT]
> Azure DB rozwiązania Cosmos łącznik obsługuje tylko interfejsu API SQL.

Aby skopiować dane jako — jest do/z pliki w formacie JSON lub innej kolekcji rozwiązania Cosmos bazy danych, zobacz [dokumentów JSON importu/eksportu](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potoku o działanie kopiowania, który przenosi dane z bazy danych rozwiązania Cosmos Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładów z definicji JSON dla jednostek fabryki danych, które służą do kopiowania danych do/z rozwiązania Cosmos bazy danych, zobacz [przykłady JSON](#json-examples) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do rozwiązania Cosmos bazy danych: 

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi Azure DB rozwiązania Cosmos połączone elementy JSON.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **usługi DocumentDb** |Yes |
| Parametry połączenia |Określ informacje potrzebne do łączenia z bazą danych Azure DB rozwiązania Cosmos. |Yes |

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
Pełną listę właściwości dostępne do definiowania zestawów danych & sekcje zapoznaj się [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Jak struktura, dostępności i zasad zestawu danych JSON jest podobna dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

Sekcja typeProperties jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **DocumentDbCollection** ma następujące właściwości.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| collectionName |Nazwa kolekcji dokumentów DB rozwiązania Cosmos. |Yes |

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
### <a name="schema-by-data-factory"></a>Schemat fabryka danych
W przypadku danych bez schematu sklepów takich jak bazy danych Azure rozwiązania Cosmos usługi fabryka danych z wnioskuje schemat w jednym z następujących sposobów:  

1. Jeśli określisz struktury danych za pomocą **struktury** tej struktury Schema honoruje właściwości w definicji zestawu danych, usługi fabryka danych. W takim przypadku wiersza nie zawiera wartości dla kolumny, będzie należy podać dla niego wartość null.
2. Jeśli nie określisz struktury danych za pomocą **struktury** właściwości w definicji zestawu danych, usługi fabryka danych z wnioskuje schemat za pomocą pierwszego wiersza w danych. W takim przypadku jeśli pierwszy wiersz zawiera pełną schematu, niektóre kolumny będą niedostępne w wyniku operacji kopiowania.

W związku z tym dla źródeł danych bez schematu, najlepszym rozwiązaniem jest zdefiniowanie struktury danych przy użyciu **struktury** właściwości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę właściwości dostępne do definiowania działań & sekcje zapoznaj się [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jeden parametr wejściowy i tworzy tylko jedno wyjście.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony zależą od każdy typ działania, a w przypadku działania kopiowania się różnić w zależności od typów źródeł i sink.

W przypadku działania kopiowania, gdy źródłem jest typu **DocumentDbCollectionSource** następujące właściwości są dostępne w **typeProperties** sekcji:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie można odczytać danych. |Wyślij zapytanie do ciągu obsługuje bazy danych Azure rozwiązania Cosmos. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne w celu wskazania, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Azure DB rozwiązania Cosmos jest magazynem NoSQL dla dokumentów JSON, których struktury zagnieżdżone są dozwolone. Fabryka danych Azure umożliwia użytkownikowi oznaczenia hierarchii za pomocą nestingSeparator, czyli "." w powyższych przykładach. Z separatorem, działanie kopiowania spowoduje wygenerowanie obiektu "Name" o trzy elementy podrzędne elementy najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

**DocumentDbCollectionSink** obsługuje następujące właściwości:

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Wymagany jest znak specjalny w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu. <br/><br/>Na przykład powyżej: `Name.First` w danych wyjściowych tabeli tworzy następującą strukturę JSON w dokumencie rozwiązania Cosmos bazy danych:<br/><br/>"Nazwa": {<br/>    "Pierwszy": "Jan"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropką). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropką). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure DB rozwiązania Cosmos w celu utworzenia dokumentów.<br/><br/>Aby precyzyjnie zdefiniować wydajność podczas kopiowania danych z bazy danych usługi rozwiązania Cosmos przy użyciu tej właściwości. Wraz ze zwiększeniem writeBatchSize, ponieważ więcej żądań równoległych do rozwiązania Cosmos bazy danych są wysyłane, może spodziewać się lepszą wydajność. Jednak należy unikać ograniczania przepustowości, który może zgłaszać komunikat o błędzie: "jest duża szybkość żądania".<br/><br/>Ograniczanie zadecyduje o wiele czynników, w tym rozmiar dokumentów, liczbę dokumentów, indeksowania zasady kolekcji docelowej, itd. Dla operacji kopiowania umożliwiają lepsze kolekcji (np. S3) ma największą przepływność dostępne (2500 żądań jednostek na sekundę). |Liczba całkowita |Nie (domyślne: 5) |
| writeBatchTimeout |Poczekaj na ukończenie upłynie limit czasu operacji. |zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

## <a name="importexport-json-documents"></a>Dokumentów JSON Import/Eksport
Korzystając z tego łącznika DB rozwiązania Cosmos, można łatwo

* Zaimportuj dokumentów JSON z różnych źródeł do rozwiązania Cosmos bazy danych, w tym obiektów Blob platformy Azure, usługa Azure Data Lake, lokalnego systemu plików lub innych magazynów opartych na plikach obsługiwane przez usługi fabryka danych Azure.
* Wyeksportuj dokumentów JSON z collecton rozwiązania Cosmos bazy danych do różnych magazynów opartych na plikach.
* Migrowanie danych między dwoma kolekcje DB rozwiązania Cosmos w postaci — jest.

Do osiągnięcia tych kopii niezależny od schematu 
* Korzystając z Kreatora kopiowania, sprawdź **"wyeksportować w postaci-ma pliki w formacie JSON lub rozwiązania Cosmos bazy danych kolekcji"** opcji.
* Gdy edycję JSON, nie należy określać w sekcji "structure" w opublikowanych DB rozwiązania Cosmos ani właściwości "nestingSeparator" dla bazy danych rozwiązania Cosmos źródło/ujście w przypadku działania kopiowania. Importuj z / Eksportuj do pliki w formacie JSON, w zestawie plików magazynu danych należy określić typ formatu jako "JsonFormat", "filePattern" config i Pomiń pozostałe ustawienia formatu, zobacz [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format) sekcji Szczegóły.

## <a name="json-examples"></a>Przykłady JSON
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z bazy danych Azure rozwiązania Cosmos i magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Przykład: Kopiowanie danych z bazy danych Azure rozwiązania Cosmos do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [DocumentDb](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [DocumentDbCollectionSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane w usłudze Azure DB rozwiązania Cosmos do obiektów Blob platformy Azure. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Azure DB rozwiązania Cosmos połączonej usługi:**

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
**Magazyn obiektów Blob Azure połączonej usługi:**

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
**Azure DB dokument wejściowy zestaw danych:**

Przykładzie przyjęto założenie, masz kolekcję o nazwie **osoby** w bazie danych bazy danych Azure rozwiązania Cosmos.

Ustawienie "external": "prawda", a następnie określając externalData informacje o zasadach usługi fabryka danych Azure czy tabeli zewnętrznej dla fabryki danych i nie są produkowane przez działanie w fabryce danych.

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

**Azure Blob wyjściowy zestaw danych:**

Dane zostaną skopiowane do nowego obiektu blob co godzinę o ścieżce dla obiektu blob odzwierciedlające określonej daty/godziny z szczegółowości godzinę.

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
Przykładowy dokument JSON w kolekcji osoby w bazie danych DB rozwiązania Cosmos:

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
Rozwiązania cosmos bazy danych obsługuje tworzenie zapytań dla dokumentów przy użyciu składni SQL przez hierarchiczna dokumentów JSON.

Przykład: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Następujące potoku kopiuje dane z kolekcji osoby w bazie danych bazy danych Azure rozwiązania Cosmos obiektów blob platformy Azure. W ramach działania kopiowania danych wejściowych i wyjściowych zestawów danych zostały określone.  

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Przykład: Kopiowanie danych z obiektu Blob Azure do bazy danych Azure rozwiązania Cosmos 
Poniższy przykład przedstawia:

1. Połączonej usługi typu [DocumentDb](#azure-documentdb-linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

Przykład kopiuje dane z usługi Azure blob do bazy danych Azure rozwiązania Cosmos. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**Magazyn obiektów Blob Azure połączonej usługi:**

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
**Azure DB rozwiązania Cosmos połączonej usługi:**

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
**Azure wejściowego zestawu danych obiektów Blob:**

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
**Azure DB rozwiązania Cosmos wyjściowy zestaw danych:**

Przykład kopiuje dane na kolekcję o nazwie "Osoba".

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
Następujące potoku kopiuje dane z obiektów Blob platformy Azure do kolekcji osoby w bazie danych rozwiązania Cosmos. W ramach działania kopiowania danych wejściowych i wyjściowych zestawów danych zostały określone.

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
          }
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
Jeśli przykładowe dane wejściowe obiektu blob jako

```
1,John,,Doe
```
Następnie dane wyjściowe JSON do rozwiązania Cosmos bazy danych będą się jako:

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
Azure DB rozwiązania Cosmos jest magazynem NoSQL dla dokumentów JSON, których struktury zagnieżdżone są dozwolone. Fabryka danych Azure umożliwia użytkownikowi oznaczenia hierarchii za pomocą **nestingSeparator**, czyli "." w tym przykładzie. Z separatorem, działanie kopiowania spowoduje wygenerowanie obiektu "Name" o trzy elementy podrzędne elementy najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli.

## <a name="appendix"></a>Dodatek
1. **Pytanie:** jest aktualizacja obsługi działanie kopiowania istniejące rekordy?

    **Odpowiedź:** nie.
2. **Pytanie:** jak ponowienie kopię bazy danych Azure rozwiązania Cosmos przeciwdziałania już skopiowane rekordy?

    **Odpowiedź:** rekordów zawiera pola "ID", próbuje operacji kopiowania do wstawienia rekordu o tym samym identyfikatorze operacji kopiowania zgłasza błąd.  
3. **Pytanie:** fabryki danych obsługuje [zakres lub partycjonowanie danych opartych na skrót](../../cosmos-db/sql-api-partition-data.md)?

    **Odpowiedź:** nie.
4. **Pytanie:** można określić więcej niż jednej bazy danych Azure rozwiązania Cosmos kolekcji dla tabeli?

    **Odpowiedź:** nie. W tym momencie można określić tylko jedną kolekcję.

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
