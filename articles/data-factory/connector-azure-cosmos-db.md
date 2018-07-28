---
title: Kopiowanie danych do i z usługi Azure Cosmos DB przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów danych do usługi Azure Cosmos DB (lub) z usługi Cosmos DB do ujścia obsługiwanych magazynów przy użyciu usługi fabryka danych.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 6c0921a466864bf2b07711cfcd1eac397c5ced83
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325357"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Cosmos DB przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Cosmos DB (interfejs API SQL). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z usługi Azure Cosmos DB do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Azure Cosmos DB. Aby uzyskać listę magazynów danych obsługiwanych jako źródła/ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Azure Cosmos DB obsługuje:

- Usługa cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Importowanie/eksportowanie dokumentów JSON jako — jest lub kopiowania danych z/do tabelaryczny zestaw danych np. bazy danych SQL, pliki CSV itp.

Skopiuj dokumenty w formacie — jest do i z plików JSON lub innej kolekcji usługi Cosmos DB, zobacz [dokumentów JSON Import/Export](#importexport-json-documents).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure Cosmos DB.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi połączonej usługi Azure Cosmos DB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **CosmosDb**. | Yes |
| Parametry połączenia |Określ informacje potrzebne do łączenia z bazą danych Azure Cosmos DB. Należy pamiętać, że należy określić informacje o bazie danych w parametrach połączenia, zgodnie z poniższych przykładowych. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure Cosmos DB.

Aby skopiować dane z/do usługi Azure Cosmos DB, należy ustawić właściwość typu zestawu danych na **DocumentDbCollection**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **DocumentDbCollection** |Yes |
| CollectionName |Nazwa kolekcji dokumentu Cosmos DB. |Yes |

**Przykład:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory

Dla magazynów danych bez schematu, takiej jak Azure Cosmos DB działanie kopiowania wnioskuje schemat w jednym z następujących sposobów. W związku z tym chyba że chcesz [importu/eksportu dokumentów JSON jako — jest](#importexport-json-documents), najlepszym rozwiązaniem jest określenie struktury danych w **struktury** sekcji.

*. Jeśli określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługa Data Factory honoruje tej struktury Schema. W tym przypadku jeśli wiersz zawiera wartość dla kolumny, wartość null zostanie podana dla niego.
*. Jeśli nie określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych, których usługa Data Factory wnioskuje schemat przy użyciu pierwszego wiersza danych. W tym przypadku jeśli pierwszy wiersz zawiera pełny schemat, niektóre kolumny będzie brakować w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługę Azure Cosmos DB źródła i ujścia.

### <a name="azure-cosmos-db-as-source"></a>Usługa Azure Cosmos DB jako źródła

Aby skopiować dane z usługi Azure Cosmos DB, należy ustawić typ źródłowego w działaniu kopiowania, aby **DocumentDbCollectionSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **DocumentDbCollectionSource** |Yes |
| query |Określ zapytanie usługi Cosmos DB można odczytać danych.<br/><br/>Przykład: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne, aby wskazać, że dokument jest zagnieżdżona i jak do flattern zestaw wyników.<br/><br/>Na przykład, jeśli zapytanie usługi Cosmos DB, funkcja zwraca wynik zagnieżdżonych `"Name": {"First": "John"}`, działanie kopiowania będzie rozpoznanie nazwy kolumny jako "Name.First" z wartością "John" nestedSeparator jest kropką. |Nie (wartość domyślna to kropka `.`) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Usługa Azure Cosmos DB jako ujście

Aby skopiować dane do usługi Azure Cosmos DB, należy ustawić typ ujścia w działaniu kopiowania, aby **DocumentDbCollectionSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type ujścia działania kopiowania: **DocumentDbCollectionSink** |Yes |
| writeBehavior |Opisano, jak można zapisać danych do usługi Cosmos DB. Dozwolone wartości to: `insert` i `upsert`.<br/>Zachowanie **upsert** zastąpi dokumentu, jeśli dokument o takim samym identyfikatorze już istnieje; w przeciwnym razie wstaw go. Uwaga ADF automatycznie generuje identyfikator dla dokumentu, jeśli nie jest określony w oryginalnym dokumencie lub przez mapowanie kolumn), co oznacza, że należy upewnić się, że dokument ma "id", aby dało się upsert działać zgodnie z oczekiwaniami. |Nie, Wstaw jest domyślny |
| writeBatchSize | Użycie fabryki danych [przetwarzania zbiorczego usługi Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) można zapisać danych do usługi Cosmos DB. "writeBatchSize" Steruje rozmiarem pamięci dokumentów, które firma Microsoft zapewnia do biblioteki każdorazowo. Można spróbować zwiększyć writeBatchSize w celu zwiększenia wydajności. |Nie |
| nestingSeparator |Znaki specjalne w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu jest wymagana. <br/><br/>Na przykład `Name.First` w wyjściowy zestaw danych struktury generuje następującej strukturze JSON w dokumencie usługi Cosmos DB:`"Name": {"First": "[value maps to this column from source]"}` gdy nestedSeparator jest kropką. |Nie (wartość domyślna to kropka `.`) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Dokumenty JSON importu/eksportu

Za pomocą tego łącznika usługi Cosmos DB, możesz z łatwością

* Importuj dokumentów JSON z różnych źródeł do usługi Cosmos DB: obiektów Blob platformy Azure, Azure Data Lake Store i inne magazyny oparte na plikach, obsługiwane przez usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z collecton usługi Cosmos DB do różnych magazynów opartych na plikach.
* Skopiuj dokumenty między dwie kolekcje usługi Cosmos DB jako-to.

Aby osiągnąć takie kopiowania niezależny od schematów:

* Korzystając z narzędzia do kopiowania danych, sprawdź **"Eksportuj jako — pliki w formacie JSON lub kolekcję usługi Cosmos DB"** opcji.
* Gdy przy użyciu tworzenie działań, nie należy określać w sekcji "strukturę" (czyli schemat) w zestawach danych usługi Cosmos DB ani właściwości "nestingSeparator" usługi Cosmos DB źródła/ujścia w działaniu kopiowania. Gdy importowanie / eksportowanie do plików JSON w odpowiedni plik magazynu zestawu danych, określ typ formatu jako "JsonFormat" i konfiguracja "filePattern" poprawnie (zobacz [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format) sekcji, aby uzyskać szczegółowe informacje), następnie należy określać struktury" "(czyli schemat) sekcji, a następnie przejdź ustawienia formatu rest.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
