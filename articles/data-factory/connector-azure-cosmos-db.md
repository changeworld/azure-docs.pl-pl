---
title: Kopiowanie danych z usługi Azure Cosmos DB (interfejs API SQL) lub za pomocą usługi Data Factory | Dokumentacja firmy Microsoft
description: Informacje o kopiowaniu danych z obsługiwanych magazynów danych źródłowych do programu lub z Azure Cosmos DB (SQL API) do obsługiwanych magazynów ujścia przy użyciu Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 561f383327738c9a2ab29f2907f00ace1eec6def
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010277"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Cosmos DB (interfejs API SQL) lub za pomocą usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Cosmos DB (interfejs API SQL). Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik jest obsługiwany tylko kopiowanie danych z interfejsu API SQL usługi Cosmos DB. W przypadku interfejsu API MongoDB zapoznaj się z [łącznikiem dla interfejsu api Azure Cosmos DB MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsów API nie są obecnie obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Cosmos DB (SQL API) jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Azure Cosmos DB (interfejs API SQL) można skopiować do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Azure Cosmos DB (interfejs API SQL). Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Łącznik Azure Cosmos DB (SQL API) można użyć do:

- Kopiowanie danych z i do usługi Azure Cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapis do usługi Azure Cosmos DB jako **Wstaw** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON jako — jest lub kopiowania danych z lub do tabelarycznego zestawu danych. Przykłady obejmują bazy danych SQL i w pliku CSV. Aby skopiować dokumenty jako-is do lub z plików JSON lub z innej kolekcji Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

Data Factory integruje się z [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) aby zapewnić najlepszą wydajność podczas zapisu do usługi Azure Cosmos DB.

> [!TIP]
> [Migracji danych wideo](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z usługi Azure Blob storage do usługi Azure Cosmos DB. Film wideo zawiera także dostrajanie wydajności uwagi, służy do wprowadzania danych do usługi Azure Cosmos DB w zasadzie.

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania Data Factory jednostek, które są specyficzne dla Azure Cosmos DB (interfejs API SQL).

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi Azure Cosmos DB (interfejsu API SQL) są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **CosmosDb**. | Yes |
| connectionString |Określ informacje, które są wymagane do połączenia z bazą danych Azure Cosmos DB.<br />**Uwaga**: W parametrach połączenia należy określić informacje o bazie danych, jak pokazano w poniższym przykładzie. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć `accountKey` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska integration runtime. Jeśli ta właściwość nie jest określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
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

**Przykład: Zapisz klucz konta w Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure Cosmos DB (interfejs API SQL). 

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z lub do Azure Cosmos DB (interfejs API SQL), ustaw właściwość **Type** zestawu danych na **DocumentDbCollection**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwości zestawu danych musi być równa **DocumentDbCollection**. |Yes |
| collectionName |Nazwa kolekcji dokumentów usługi Azure Cosmos DB. |Yes |

**Przykład**

```json
{
    "name": "CosmosDbSQLAPIDataset",
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

Dla magazynów danych bez schematu, np. usługi Azure Cosmos DB działanie kopiowania wnioskuje schema z jednej z metod opisanych w poniższej liście. Jeśli nie chcesz [importowanie lub eksportowanie dokumentów JSON jako — jest](#import-or-export-json-documents), najlepszym rozwiązaniem jest określenie struktury danych w **struktury** sekcji.

* Jeśli określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych usługi Data Factory honoruje tej struktury Schema. 

    Jeśli wiersz nie zawiera wartości dla kolumny, wartości null są udostępniane dla wartości kolumny.
* Jeśli nie określisz struktury danych przy użyciu **struktury** właściwości w definicji zestawu danych, których usługa Data Factory wnioskuje schemat przy użyciu pierwszego wiersza danych. 

    Jeśli pierwszy wiersz nie zawiera pełnej schematu, niektóre kolumny będą w wyniku operacji kopiowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które są obsługiwane przez Azure Cosmos DB (interfejs API SQL) i obiekty sink.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (interfejs API SQL) jako źródło

Aby skopiować dane z Azure Cosmos DB (interfejs API SQL), ustaw typ **źródła** w działaniu Kopiuj na **DocumentDbCollectionSource**. 

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **DocumentDbCollectionSource**. |Yes |
| query |Określ zapytanie usługi Azure Cosmos DB można odczytać danych.<br/><br/>Przykład:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, to instrukcja SQL jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne, która wskazuje, czy dokument jest zagnieżdżona i jak spłaszczanie zestawu wyników.<br/><br/>Na przykład, jeśli zapytanie usługi Azure Cosmos DB zwraca wynik zagnieżdżonych `"Name": {"First": "John"}`, działanie kopiowania określa nazwy kolumny jako `Name.First`, z wartością "John", gdy **nestedSeparator** wartość **.** (kropka). |Nie<br />(wartość domyślna to **.** (kropka)) |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (interfejs API SQL) jako ujścia

Aby skopiować dane do Azure Cosmos DB (interfejs API SQL), ustaw typ **ujścia** w działaniu Copy na **DocumentDbCollectionSink**. 

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość ujścia działania kopiowania musi być równa **DocumentDbCollectionSink**. |Yes |
| writeBehavior |Opisuje sposób zapisywania danych do usługi Azure Cosmos DB. Dozwolone wartości: **Wstaw** i **upsert**.<br/><br/>Zachowanie **upsert** zastąpi dokumentu, jeśli dokument o tym samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokumentu.<br /><br />**Uwaga**: Data Factory automatycznie generuje identyfikator dokumentu, jeśli nie określono identyfikatora w oryginalnym dokumencie lub w mapowaniu kolumn. Oznacza to, że użytkownik musi zapewnić, że dla **upsert** działała zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **Wstaw**) |
| writeBatchSize | Fabryka danych używa [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) można zapisać danych do usługi Azure Cosmos DB. Właściwość **writeBatchSize** steruje rozmiarem dokumentów dostarczanych przez ADF do biblioteki. Można spróbować zwiększyć wartość **writeBatchSize** Aby zwiększyć wydajność i zmniejszenie wartości, jeśli dokument jest duży rozmiar — zobacz poniższe porady. |Nie<br />(wartość domyślna to **10 000**) |
| nestingSeparator |Znaki specjalne w **źródła** nazwy kolumny, która wskazuje, że zagnieżdżone dokumentu jest potrzebna. <br/><br/>Na przykład `Name.First` w wyjściowy zestaw danych struktury generuje następującej strukturze JSON w usłudze Azure Cosmos DB dokumentu, gdy **nestedSeparator** jest **.** (kropka): `"Name": {"First": "[value maps to this column from source]"}`  |Nie<br />(wartość domyślna to **.** (kropka)) |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak Cosmos DB jednostek ru na potrzeby optymalizacji wydajności kopiowania i zaleceń. W przypadku tego zachowania należy określić `true` , aby je wyłączyć. | Nie (domyślnie `false`) |

>[!TIP]
>Usługa cosmos DB ogranicza rozmiar pojedynczego żądania do 2MB. Formuła jest rozmiar żądania = rozmiar pojedynczego dokumentu * zapisu rozmiar partii. Jeśli napotkasz błąd powiedzenie **"Żądania" rozmiar jest za duży.** , **zmniejszyć `writeBatchSize` wartość** w konfiguracji obiektu sink kopiowania.

**Przykład**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="import-or-export-json-documents"></a>Importowanie lub eksportowanie dokumentów JSON

Za pomocą tego łącznika Azure Cosmos DB (interfejsu SQL API) można łatwo:

* Zaimportuj dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z usługi Azure Blob storage, Azure Data Lake Store i inne magazyny oparte na plikach, które obsługuje usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.
* Skopiuj dokumenty między dwie kolekcje usługi Azure Cosmos DB jako-to.

Aby osiągnąć kopiowania niezależny od schematów:

* Korzystając z narzędzia do kopiowania danych wybierz **wyeksportować jako — pliki w formacie JSON lub kolekcję usługi Cosmos DB** opcji.
* Gdy używasz tworzenie działań, nie określaj **struktury** (nazywane również *schematu*) sekcji w zestawie danych usługi Azure Cosmos DB. Ponadto, nie określaj **nestingSeparator** właściwości w usłudze Azure Cosmos DB źródła lub ujścia w działaniu kopiowania. Podczas importowania z lub wyeksportować do pliki w formacie JSON w odpowiedni plik przechowywać zestawu danych, określ **format** wpisać jako **JsonFormat** i skonfigurować **filePattern** jako opisane w [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format) sekcji. Następnie należy określać **struktury** sekcji i Pomiń pozostałą część ustawienia formatowania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
