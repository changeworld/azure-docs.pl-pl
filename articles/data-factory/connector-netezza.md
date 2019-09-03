---
title: Kopiowanie danych z Netezza za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Netezza do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 7664c2f4fd08e06b51734b5508871b67d1a1b7c9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231411"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiowanie danych z Netezza za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze Netezza. Artykuł opiera się na [działania kopiowania w usłudze Azure Data Factory](copy-activity-overview.md), który ma ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z Netezza do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę danych przechowywane na tym, że działanie kopiowania obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).

Łącznik Netezza obsługuje równoległe kopiowanie ze źródła. Więcej informacji zawiera sekcja [Parallel Copy from Netezza](#parallel-copy-from-netezza) .

Usługa Azure Data Factory udostępnia wbudowane sterowników, aby włączyć łączność. Nie trzeba ręcznie zainstalować dowolnego sterownika, aby użyć tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Wprowadzenie

Można utworzyć potoku, który używa działania kopiowania przy użyciu zestawu .NET SDK, zestaw SDK języka Python, programu Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [samouczka działania kopiowania](quickstart-create-data-factory-dot-net.md) instrukcje krok po kroku dotyczące sposobu tworzenia potoku, który zawiera działania kopiowania.

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które służy do definiowania jednostek usługi Data Factory, które są specyficzne dla łącznika Netezza.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Usługi połączone Netezza obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość musi być równa **Netezza**. | Yes |
| connectionString | Ciąg połączenia ODBC, aby nawiązać połączenie Netezza. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `pwd` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określona, używana jest domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia jest `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. W poniższej tabeli opisano więcej właściwości, które można ustawić:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| SecurityLevel | Poziom zabezpieczeń (SSL/TLS), sterownik używany dla połączenia z magazynem danych. Przykład: `SecurityLevel=preferredSecured`. Obsługiwane są następujące wartości:<br/>- **Tylko niezabezpieczone** (**onlyUnSecured**): Sterownik nie używa protokołu SSL.<br/>- **Preferowana niezabezpieczona (preferredUnSecured) (domyślnie)** : Jeśli serwer zawiera wybór, sterownik nie używa protokołu SSL. <br/>- **Preferowany zabezpieczony (preferredSecured)** : Jeśli serwer zawiera wybór, sterownik używa protokołu SSL. <br/>- **Tylko zabezpieczone (onlySecured)** : Sterownik nie nawiązuje połączenia, o ile nie jest dostępne połączenie SSL. | Nie |
| CaCertFile | Pełna ścieżka do certyfikatu SSL, który jest używany przez serwer. Przykład: `CaCertFile=<cert path>;`| Tak, jeśli jest włączony protokół SSL |

**Przykład**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj hasło w Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
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

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych Netezza.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md).

Aby skopiować dane z Netezza, ustaw **typu** właściwości zestawu danych na **NetezzaTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **NetezzaTable** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| table | Nazwa tabeli. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i`table` dla nowego obciążenia. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które obsługuje źródła Netezza.

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako źródło

>[!TIP]
>Aby skutecznie ładować dane z Netezza przy użyciu partycjonowania danych, Dowiedz się więcej z sekcji [Kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) .

Aby skopiować dane z Netezza, ustaw **źródła** typ w działaniu kopiowania, aby **NetezzaSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | **Typu** właściwość źródła działania kopiowania musi być równa **NetezzaSource**. | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Przykład: `"SELECT * FROM MyTable"` | Nie (Jeśli określono parametr "tableName" w zestawie danych) |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z Netezza. <br>Dozwolone wartości to: **Brak** (wartość domyślna) , dataslice i **DynamicRange**.<br>Gdy opcja partycji jest włączona (to nie `None`jest), stopień równoległości do współbieżnego ładowania danych z bazy danych Netezza jest kontrolowany przez [`parallelCopies`](copy-activity-performance.md#parallel-copy) ustawienie dla działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji `None`nie jest. | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **w typie liczb całkowitych** , która będzie używana przez partycjonowanie zakresu do kopiowania równoległego. Jeśli nie zostanie określony, klucz podstawowy tabeli zostanie wykryty i użyty jako kolumna partycji. <br>Zastosuj, gdy opcja partycji to `DynamicRange`. Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionColumnName` w klauzuli WHERE. Zobacz przykład w sekcji [Kopiowanie równoległe z Netezza](#parallel-copy-from-netezza) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji `DynamicRange`jest. Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionUpbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Netezza](#parallel-copy-from-netezza) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji, w której mają zostać skopiowane dane. <br>Zastosuj, gdy opcja partycji to `DynamicRange`. Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfRangePartitionLowbound` w klauzuli WHERE. Aby zapoznać się z przykładem, zobacz sekcję [copy Parallel from Netezza](#parallel-copy-from-netezza) . | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Równoległa kopia z Netezza

Łącznik Data Factory Netezza zapewnia wbudowane Partycjonowanie danych do kopiowania danych ze Netezza równolegle. Opcje partycjonowania danych można znaleźć w tabeli **źródłowej** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-netezza/connector-netezza-partition-options.png)

Po włączeniu kopiowania partycjonowanego Data Factory uruchamia zapytania równoległe względem źródła Netezza w celu załadowania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z bazy danych Netezza.

Przed załadowaniem dużej ilości danych z bazy danych Netezza zaleca się włączenie kopiowania równoległego przy użyciu partycjonowania danych. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach, należy ponownie wykonać zapis do folderu jako wiele plików (Określ tylko nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli.                                   | **Opcja partycji**: Wycinek danych. <br><br/>Podczas wykonywania Data Factory automatycznie dzieli dane na podstawie [wbudowanych wycinków danych Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)i kopiuje dane przez partycje. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej.                 | **Opcja partycji**: Wycinek danych.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Podczas wykonywania Data Factory zastępuje `?AdfPartitionCount` (z liczbą równoległą kopiowania ustawioną w działaniu kopiowania) i `?AdfDataSliceCondition` z logiką partycji wycinka danych i wysyła do Netezza. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego, mając kolumnę Integer z równomiernie rozproszoną wartością dla partycjonowania zakresu. | **Opcje partycji**: Dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumnę służącą do partycjonowania danych. Można podzielić na kolumny z typem danych Integer.<br>**Górna** granica partycji i **Dolna granica partycji**: Określ, czy chcesz filtrować względem kolumny partycji, aby pobierać dane tylko między niższym i górnym zakresem.<br><br>Podczas wykonywania Data Factory zamienia `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, i `?AdfRangePartitionLowbound` z rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do Netezza. <br>Na przykład jeśli kolumna partycji "ID" ma ustawioną dolną granicę 1 i górną granicę 80, z kopią równoległą ustawioną na wartość 4, Data Factory pobiera dane przez 4 partycje. Ich identyfikatory należą do zakresu od [1, 20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: zapytanie z partycją wycinka danych**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działania kopiowania obsługuje jako źródła i sink w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
