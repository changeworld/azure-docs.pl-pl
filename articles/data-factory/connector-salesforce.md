---
title: Kopiowanie danych z i do usługi Salesforce za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z usług Salesforce do magazynów danych ujścia obsługiwanych lub obsługiwanych źródłowych magazynów danych do usługi Salesforce za pomocą działania kopiowania w potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: jingwang
ms.openlocfilehash: 6056df9aa9079887bfb06ca20ad564eb52baff38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546576"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopiowanie danych z i do usługi Salesforce za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-salesforce-connector.md)
> * [Bieżąca wersja](connector-salesforce.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Salesforce. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z usług Salesforce, do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Salesforce. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Salesforce obsługuje:

- Wersje usługi SalesForce dla deweloperów, Professional, Enterprise lub bez ograniczeń.
- Kopiowanie danych z i do produkcji Salesforce, piaskownica i domeny niestandardowej.

Łącznik Salesforce bazuje na usłudze REST/zbiorcze interfejsu API usługi Salesforce z [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z i [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) kopiowania danych.

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienia do interfejsu API musi być włączona w usłudze Salesforce. Aby uzyskać więcej informacji, zobacz [dostępu Włącz interfejs API w usłudze Salesforce przez zestaw uprawnień](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limity żądań usługi SalesForce

Usługa SalesForce ma limity współbieżnych żądań interfejsu API i łączna liczba żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczbą jednoczesnych żądań przekracza limit, ograniczanie występuje i widzisz błędy losowe.
- Jeśli całkowita liczba żądań przekracza limit, konta usług Salesforce jest zablokowany przez 24 godziny.

W obu scenariuszach również otrzymać komunikat o błędzie "REQUEST_LIMIT_EXCEEDED". Aby uzyskać więcej informacji, zobacz sekcję "Limity żądań interfejsu API" w [limity dla deweloperów usługi Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Salesforce.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi połączonej usługi Salesforce.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa **Salesforce**. |Yes |
| environmentUrl | Określ adres URL wystąpienia usług Salesforce. <br> -Domyślnie `"https://login.salesforce.com"`. <br> -Aby skopiować dane z piaskownicy, należy określić `"https://test.salesforce.com"`. <br> -Aby skopiować dane z domeny niestandardowej, określić, na przykład `"https://[domain].my.salesforce.com"`. |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| password |Określ hasło dla konta użytkownika.<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| securityToken |Określ token zabezpieczeń dla konta użytkownika. Aby uzyskać instrukcje na temat resetowania i pobrać tokenu zabezpieczeń, zobacz [uzyskać token zabezpieczający](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Aby dowiedzieć się więcej o tokenów zabezpieczających ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie dla źródła, tak ujścia Jeśli źródłem jest połączona usługa nie ma środowiska integration runtime |

>[!IMPORTANT]
>Podczas kopiowania danych z usługą Salesforce, domyślnego środowiska Azure Integration Runtime nie może służyć do wykonywania kopii. Innymi słowy, jeśli połączona ze źródłem usługi nie ma określonego IR jawnie [utworzyć środowisko IR Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu wystąpienia usługi Salesforce. Kojarzenie usługi połączone usługi Salesforce, jak w poniższym przykładzie.

**Przykład: Store poświadczeń w usłudze Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

**Przykład: Store poświadczeń w usłudze Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Salesforce.

Aby skopiować dane z i do usługi Salesforce, należy ustawić właściwość typu zestawu danych na **SalesforceObject**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SalesforceObject**.  | Yes |
| objectApiName | Nazwa obiektu usług Salesforce do pobierania danych z. | Brak źródła tak dla ujścia |

> [!IMPORTANT]
> Część "__c" **Nazwa interfejsu API** jest wymagany dla dowolnego obiektu niestandardowego.

![Połączenie usługi Salesforce fabryki danych nazwa interfejsu API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Przykład:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Dla zgodności z poprzednimi wersjami: Podczas kopiowania danych z usług Salesforce, jeśli korzystasz z poprzednim zestawem danych typu "RelationalTable", nadal działa, gdy zostanie wyświetlony sugestii, aby przełączyć się do nowego typu "SalesforceObject".

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **RelationalTable**. | Yes |
| tableName | Nazwa tabeli w usłudze Salesforce. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługi Salesforce źródła i ujścia.

### <a name="salesforce-as-a-source-type"></a>SalesForce jako typ źródła

Aby skopiować dane z usług Salesforce, należy ustawić typ źródła w działaniu kopiowania, aby **SalesforceSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **SalesforceSource**. | Yes |
| query |Użyj zapytania niestandardowe można odczytać danych. Możesz użyć [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) SQL 92 lub kwerendy. Zobacz więcej porad w [zapytania porady](#query-tips) sekcji. Jeśli zapytanie nie zostanie określony, wszystkie dane z obiektu usług Salesforce określonego w "objectApiName" w zestawie danych będą pobierane. | Nie (Jeśli określono parametr "objectApiName" w zestawie danych) |
| readBehavior | Wskazuje, czy wysyłać zapytania o istniejące rekordy lub wysyłać zapytania o wszystkie rekordy, włącznie z tymi usunięte. Jeśli nie zostanie określony, domyślnym zachowaniem jest pierwsza. <br>Dozwolone wartości: **zapytania** (ustawienie domyślne), **queryAll**.  | Nie |

> [!IMPORTANT]
> Część "__c" **Nazwa interfejsu API** jest wymagany dla dowolnego obiektu niestandardowego.

![Połączenie usługi Salesforce fabryki danych listy nazw interfejsu API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Dla zgodności z poprzednimi wersjami: Podczas kopiowania danych z usług Salesforce, jeśli używasz poprzedniej kopii typu "RelationalSource", źródło nadal działa, a zobaczysz sugestię, aby przełączyć się do nowego typu "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>SalesForce jako typ ujścia

Aby skopiować dane do usługi Salesforce, należy ustawić typ ujścia w działaniu kopiowania, aby **SalesforceSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **SalesforceSink**. | Yes |
| writeBehavior | Zachowanie zapisu dla tej operacji.<br/>Dozwolone wartości to **Wstaw** i **Upsert**. | Nie (wartość domyślna to wstawiania) |
| externalIdFieldName | Nazwa pola Identyfikatora zewnętrznego dla operacji upsert. Określone pole musi być zdefiniowany jako "Pole identyfikatora zewnętrznego" w obiekcie usługi Salesforce. Go nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak, aby "Upsert" |
| writeBatchSize | Liczba wierszy danych zapisanych w każdej z partii usługi Salesforce. | Nie (wartość domyślna to 5000) |
| ignoreNullValues | Wskazuje, czy ignorować wartości NULL w danych wejściowych podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **Wartość true,**: Pozostawiają dane w obiekcie docelowym niezmieniony po wykonaniu operacji upsert lub aktualizacji. Po wykonaniu operacji wstawiania, należy wstawić zdefiniowana wartość domyślna.<br/>- **FALSE**: Zaktualizować dane w obiekcie docelowym na wartość NULL, podczas wykonywania operacji upsert lub aktualizacji. Po wykonaniu operacji wstawiania, należy wstawić wartość NULL. | Nie (wartość domyślna to false) |

**Przykład: SalesForce ujścia w działaniu kopiowania**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
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

### <a name="retrieve-data-from-a-salesforce-report"></a>Pobieranie danych z raportu usługi Salesforce

Można pobierać dane z raportów usług Salesforce, określając zapytania jako `{call "<report name>"}`. Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Pobieranie rekordów usuniętych z usługi Salesforce Kosza usługi

Aby wysłać zapytanie nietrwale usunięte rekordy z usług Salesforce Kosza, można określić `readBehavior` jako `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Różnica między SOQL, SQL i składnia zapytań

Podczas kopiowania danych z usług Salesforce, można użyć SOQL zapytania lub zapytanie SQL. Zwróć uwagę, te dwie zawiera różnej składni i obsługę funkcji, nie należy mieszać. Zaleca się użyć tej kwerendy SOQL, co jest natywnie obsługiwane przez usługi Salesforce. W poniższej tabeli wymieniono najważniejsze różnice:

| Składnia | Tryb SOQL | Tryb SQL |
|:--- |:--- |:--- |
| Wybór kolumn | Należy wyliczyć pola, które mają zostać skopiowane do zapytania, np. `SELECT field1, filed2 FROM objectname` | `SELECT *` jest obsługiwane, oprócz kolumnę zaznaczenia. |
| Znaki cudzysłowu | Nazwy wprowadzone obiektów nie może być ujmowane w cudzysłów. | Nazwy pól/obiektów mogą być ujmowane w cudzysłów, np. `SELECT "id" FROM "Account"` |
| Format daty/godziny |  Zobacz szczegóły dotyczące [tutaj](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) i przykłady w następnej sekcji. | Zobacz szczegóły dotyczące [tutaj](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) i przykłady w następnej sekcji. |
| Wartości logiczne | Reprezentowana jako `False` i `True`, np. `SELECT … WHERE IsDeleted=True`. | Reprezentowana jako 0 lub 1, np. `SELECT … WHERE IsDeleted=1`. |
| Zmiana nazwy kolumny | Nieobsługiwane. | Obsługiwane, np.: `SELECT a AS b FROM …`. |
| Relacja | Obsługiwane, np. `Account_vod__r.nvs_Country__c`. | Nieobsługiwane. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Pobierać dane przy użyciu klauzuli where klauzuli kolumny daty i godziny

Po określeniu zapytania SOQL lub SQL, należy zwrócić uwagę na różnicę format daty/godziny. Na przykład:

* **Przykładowe SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Przykładowe SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformedquerytruncated"></a>Błąd MALFORMED_QUERY: obcięty

Jeśli napotkasz błąd "MALFORMED_QUERY: Przycięty", zwykle jest ze względu na masz JunctionIdList typ kolumny w danych i usługa Salesforce ma ograniczenie obsługujące takich danych z dużą liczbę wierszy. Aby rozwiązać problem, spróbuj wysłać wykluczanie JunctionIdList kolumny lub ograniczyć liczbę wierszy, aby skopiować (można podzielić na wiele uruchomienia działania kopiowania).

## <a name="data-type-mapping-for-salesforce"></a>Typ danych mapowanie dla usług Salesforce

Podczas kopiowania danych z usług Salesforce, następujące mapowania są używane z typów danych usługi Salesforce do typów danych tymczasowych fabryki danych. Aby uzyskać informacje dotyczące sposobu działania kopiowania mapowania typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych usługi SalesForce | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Liczba automatycznie |String |
| Zaznacz pole wyboru |Boolean |
| Waluta |Decimal |
| Date |DateTime |
| Data/godzina |DateTime |
| Email |String |
| Identyfikator |String |
| Relacje odnośników |String |
| Lista wyboru wielokrotnego |String |
| Liczba |Decimal |
| Procent |Decimal |
| Numer telefonu |String |
| Listy wyboru |String |
| Text |String |
| Obszar tekstu |String |
| Obszar tekstu (Long) |String |
| Obszar tekstu (zaawansowane) |String |
| Tekst (zaszyfrowane) |String |
| Adres URL |String |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
