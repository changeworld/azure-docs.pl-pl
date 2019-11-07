---
title: Kopiowanie danych do lub z Azure Cosmos DB (SQL API) za pomocą Data Factory
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
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681301"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie danych do lub z Azure Cosmos DB (SQL API) za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do Azure Cosmos DB (interfejs API SQL). Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik obsługuje tylko kopiowanie danych do/z Cosmos DB interfejsu API SQL. W przypadku interfejsu API MongoDB zapoznaj się z [łącznikiem dla interfejsu api Azure Cosmos DB MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsów API nie są obecnie obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Azure Cosmos DB (SQL API) jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Azure Cosmos DB (interfejs API SQL) można skopiować do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Azure Cosmos DB (interfejs API SQL). Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

Łącznik Azure Cosmos DB (SQL API) można użyć do:

- Skopiuj dane z i do Azure Cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapisz do Azure Cosmos DB jako **INSERT** lub **upsert**.
- Importuj i Eksportuj dokumenty JSON jako-is lub Kopiuj dane z lub do tabelarycznego zestawu danych. Przykładami mogą być baza danych SQL i plik CSV. Aby skopiować dokumenty jako-is do lub z plików JSON lub z innej kolekcji Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

Data Factory integruje się z [Azure Cosmos DB zbiorczą biblioteką wykonawców](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) , aby zapewnić najlepszą wydajność podczas zapisywania do Azure Cosmos DB.

> [!TIP]
> Film dotyczący [migracji danych](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z usługi Azure Blob storage do Azure Cosmos DB. Film wideo opisuje również zagadnienia dotyczące dostrajania wydajności dotyczące pozyskiwania danych do Azure Cosmos DB.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania Data Factory jednostek, które są specyficzne dla Azure Cosmos DB (interfejs API SQL).

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Azure Cosmos DB (interfejsu API SQL) są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **CosmosDb**. | Tak |
| Przekształcon |Określ informacje wymagane do nawiązania połączenia z bazą danych Azure Cosmos DB.<br />**Uwaga**: należy określić informacje o bazie danych w parametrach połączenia, jak pokazano w poniższym przykładzie. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć konfigurację `accountKey` z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z lub do Azure Cosmos DB (interfejs API SQL), ustaw właściwość **Type** zestawu danych na **DocumentDbCollection**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **DocumentDbCollection**. |Tak |
| CollectionName |Nazwa Azure Cosmos DB kolekcji dokumentów. |Tak |

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

### <a name="schema-by-data-factory"></a>Schemat według Data Factory

W przypadku magazynów danych bez schematu, takich jak Azure Cosmos DB, działanie Copy wnioskuje schemat na jeden z metod opisanych na poniższej liście. W przypadku [zaimportowania lub wyeksportowania dokumentów JSON nie jest](#import-or-export-json-documents)to zalecane, najlepszym rozwiązaniem jest określenie struktury danych w sekcji **Struktura** .

* Jeśli określisz strukturę danych przy użyciu właściwości **Structure** w definicji zestawu danych, Data Factory uznaje tę strukturę jako schemat. 

    Jeśli wiersz nie zawiera wartości dla kolumny, dla wartości kolumny jest podawana wartość null.
* Jeśli nie określisz struktury danych przy użyciu właściwości **Structure** w definicji zestawu danych, Usługa Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. 

    Jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji kopiowania brakuje niektórych kolumn.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które są obsługiwane przez Azure Cosmos DB (interfejs API SQL) i obiekty sink.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (interfejs API SQL) jako źródło

Aby skopiować dane z Azure Cosmos DB (interfejs API SQL), ustaw typ **źródła** w działaniu Kopiuj na **DocumentDbCollectionSource**. 

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **DocumentDbCollectionSource**. |Tak |
| query |Określ zapytanie Azure Cosmos DB, aby odczytywać dane.<br/><br/>Przykład:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, ta instrukcja SQL jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znak specjalny wskazujący, że dokument jest zagnieżdżony i jak spłaszczyć zestaw wyników.<br/><br/>Na przykład, jeśli zapytanie Azure Cosmos DB zwraca zagnieżdżony wynik `"Name": {"First": "John"}`, działanie Copy identyfikuje nazwę kolumny jako `Name.First`z wartością "Jan", gdy wartość **nestedSeparator** jest równa **.** (kropka). |Nie<br />(wartość domyślna to **.** (kropka)) |

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

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **DocumentDbCollectionSink**. |Tak |
| WriteBehavior |Opisuje sposób zapisywania danych w Azure Cosmos DB. Dozwolone wartości: **INSERT** i **upsert**.<br/><br/>Zachowanie **upsert** polega na zastępowaniu dokumentu, jeśli dokument o takim samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokument.<br /><br />**Uwaga**: Data Factory automatycznie generuje identyfikator dla dokumentu, jeśli nie określono identyfikatora w oryginalnym dokumencie lub w mapowaniu kolumn. Oznacza to, że aby program **upsert** działał zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **INSERT**) |
| writeBatchSize | Data Factory używa [Azure Cosmos DB zbiorczej biblioteki](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) do zapisywania danych w Azure Cosmos DB. Właściwość **writeBatchSize** steruje rozmiarem dokumentów dostarczanych przez ADF do biblioteki. Możesz spróbować zwiększyć wartość **writeBatchSize** , aby zwiększyć wydajność i zmniejszyć wartość w przypadku dużego rozmiaru dokumentu — Zobacz poniżej porady. |Nie<br />(wartość domyślna to **10 000**) |
| nestingSeparator |Znak specjalny w nazwie kolumny **źródłowej** , który wskazuje, że jest wymagany dokument zagnieżdżony. <br/><br/>Na przykład `Name.First` w strukturze wyjściowego zestawu danych generuje następującą strukturę JSON w dokumencie Azure Cosmos DB, gdy **nestedSeparator** **.** (kropka): `"Name": {"First": "[value maps to this column from source]"}`  |Nie<br />(wartość domyślna to **.** (kropka)) |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak Cosmos DB jednostek ru na potrzeby optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, określ `true`, aby je wyłączyć. | Nie (wartość domyślna to `false`) |

>[!TIP]
>Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła ma rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli wystąpi błąd mówiący, **"rozmiar żądania jest zbyt duży"** , **Zmniejsz wartość `writeBatchSize`** w konfiguracji ujścia kopiowania.

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

* Zaimportuj dokumenty JSON z różnych źródeł do Azure Cosmos DB, w tym z usługi Azure Blob Storage, Azure Data Lake Store i innych magazynów opartych na plikach, które Azure Data Factory obsługiwane przez program.
* Eksportuj dokumenty JSON z kolekcji Azure Cosmos DB do różnych magazynów opartych na plikach.
* Kopiuj dokumenty między dwoma kolekcjami Azure Cosmos DB.

Aby uzyskać kopię schematu niezależny od:

* Korzystając z narzędzia Kopiowanie danych, wybierz opcję **Eksportuj jako plik JSON lub Cosmos DB kolekcji** .
* W przypadku korzystania z funkcji tworzenia działań nie należy określać **struktury** (nazywanej również *schematem*) w zestawie danych Azure Cosmos DB. Ponadto nie należy określać właściwości **nestingSeparator** w źródle Azure Cosmos DB lub ujścia w działaniu kopiowania. Podczas importowania z plików JSON lub eksportu do nich, w odpowiednim zestawie danych magazynu plików Określ typ **formatu** jako **formatu jsonformat** i skonfiguruj **filePattern** zgodnie z opisem w sekcji [Format JSON](supported-file-formats-and-compression-codecs.md#json-format) . Następnie nie określaj sekcji **struktury** i pomiń pozostałe ustawienia formatu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
