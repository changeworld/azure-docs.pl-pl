---
title: Kopiowanie danych do lub z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z magazynów danych obsługiwanych źródłowych do lub z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB do ujścia obsługiwanych magazynów za pomocą usługi Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61259730"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiowanie danych do lub z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z i do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik jest obsługiwany tylko skopiować dane do/z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB. Interfejs API SQL można znaleźć [łącznika interfejsu API SQL usługi Cosmos DB](connector-azure-cosmos-db.md). Inne typy interfejsu API nie są teraz obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Interfejs API usługi Azure Cosmos DB można użyć łącznika MongoDB:

- Kopiowanie danych z i do [interfejsu API usługi Azure Cosmos DB, bazy danych mongodb](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapis do usługi Azure Cosmos DB jako **Wstaw** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON jako — jest lub kopiowania danych z lub do tabelarycznego zestawu danych. Przykłady obejmują bazy danych SQL i w pliku CSV. Skopiuj dokumenty w formacie-polega na lub z plików JSON lub do / z innej kolekcji usługi Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla interfejsu API usługi Azure Cosmos DB, bazy danych mongodb.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **CosmosDbMongoDbApi**. | Yes |
| connectionString |Określ parametry połączenia dla interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Można znaleźć w witrynie Azure portal -> bloku usługi Cosmos DB -> Parametry połączenia podstawowej lub dodatkowej, za pomocą wzorca `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| database | Nazwa bazy danych, który chcesz uzyskać dostęp. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska integration runtime. Jeśli ta właściwość nie jest określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB zestawu danych:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **CosmosDbMongoDbApiCollection**. |Yes |
| collectionName |Nazwa kolekcji usługi Azure Cosmos DB. |Yes |

**Przykład**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługują API usługi Azure Cosmos DB dla bazy danych MongoDB źródła i ujścia.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Interfejs API danych usługi Azure Cosmos DB, bazy danych mongodb jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **CosmosDbMongoDbApiSource**. |Yes |
| filter | Określa filtr wyboru przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, pominięcie tego parametru lub przekazać pusty dokument ({}). | Nie |
| cursorMethods.project | Określa pól do zwrócenia w dokumentach projekcji. Aby zwrócić wszystkie pola w pasujących dokumentów, Pomiń ten parametr. | Nie |
| cursorMethods.sort | Określa kolejność, w którym zapytanie zwraca pasujących dokumentów. Zapoznaj się [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| cursorMethods.limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Zapoznaj się [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie | 
| cursorMethods.skip | Określa liczbę dokumentów, aby przejść do i z której rozpoczyna się bazy danych MongoDB do zwracania wyników. Zapoznaj się [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów, aby wrócić w każdej z partii odpowiedzi z wystąpieniem bazy danych MongoDB. W większości przypadków modyfikowanie rozmiar partii nie wpłynie na użytkownika lub aplikacji. Limity usługi cosmos DB, w każdej partii nie może przekraczać 40MB rozmiar, który jest sumą liczby batchSize rozmiar dokumentów, więc Zmniejsz tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100**) |

>[!TIP]
>Obsługa ADF korzystanie z formatu BSON dokumentu w **trybie z ograniczeniami**. Upewnij się, że zapytanie filtru jest w trybie z ograniczeniami zamiast trybu powłoki. Więcej opis znajduje się w temacie [ręczne bazy danych MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Interfejs API danych usługi Azure Cosmos DB, bazy danych mongodb jako ujście

Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość ujścia działania kopiowania musi być równa **CosmosDbMongoDbApiSink**. |Yes |
| writeBehavior |Opisuje sposób zapisywania danych do usługi Azure Cosmos DB. Dozwolone wartości: **Wstaw** i **upsert**.<br/><br/>Zachowanie **upsert** zastąpi dokumentu, jeśli dokument o tym samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokumentu.<br /><br />**Uwaga**: Fabryka danych automatycznie generuje identyfikator dla dokumentu, jeśli identyfikator nie jest określona w oryginalnym dokumencie lub mapowania kolumn. Oznacza to, że użytkownik musi zapewnić, że dla **upsert** działała zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **Wstaw**) |
| writeBatchSize | **WriteBatchSize** właściwość steruje rozmiarem pamięci dokumenty, aby zapisać w każdej partii. Można spróbować zwiększyć wartość **writeBatchSize** Aby zwiększyć wydajność i zmniejszyć wartość, jeśli rozmiar dokumentu jest duży. |Nie<br />(wartość domyślna to **10 000**) |
| writeBatchTimeout | Czas oczekiwania dla partii wstawić na zakończenie przed upływem limitu czasu operacji. Dozwolone wartości to przedziału czasu. | Nie<br/>(wartość domyślna to **00:30:00** — 30 minut) |

**Przykład**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Aby zaimportować dokumentów JSON jako — zapoznaj się [importowanie lub eksportowanie dokumentów JSON](#import-or-export-json-documents) sekcji; Aby skopiować dane tabelaryczne kształcie, zobacz temat [mapowanie schematu](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importowanie lub eksportowanie dokumentów JSON

Można użyć tego łącznika usługi Azure Cosmos DB, aby łatwo:

* Zaimportuj dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z usługi Azure Blob storage, Azure Data Lake Store i inne magazyny oparte na plikach, które obsługuje usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.
* Skopiuj dokumenty między dwie kolekcje usługi Azure Cosmos DB jako-to.

Uzyskanie kopii takich niezależny od schematów, Pomiń "strukturę" (nazywane również *schematu*) sekcji zestaw danych i mapowanie schematu w działaniu kopiowania.

## <a name="schema-mapping"></a>mapowanie schematu

Kopiowanie danych z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB do ujścia tabelarycznych lub wycofane, można znaleźć [mapowanie schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

Specjalnie do zapisu do usługi Cosmos DB, aby upewnić się, że Wypełnij Cosmos DB przy użyciu Identyfikatora obiektu w prawo przy użyciu danych źródłowych na przykład masz kolumnę "id" w tabeli bazy danych SQL i ma być używana wartość, która jako identyfikator dokumentu w bazie danych MongoDB dla insert/upsert , musisz ustawić mapowania prawidłowego schematu, zgodnie z definicją trybie z ograniczeniami bazy danych MongoDB (`_id.$oid`) zgodnie z poniższymi:

![Mapowanie Identyfikatora w ujściu usługi MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po kopiowania wykonania działania, poniżej BSON ObjectId jest generowany w ujściu:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
