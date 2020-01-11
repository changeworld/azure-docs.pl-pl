---
title: Kopiowanie i Przekształcanie danych w Azure Cosmos DB (interfejs API SQL)
description: Dowiedz się, jak kopiować dane do i z Azure Cosmos DB (SQL API) oraz przekształcać dane w Azure Cosmos DB (SQL API) przy użyciu Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: 52bec8bba7bb3ddf545e3bd1866775f0964c6ad3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893137"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w Azure Cosmos DB (interfejs API SQL) za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-documentdb-connector.md)
> * [Bieżąca wersja](connector-azure-cosmos-db.md)

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z i do Azure Cosmos DB (interfejs API SQL) oraz do przekształcania danych w Azure Cosmos DB (SQL API). Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

>[!NOTE]
>Ten łącznik obsługuje tylko Cosmos DB interfejsu API SQL. W przypadku interfejsu API MongoDB zapoznaj się z [łącznikiem dla interfejsu api Azure Cosmos DB MongoDB](connector-azure-cosmos-db-mongodb-api.md). Inne typy interfejsów API nie są obecnie obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Cosmos DB (SQL API) jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

W przypadku działania kopiowania ten łącznik Azure Cosmos DB (interfejs SQL API) obsługuje:

- Kopiowanie danych z i do usługi Azure Cosmos DB [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Zapis do usługi Azure Cosmos DB jako **Wstaw** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON jako — jest lub kopiowania danych z lub do tabelarycznego zestawu danych. Przykłady obejmują bazy danych SQL i w pliku CSV. Aby skopiować dokumenty jako-is do lub z plików JSON lub z innej kolekcji Azure Cosmos DB, zobacz [Importowanie i eksportowanie dokumentów JSON](#import-and-export-json-documents).

Data Factory integruje się z [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) aby zapewnić najlepszą wydajność podczas zapisu do usługi Azure Cosmos DB.

> [!TIP]
> [Migracji danych wideo](https://youtu.be/5-SRNiC_qOU) przeprowadzi Cię przez kroki kopiowania danych z usługi Azure Blob storage do usługi Azure Cosmos DB. Film wideo zawiera także dostrajanie wydajności uwagi, służy do wprowadzania danych do usługi Azure Cosmos DB w zasadzie.

## <a name="get-started"></a>Rozpocznij

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania Data Factory jednostek, które są specyficzne dla Azure Cosmos DB (interfejs API SQL).

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi Azure Cosmos DB (interfejsu API SQL) są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **CosmosDb**. | Tak |
| connectionString |Określ informacje, które są wymagane do połączenia z bazą danych Azure Cosmos DB.<br />**Uwaga**: należy określić informacje o bazie danych w parametrach połączenia, jak pokazano w przykładach. <br/> Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć konfigurację `accountKey` z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub własnego środowiska integration runtime. Jeśli ta właściwość nie jest określona, używana jest domyślna Azure Integration Runtime. |Nie |

**Przykład**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
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

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md).

Następujące właściwości są obsługiwane dla zestawu danych Azure Cosmos DB (SQL API): 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **CosmosDbSqlApiCollection**. |Tak |
| collectionName |Nazwa kolekcji dokumentów usługi Azure Cosmos DB. |Tak |

Jeśli używasz zestawu danych typu "DocumentDbCollection", nadal jest on obsługiwany w przypadku zgodności z poprzednimi wersjami dla działania kopiowania i wyszukiwania, nie jest to obsługiwane w przypadku przepływu danych. Zamierzasz użyć nowego modelu do przodu.

**Przykład**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które są obsługiwane przez Azure Cosmos DB (interfejs API SQL) i obiekty sink. Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (interfejs API SQL) jako źródło

Aby skopiować dane z Azure Cosmos DB (interfejs API SQL), ustaw typ **źródła** w działaniu Kopiuj na **DocumentDbCollectionSource**. 

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **CosmosDbSqlApiSource**. |Tak |
| query |Określ zapytanie usługi Azure Cosmos DB można odczytać danych.<br/><br/>Przykład:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, to instrukcja SQL jest wykonywana: `select <columns defined in structure> from mycollection` |
| preferredRegions | Preferowana lista regionów do połączenia podczas pobierania danych z Cosmos DB. | Nie |
| pageSize | Liczba dokumentów na stronie wyniku zapytania. Wartość domyślna to "-1", co oznacza użycie dynamicznego rozmiaru strony po stronie usługi do 1000. | Nie |

Jeśli używasz źródła typu "DocumentDbCollectionSource", jest ono nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu do przesyłania dalej, który zapewnia bogatsze możliwości kopiowania danych z Cosmos DB.

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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Przy kopiowaniu danych z Cosmos DB, chyba że chcesz [eksportować dokumenty JSON jako-is](#import-and-export-json-documents), najlepszym rozwiązaniem jest określenie mapowania w działaniu kopiowania. Data Factory przestrzega mapowania określonego w działaniu — Jeśli wiersz nie zawiera wartości dla kolumny, dla wartości kolumny zostanie podana wartość null. Jeśli nie określisz mapowania, Data Factory wnioskuje schemat przy użyciu pierwszego wiersza w danych. Jeśli pierwszy wiersz nie zawiera pełnego schematu, w wyniku operacji działania będzie brakować niektórych kolumn.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (interfejs API SQL) jako ujścia

Aby skopiować dane do Azure Cosmos DB (interfejs API SQL), ustaw typ **ujścia** w działaniu Copy na **DocumentDbCollectionSink**. 

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **CosmosDbSqlApiSink**. |Tak |
| writeBehavior |Opisuje sposób zapisywania danych do usługi Azure Cosmos DB. Dozwolone wartości: **Wstaw** i **upsert**.<br/><br/>Zachowanie **upsert** zastąpi dokumentu, jeśli dokument o tym samym identyfikatorze już istnieje; w przeciwnym razie Wstaw dokumentu.<br /><br />**Uwaga**: Data Factory automatycznie generuje identyfikator dla dokumentu, jeśli identyfikator nie został określony w oryginalnym dokumencie lub mapowania kolumn. Oznacza to, że użytkownik musi zapewnić, że dla **upsert** działała zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **Wstaw**) |
| writeBatchSize | Fabryka danych używa [biblioteki wykonawca zbiorcze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) można zapisać danych do usługi Azure Cosmos DB. Właściwość **writeBatchSize** kontroluje rozmiar dokumentów dostarczanych przez ADF do biblioteki. Można spróbować zwiększyć wartość **writeBatchSize** Aby zwiększyć wydajność i zmniejszenie wartości, jeśli dokument jest duży rozmiar — zobacz poniższe porady. |Nie<br />(wartość domyślna to **10 000**) |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak Cosmos DB jednostek ru na potrzeby optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, określ `true`, aby je wyłączyć. | Nie (wartość domyślna to `false`) |

>[!TIP]
>Aby zaimportować dokumenty JSON, należy zapoznać się z sekcją [Importowanie lub eksportowanie dokumentów JSON](#import-and-export-json-documents) . Aby skopiować dane w formie tabelarycznej, zapoznaj się z tematem [Migrowanie z relacyjnej bazy danych do Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Usługa cosmos DB ogranicza rozmiar pojedynczego żądania do 2MB. Formuła jest rozmiar żądania = rozmiar pojedynczego dokumentu * zapisu rozmiar partii. Jeśli napotkasz błąd powiedzenie **"Żądania" rozmiar jest za duży.** , **zmniejszyć `writeBatchSize` wartość** w konfiguracji obiektu sink kopiowania.

Jeśli używasz źródła typu "DocumentDbCollectionSink", jest ono nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu do przesyłania dalej, który zapewnia bogatsze możliwości kopiowania danych z Cosmos DB.

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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z Azure Cosmos DB do tabelarycznego ujścia lub odwrotnie, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać kolekcje w Cosmos DB. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla Azure Cosmos DB są dostępne na karcie **Opcje źródła** transformacji źródłowej. 

**Uwzględnij kolumny systemowe:** Jeśli wartość to true, ```id```, ```_ts```i inne kolumny systemowe zostaną uwzględnione w metadanych przepływu danych z CosmosDB. Podczas aktualizowania kolekcji należy uwzględnić to, aby można było uzyskać istniejący identyfikator wiersza.

**Rozmiar strony:** Liczba dokumentów na stronie wyniku zapytania. Wartość domyślna to "-1", która używa strony dynamicznej usługi do 1000.

**Przepływność:** Ustaw opcjonalną wartość liczby jednostek ru, która ma zostać zastosowana do kolekcji CosmosDB dla każdego wykonywania tego przepływu danych podczas operacji odczytu. Wartość minimalna to 400.

**Preferowane regiony:** Wybierz preferowane regiony odczytu dla tego procesu.

### <a name="sink-transformation"></a>Przekształcanie ujścia

Ustawienia specyficzne dla Azure Cosmos DB są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania. Aby zaktualizować, upsert lub usunąć wiersze, przekształcenie ALTER Row jest wymagane do tagowania wierszy dla tych działań. W przypadku aktualizacji, upserts i usunięć należy ustawić kolumnę klucza lub kolumny, aby określić, który wiersz ma być zmieniany.

**Akcja zbierania:** Określa, czy utworzyć ponownie kolekcję docelową przed zapisem.
* Brak: nie zostanie wykonana żadna akcja do kolekcji.
* Utwórz ponownie: kolekcja zostanie porzucona i utworzona ponownie

**Rozmiar wsadu**: określa, ile wierszy jest zapisywanych w każdym przedziale. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych.

**Klucz partycji:** Wprowadź ciąg, który reprezentuje klucz partycji dla kolekcji. Przykład: ```/movies/title```

**Przepływność:** Ustaw opcjonalną wartość liczby jednostek ru, która ma zostać zastosowana do kolekcji CosmosDB dla każdego wykonywania tego przepływu danych. Wartość minimalna to 400.

**Budżet przepływności zapisu:** Liczba całkowita reprezentująca liczbę jednostek ru, które mają zostać przydzielone do zadania platformy Spark pozyskiwania zbiorczego. Ta liczba jest poza łączną przepływność przydzieloną do kolekcji.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="import-and-export-json-documents"></a>Importowanie i eksportowanie dokumentów JSON

Za pomocą tego łącznika Azure Cosmos DB (interfejsu SQL API) można łatwo:

* Skopiuj dokumenty między dwie kolekcje usługi Azure Cosmos DB jako-to.
* Zaimportuj dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z usługi Azure Blob storage, Azure Data Lake Store i inne magazyny oparte na plikach, które obsługuje usługę Azure Data Factory.
* Eksportowanie dokumentów JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.

Aby osiągnąć kopiowania niezależny od schematów:

* Korzystając z narzędzia do kopiowania danych wybierz **wyeksportować jako — pliki w formacie JSON lub kolekcję usługi Cosmos DB** opcji.
* W przypadku korzystania z funkcji tworzenia działań wybierz format JSON z odpowiednim magazynem plików dla źródła lub ujścia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrowanie z relacyjnej bazy danych do Cosmos DB

W przypadku migrowania z relacyjnej bazy danych, np. SQL Server do Azure Cosmos DB, działanie kopiowania może łatwo mapować dane tabelaryczne ze źródła na spłaszczone dokumenty JSON w Cosmos DB. W niektórych przypadkach możesz chcieć ponownie zaprojektować model danych, aby zoptymalizować go do NoSQL przypadków użycia zgodnie z [modelem danych w Azure Cosmos DB](../cosmos-db/modeling-data.md), na przykład w celu denormalizacji danych, osadzając wszystkie powiązane elementy podrzędne w ramach jednego dokumentu JSON. W takim przypadku zapoznaj się z [tym wpisem w blogu](https://medium.com/@ArsenVlad/denormalizing-via-embedding-when-copying-data-from-sql-to-cosmos-db-649a649ae0fb) , aby zapoznać się z przewodnikiem dotyczącym korzystania z Azure Data Factory kopiowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
