---
title: Skopiuj dane z i do chmury usługi Salesforce przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z chmury usługi Salesforce do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do chmury usług Salesforce za pomocą działania kopiowania w potoku usługi Fabryka danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 729ea0fa667a11f710fd815003bc0995cb08ae70
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842558"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Skopiuj dane z i do chmury usługi Salesforce przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do chmury usługi Salesforce. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z chmury usługi Salesforce można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do chmury usługi Salesforce. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W ramach tego łącznika usługi Salesforce w chmurze obsługuje:

- Wersje Developer, Professional, Enterprise i Unlimited usługi Salesforce.
- Kopiowanie danych z i do środowiska produkcyjnego, piaskownicy i niestandardowej domeny usługi Salesforce.

Łącznik usługi Salesforce w chmurze jest oparty na interfejsie API REST/Bulk usług Salesforce z [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z i [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) do kopiowania danych do programu.

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienie API musi być włączone w usłudze Salesforce. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do interfejsu API w usłudze Salesforce według zestawu uprawnień](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limity żądań usługi Salesforce

Usługi Salesforce mają limity dla obu żądań interfejsu API i współbieżnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba współbieżnych żądań przekracza limit, nastąpi ograniczenie i zobaczysz błędy losowe.
- Jeśli łączna liczba żądań przekracza limit, konto usługi Salesforce jest blokowane przez 24 godziny.

W obu scenariuszach może być również wyświetlany komunikat o błędzie "REQUEST_LIMIT_EXCEEDED". Aby uzyskać więcej informacji, zobacz sekcję "limity żądań interfejsu API" w obszarze [limity deweloperów usługi Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Salesforce w chmurze.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi Salesforce są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość Type musi być ustawiona na wartość **SalesforceServiceCloud**. |Yes |
| environmentUrl | Określ adres URL wystąpienia chmury usługi Salesforce. <br> -Wartość domyślna `"https://login.salesforce.com"`to. <br> -Aby skopiować dane z piaskownicy, `"https://test.salesforce.com"`Określ. <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład `"https://[domain].my.salesforce.com"`,. |Nie |
| username |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| password |Określ hasło dla konta użytkownika.<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| securityToken |Określ token zabezpieczający dla konta użytkownika. Instrukcje dotyczące resetowania i uzyskiwania tokenu zabezpieczającego znajdują się w temacie [Get a Security Token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Aby uzyskać ogólne informacje na temat tokenów zabezpieczających, zobacz [zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie dla źródła, tak dla ujścia, jeśli źródłowa usługa nie ma środowiska Integration Runtime |

>[!IMPORTANT]
>Podczas kopiowania danych do chmury usługi Salesforce nie można użyć domyślnego Azure Integration Runtime do wykonania kopiowania. Innymi słowy, jeśli źródłowa usługa połączona nie ma określonego środowiska Integration Runtime, jawnie [utwórz Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) przy użyciu lokalizacji zbliżonej do wystąpienia chmury usługi Salesforce. Skojarz połączoną usługę w chmurze usługi Salesforce, jak w poniższym przykładzie.

**Przykład: Przechowywanie poświadczeń w Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowywanie poświadczeń w Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych w chmurze usługi Salesforce.

Aby skopiować dane z i do chmury usługi Salesforce, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **SalesforceServiceCloudObject**.  | Tak |
| objectApiName | Nazwa obiektu usług Salesforce, z którego mają zostać pobrane dane. | Brak źródła tak dla ujścia |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory nazwę interfejsu API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Przykład:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **relacyjną**. | Tak |
| tableName | Nazwa tabeli w chmurze usługi Salesforce. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia w chmurze usługi Salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Chmura usługi Salesforce jako typ źródła

Aby skopiować dane z chmury usługi Salesforce, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SalesforceServiceCloudSource**. | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Można użyć zapytania [SOQL (Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lub zapytania SQL-92. Zobacz więcej porad w sekcji [porady dotyczące zapytań](#query-tips) . Jeśli nie określono zapytania, zostaną pobrane wszystkie dane obiektu chmury usługi Salesforce określonego w "objectApiName" w zestawie danych. | Nie (Jeśli określono wartość "objectApiName" w zestawie danych) |
| readBehavior | Wskazuje, czy mają być zbadane istniejące rekordy, czy też mają być poszukiwane wszystkie rekordy, w tym usunięte. Jeśli nie zostanie określony, domyślnym zachowaniem jest pierwsze. <br>Dozwolone wartości: **zapytanie** (wartość domyślna), **queryAll**.  | Nie |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory lista nazw interfejsów API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Chmura usługi Salesforce jako typ ujścia

Aby skopiować dane do chmury usługi Salesforce, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SalesforceServiceCloudSink**. | Tak |
| writeBehavior | Zachowanie zapisu dla operacji.<br/>Dozwolone wartości to **INSERT** i **upsert**. | Nie (wartość domyślna to Insert) |
| externalIdFieldName | Nazwa pola identyfikatora zewnętrznego dla operacji upsert. Określone pole musi być zdefiniowane jako "pole identyfikatora zewnętrznego" w obiekcie chmury usługi Salesforce. Nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak dla "upsert" |
| writeBatchSize | Liczba wierszy danych zapisywana w chmurze usługi Salesforce w każdej partii. | Nie (domyślnie 5 000) |
| ignoreNullValues | Wskazuje, czy ignorować wartości NULL z danych wejściowych podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **Wartość true**: Pozostaw dane w obiekcie docelowym bez zmian po wykonaniu operacji upsert lub Update. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **Wartość false**: Zaktualizuj dane w obiekcie docelowym do wartości NULL po wykonaniu operacji upsert lub Update. Wstaw wartość NULL po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Porady dotyczące zapytań

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Pobieranie danych z raportu w chmurze usługi Salesforce

Dane można pobrać z raportów w chmurze usługi Salesforce, określając zapytanie jako `{call "<report name>"}`. Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Pobieranie usuniętych rekordów z kosza chmury usługi Salesforce

Aby wykonać zapytanie dotyczące usuniętych nietrwałych rekordów z Kosza usługi Salesforce w chmurze, możesz `readBehavior` określić `queryAll`jako. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Różnica między SOQL i składnią zapytania SQL

Podczas kopiowania danych z chmury usługi Salesforce można użyć zapytania SOQL lub zapytania SQL. Należy pamiętać, że te dwa mają różne składnie i funkcje, nie należy ich mieszać. Zalecane jest użycie zapytania SOQL, które jest natywnie obsługiwane przez chmurę usługi Salesforce. W poniższej tabeli wymieniono główne różnice:

| Składnia | Tryb SOQL | Tryb SQL |
|:--- |:--- |:--- |
| Wybór kolumny | Należy wyliczyć pola, które mają być skopiowane do zapytania, np.`SELECT field1, filed2 FROM objectname` | `SELECT *`jest obsługiwana oprócz zaznaczenia kolumny. |
| Cudzysłowy | Nazwy zgłoszonych/obiektów nie mogą być ujęte w cudzysłów. | Nazwy pól/obiektów mogą być ujęte w cudzysłów, np.`SELECT "id" FROM "Account"` |
| Format daty i godziny |  Zapoznaj się z informacjami [tutaj](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) i przykładami w następnej sekcji. | Zapoznaj się z informacjami [tutaj](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) i przykładami w następnej sekcji. |
| Wartości logiczne | Reprezentowane jako `False` i `True`, np. `SELECT … WHERE IsDeleted=True`. | Reprezentowane jako 0 lub 1, np. `SELECT … WHERE IsDeleted=1`. |
| Zmiana nazwy kolumny | Nieobsługiwane. | Obsługiwane, np.: `SELECT a AS b FROM …`. |
| Relacja | Obsługiwane, np. `Account_vod__r.nvs_Country__c`. | Nieobsługiwane. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Pobieranie danych przy użyciu klauzuli WHERE w kolumnie DateTime

Po określeniu zapytania SOQL lub SQL należy zwrócić uwagę na różnice w formacie daty/godziny. Na przykład:

* **Przykład SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Przykład SQL**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Błąd MALFORMED_QUERY: obcięty

Jeśli wystąpi błąd elementu "MALFORMED_QUERY: Obcięty ", zwykle jest to spowodowane tym, że masz kolumnę typu JunctionIdList w danych, a w usłudze Salesforce obowiązuje ograniczenie obsługi takich danych o dużej liczbie wierszy. Aby wyeliminować problem, spróbuj wykluczyć kolumnę JunctionIdList lub ograniczyć liczbę wierszy do skopiowania (można podzielić na wiele uruchomień działania kopiowania).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapowanie typu danych dla chmury usługi Salesforce

Podczas kopiowania danych z chmury usługi Salesforce następujące mapowania są używane w ramach typów danych w chmurze usługi Salesforce do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych w chmurze usługi Salesforce | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |Datetime |
| Date/Time |Datetime |
| Email |String |
| Id |Ciąg |
| Lookup Relationship |String |
| Multi-Select Picklist |Ciąg |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |Ciąg |
| Text Area |Ciąg |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |Ciąg |
| URL |Ciąg |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
