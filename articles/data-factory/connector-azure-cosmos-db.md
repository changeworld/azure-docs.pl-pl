---
title: Kopiowanie danych z usługi Azure Cosmos DB (interfejs API SQL) lub za pomocą usługi Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z magazynów danych obsługiwanych źródłowych, do i z usługi Azure Cosmos DB (interfejs API SQL) do ujścia obsługiwanych magazynów za pomocą usługi Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259521"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Cosmos DB (interfejs API SQL) lub za pomocą usługi Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Cosmos DB (interfejs API SQL). Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik jest obsługiwany tylko kopiowanie danych z interfejsu API SQL usługi Cosmos DB. W przypadku interfejsu API usługi MongoDB można znaleźć [łącznik dla interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsu API nie są teraz obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z usługi Azure Cosmos DB (interfejs API SQL) do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Azure Cosmos DB (interfejs API SQL). Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Można użyć łącznika usługi Azure Cosmos DB (interfejs API SQL) w celu:

- Kopiowanie danych z i do usługi Azure Cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapis do usługi Azure Cosmos DB jako **Wstaw** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON jako — jest lub kopiowania danych z lub do tabelarycznego zestawu danych. Przykłady obejmują bazy danych SQL i w pliku CSV. Skopiuj dokumenty w formacie-polega na lub z plików JSON lub do / z innej kolekcji usługi Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

Data Factory integruje się z [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) aby zapewnić najlepszą wydajność podczas zapisu do usługi Azure Cosmos DB.

> [!TIP]
> [Migracji danych wideo](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z usługi Azure Blob storage do usługi Azure Cosmos DB. Film wideo zawiera także dostrajanie wydajności uwagi, służy do wprowadzania danych do usługi Azure Cosmos DB w zasadzie.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla usługi Azure Cosmos DB (interfejs API SQL).

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługę Azure Cosmos DB (interfejs API SQL) połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **CosmosDb**. | Yes |
| Parametry połączenia |Określ informacje, które są wymagane do połączenia z bazą danych Azure Cosmos DB.<br />**Uwaga**: W parametrach połączenia, jak pokazano w przykładach, które należy wykonać, należy określić informacje o bazie danych. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić klucz konta w usłudze Azure Key Vault i ściągania `accountKey` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Yes |
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

**Przykład: przechowywanie klucza konta w usłudze Azure Key Vault**

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

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych usługi Azure Cosmos DB (interfejs API SQL). 

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z lub do usługi Azure Cosmos DB (interfejs API SQL), należy ustawić **typu** właściwości zestawu danych na **DocumentDbCollection**. Obsługiwane są następujące właściwości:

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

Ta sekcja zawiera listę właściwości, które obsługują usługę Azure Cosmos DB (interfejs API SQL) źródła i ujścia.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Usługa Azure Cosmos DB (interfejs API SQL) jako źródło

Aby skopiować dane z usługi Azure Cosmos DB (interfejs API SQL), należy ustawić **źródła** typ w działaniu kopiowania, aby **DocumentDbCollectionSource**. 

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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Usługa Azure Cosmos DB (interfejs API SQL) jako ujście

Aby skopiować dane do usługi Azure Cosmos DB (interfejs API SQL), należy ustawić **ujścia** typ w działaniu kopiowania, aby **DocumentDbCollectionSink**. 

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość ujścia działania kopiowania musi być równa **DocumentDbCollectionSink**. |Yes |
| writeBehavior |Opisuje sposób zapisywania danych do usługi Azure Cosmos DB. Dozwolone wartości: **Wstaw** i **upsert**.<br/><br/>Zachowanie **upsert** zastąpi dokumentu, jeśli dokument o tym samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokumentu.<br /><br />**Uwaga**: Fabryka danych automatycznie generuje identyfikator dla dokumentu, jeśli identyfikator nie jest określona w oryginalnym dokumencie lub mapowania kolumn. Oznacza to, że użytkownik musi zapewnić, że dla **upsert** działała zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **Wstaw**) |
| writeBatchSize | Fabryka danych używa [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) można zapisać danych do usługi Azure Cosmos DB. **WriteBatchSize** właściwość steruje rozmiarem pamięci dokumentów zawierających ADF do biblioteki. Można spróbować zwiększyć wartość **writeBatchSize** Aby zwiększyć wydajność i zmniejszenie wartości, jeśli dokument jest duży rozmiar — zobacz poniższe porady. |Nie<br />(wartość domyślna to **10 000**) |
| nestingSeparator |Znaki specjalne w **źródła** nazwy kolumny, która wskazuje, że zagnieżdżone dokumentu jest potrzebna. <br/><br/>Na przykład `Name.First` w wyjściowy zestaw danych struktury generuje następującej strukturze JSON w usłudze Azure Cosmos DB dokumentu, gdy **nestedSeparator** jest **.** (kropka): `"Name": {"First": "[value maps to this column from source]"}`  |Nie<br />(wartość domyślna to **.** (kropka)) |

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

## <a name="import-or-export-json-documents"></a>Importowanie lub eksportowanie dokumentów JSON

Można użyć tego łącznika usługi Azure Cosmos DB (interfejs API SQL), aby łatwo:

* Zaimportuj dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z usługi Azure Blob storage, Azure Data Lake Store i inne magazyny oparte na plikach, które obsługuje usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.
* Skopiuj dokumenty między dwie kolekcje usługi Azure Cosmos DB jako-to.

Aby osiągnąć kopiowania niezależny od schematów:

* Korzystając z narzędzia do kopiowania danych wybierz **wyeksportować jako — pliki w formacie JSON lub kolekcję usługi Cosmos DB** opcji.
* Gdy używasz tworzenie działań, nie określaj **struktury** (nazywane również *schematu*) sekcji w zestawie danych usługi Azure Cosmos DB. Ponadto, nie określaj **nestingSeparator** właściwości w usłudze Azure Cosmos DB źródła lub ujścia w działaniu kopiowania. Podczas importowania z lub wyeksportować do pliki w formacie JSON w odpowiedni plik przechowywać zestawu danych, określ **format** wpisać jako **JsonFormat** i skonfigurować **filePattern** jako opisane w [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format) sekcji. Następnie należy określać **struktury** sekcji i Pomiń pozostałą część ustawienia formatowania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
