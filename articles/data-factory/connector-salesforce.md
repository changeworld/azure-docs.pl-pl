---
title: Kopiowanie danych z i do usług Salesforce przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Salesforce do magazynów danych obsługiwanych zbiornika lub obsługiwanych źródłowych magazyny danych do usługi Salesforce za pomocą działania kopiowania w potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: f4de97ef2df5351ac7e8574717ee1439b54a90e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopiowanie danych z i do usług Salesforce przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-salesforce-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-salesforce.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do usług Salesforce. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 fabryki danych, która jest ogólnie dostępna, zobacz [łącznika usług Salesforce w wersji 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych z usług Salesforce. Możesz również skopiować danych z dowolnego źródła obsługiwanych magazynu danych do usług Salesforce. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności tego łącznika usług Salesforce obsługuje:

- Wersje deweloperów usług SalesForce, Professional, Enterprise lub bez ograniczeń.
- Kopiowanie danych z i do produkcji Salesforce, piaskownicy i domeny niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienia interfejsu API musi być włączony w usłudze Salesforce. Aby uzyskać więcej informacji, zobacz [API włączyć dostępu w usłudze Salesforce przez zestaw uprawnień](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limity żądań usług SalesForce

Usługa SalesForce obejmuje limity dla całkowita liczba żądań interfejsu API i równoczesnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba jednoczesnych żądań przekracza limit, ograniczanie występuje i zobacz losowe awarie.
- Jeśli całkowita liczba żądań przekracza limit, konta usług Salesforce jest zablokowany przez 24 godziny.

Może również pojawi się komunikat o błędzie "REQUEST_LIMIT_EXCEEDED" w obu przypadkach. Aby uzyskać więcej informacji, zobacz sekcję "Limity żądań interfejsu API" w [limity developer Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika usług Salesforce.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane dla usługi Salesforce połączone.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną **Salesforce**. |Yes |
| environmentUrl | Określ adres URL wystąpienia usług Salesforce. <br> -Domyślna to `"https://login.salesforce.com"`. <br> -Aby skopiować dane z piaskownicy, określ `"https://test.salesforce.com"`. <br> -Aby skopiować dane z domeny niestandardowej, na przykład określić, `"https://[domain].my.salesforce.com"`. |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| hasło |Określ hasło dla konta użytkownika.<br/><br/>Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| securityToken |Określ tokenu zabezpieczającego dla konta użytkownika. Aby uzyskać instrukcje na temat resetowania i pobrać tokenu zabezpieczeń, zobacz [uzyskać token zabezpieczeń](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Aby dowiedzieć się więcej o tokeny zabezpieczające ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie źródła, tak dla obiekt sink Jeśli źródło połączona usługa nie ma środowiska uruchomieniowego integracji |

>[!IMPORTANT]
>Po skopiowaniu danych do usługi Salesforce domyślnego środowiska uruchomieniowego integracji Azure nie może służyć do wykonywania kopii. Innymi słowy, jeśli połączone źródła usługa nie ma określonej integracji środowiska uruchomieniowego, jawnie [utworzyć środowiska uruchomieniowego integracji Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu wystąpienia usług Salesforce. Kojarzenie usługi Salesforce połączone, jak w poniższym przykładzie.

**Przykład: Przechowywania poświadczeń w fabryce danych**

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

**Przykład: Przechowywania poświadczeń w magazynie kluczy**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Salesforce.

Aby skopiować dane z i do usług Salesforce, ustaw właściwość Typ zestawu danych do **SalesforceObject**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **SalesforceObject**.  | Yes |
| objectApiName | Nazwa obiektu Salesforce można pobrać danych z. | Brak źródła tak dla obiekt sink |

> [!IMPORTANT]
> Część "__c" **Nazwa interfejsu API** jest wymagany dla dowolnych niestandardowych obiektów.

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
>Dla zgodności z poprzednimi wersjami: po skopiowaniu danych z usług Salesforce, korzystając z poprzedniego zestawu danych typu "RelationalTable", śledzi pracę w trakcie Zobacz sugestii, aby przełączyć się do nowego typu "SalesforceObject".

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwości typu zestawu danych musi mieć ustawioną **RelationalTable**. | Yes |
| tableName | Nazwa tabeli w usłudze Salesforce. | Nie (Jeśli określono parametr "zapytania" w źródle działania) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez usługi Salesforce źródłowy i odbiorczy właściwości.

### <a name="salesforce-as-a-source-type"></a>SalesForce jako typ źródła

Aby skopiować dane z witryny Salesforce, Ustaw typ źródła w przypadku działania kopiowania do **SalesforceSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **SalesforceSource**. | Yes |
| query |Użyj niestandardowych zapytania można odczytać danych. Można użyć zapytania SQL 92 lub [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) zapytania. Może to być na przykład `select * from MyTable__c`. | Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |
| readBehavior | Wskazuje, czy wysyłać zapytania o istniejące rekordy lub wysyłać zapytania o wszystkie rekordy tym usunięto te. Jeśli nie zostanie określony, domyślne zachowanie to pierwsza. <br>Dozwolone wartości: **zapytania** (ustawienie domyślne), **queryAll**.  | Nie |

> [!IMPORTANT]
> Część "__c" **Nazwa interfejsu API** jest wymagany dla dowolnych niestandardowych obiektów.

![Połączenie usługi Salesforce fabryki danych listy Nazwa interfejsu API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Dla zgodności z poprzednimi wersjami: po skopiowaniu danych z usług Salesforce, korzystając z poprzedniej kopii typu "RelationalSource", źródło śledzi pracę w trakcie Zobacz sugestii, aby przełączyć się do nowego typu "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>SalesForce jako typ ujścia

Aby skopiować dane do usługi Salesforce, należy ustawić typ ujścia w działaniu kopiowania do **SalesforceSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania **SalesforceSink**. | Yes |
| writeBehavior | Zachowanie zapisu dla tej operacji.<br/>Dozwolone wartości to **Wstaw** i **Upsert**. | Nie (wartość domyślna to Insert) |
| externalIdFieldName | Nazwa pola identyfikator zewnętrznego dla operacji upsert. Określone pole musi być zdefiniowany jako "Zewnętrzne pole Id" w obiekcie Salesforce. Go nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak, aby "Upsert" |
| writeBatchSize | Liczba wierszy z danymi zapisywanymi w Salesforce w każdej z partii. | Nie (wartość domyślna to 5000) |
| ignoreNullValues | Wskazuje, czy mają zostać zignorowane wartości NULL w danych wejściowych podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **Wartość true,**: Pozostaw bez zmian danych w obiekcie docelowym, po wykonaniu operacji upsert lub aktualizacji. Wstaw wartości domyślnej zdefiniowanej po wykonaniu operacji wstawiania.<br/>- **FALSE**: zaktualizować dane w obiekcie docelowym null po wykonaniu operacji upsert lub aktualizacji. Wstaw wartość NULL, po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |

**Przykład: Salesforce zbiornika w działaniu kopiowania**

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

## <a name="query-tips"></a>Wskazówki zapytania

### <a name="retrieve-data-from-a-salesforce-report"></a>Pobieranie danych z raportu usług Salesforce

Można pobrać dane z raportów usług Salesforce, określając kwerendy w postaci `{call "<report name>"}`. Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Pobieranie usuniętych rekordów z Salesforce Kosza

Kwerenda nietrwałego usuniętych rekordów z Salesforce Kosza, można określić **"IsDeleted = 1"** w zapytaniu. Na przykład:

* Aby odpytać tylko usunięte rekordy, określ "Wybierz * z MyTable__c **gdzie IsDeleted = 1**."
* Aby zapytania, wszystkie rekordy, w tym istniejące i usunięte, należy określić "Wybierz * z MyTable__c **gdzie IsDeleted = 0 lub IsDeleted = 1**."

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Pobieranie danych przy użyciu klauzuli where klauzuli według kolumny daty i godziny

Po określeniu zapytania SOQL lub SQL, należy zwrócić uwagę na różnica format daty/godziny. Na przykład:

* **Przykładowe SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Przykładowe SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Mapowanie dla usług Salesforce typu danych

Po skopiowaniu danych z usług Salesforce następujące mapowania są używane z typów danych Salesforce do typów danych tymczasowych fabryki danych. Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink, zobacz temat [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md).

| Typ danych witryny SalesForce | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Automatyczny numer |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |Podwójnej precyzji |
| Date |DateTime |
| Data/godzina |DateTime |
| Email |Ciąg |
| Identyfikator |Ciąg |
| Relacja wyszukiwania |Ciąg |
| Lista wyboru wielokrotnego wyboru |Ciąg |
| Liczba |Podwójnej precyzji |
| Procent |Podwójnej precyzji |
| Numer telefonu |Ciąg |
| Lista wyboru |Ciąg |
| Tekst |Ciąg |
| Obszar tekstu |Ciąg |
| Obszar tekstu (Liczba długa) |Ciąg |
| Obszar tekstu (zaawansowana) |Ciąg |
| Tekst (zaszyfrowane) |Ciąg |
| Adres URL |Ciąg |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).