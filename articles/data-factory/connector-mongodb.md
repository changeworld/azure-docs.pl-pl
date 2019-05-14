---
title: Kopiowanie danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Mongo DB do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/20/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400444"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!IMPORTANT]
>ADF wersji tej nowej wersji łącznika MongoDB, która zapewnia lepsze macierzystą obsługę bazy danych MongoDB. Jeśli używasz poprzedniej łącznika MongoDB w Twoim rozwiązaniu, które jest obsługiwane jako — jest zgodności z poprzednimi wersjami można znaleźć [łącznika MongoDB (starsza wersja)](connector-mongodb-legacy.md) artykułu.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych MongoDB, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik bazy danych MongoDB obsługuje **wersje maksymalnie 3.4**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z bazy danych MongoDB, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby poznać szczegóły.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi MongoDB.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych MongoDB, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **MongoDbV2** |Yes |
| connectionString |Określ parametry połączenia bazy danych MongoDB, np. `mongodb://[username:password@]host[:port][/[database][?options]]`. Zapoznaj się [Podręcznik bazy danych MongoDB na parametry połączenia](https://docs.mongodb.com/manual/reference/connection-string/) Aby uzyskać więcej informacji. <br/><br />Oznacz to pole jako **SecureString** typ, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| database | Nazwa bazy danych, który chcesz uzyskać dostęp. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
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

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla zestawu danych z bazy danych MongoDB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **MongoDbV2Collection** | Yes |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło bazy danych MongoDB.

### <a name="mongodb-as-source"></a>Bazy danych MongoDB jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **MongoDbV2Source** | Yes |
| filter | Określa filtr wyboru przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, pominięcie tego parametru lub przekazać pusty dokument ({}). | Nie |
| cursorMethods.project | Określa pól do zwrócenia w dokumentach projekcji. Aby zwrócić wszystkie pola w pasujących dokumentów, Pomiń ten parametr. | Nie |
| cursorMethods.sort | Określa kolejność, w którym zapytanie zwraca pasujących dokumentów. Zapoznaj się [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| cursorMethods.limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Zapoznaj się [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie |
| cursorMethods.skip | Określa liczbę dokumentów, aby przejść do i z której rozpoczyna się bazy danych MongoDB do zwracania wyników. Zapoznaj się [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów, aby wrócić w każdej z partii odpowiedzi z wystąpieniem bazy danych MongoDB. W większości przypadków modyfikowanie rozmiar partii nie wpłynie na użytkownika lub aplikacji. Limity usługi cosmos DB, w każdej partii nie może przekraczać 40MB rozmiar, który jest sumą liczby batchSize rozmiar dokumentów, więc Zmniejsz tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100**) |

>[!TIP]
>Obsługa ADF korzystanie z formatu BSON dokumentu w **trybie z ograniczeniami**. Upewnij się, że zapytanie filtru jest w trybie z ograniczeniami zamiast trybu powłoki. Więcej opis znajduje się w temacie [ręczne bazy danych MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>Eksportowanie dokumentów JSON jako — jest

Ten łącznik bazy danych MongoDB umożliwia eksportowanie dokumentów JSON jako-pochodzi z kolekcji bazy danych MongoDB do różnych magazynów opartych na plikach lub do usługi Azure Cosmos DB. Uzyskanie kopii takich niezależny od schematów, Pomiń "strukturę" (nazywane również *schematu*) sekcji zestaw danych i mapowanie schematu w działaniu kopiowania.

## <a name="schema-mapping"></a>mapowanie schematu

Aby skopiować dane z bazy danych MongoDB do ujścia tabelarycznych, zapoznaj się [mapowanie schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
