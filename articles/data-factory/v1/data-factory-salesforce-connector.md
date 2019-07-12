---
title: Przenoszenie danych z usług Salesforce za pomocą usługi Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z usług Salesforce za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d33172727d4c654614463f69b83f7802cf7fb905
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839611"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Przenoszenie danych z usług Salesforce za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-salesforce-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-salesforce.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik usługi Salesforce w wersji 2](../connector-salesforce.md).

W tym artykule przedstawiono, jak użyć działania kopiowania w usłudze Azure data factory w celu skopiowania danych z usług Salesforce do dowolnego magazynu danych, który znajduje się w kolumnie obiekt Sink w [obsługiwane źródłami i ujściami](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania i kombinacji magazynu obsługiwanych danych.

Usługa Azure Data Factory obsługuje obecnie tylko przenosi dane z usług Salesforce do [obsługiwanych magazynów danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nie obsługuje przenoszenia danych z innych danych są przechowywane do usługi Salesforce.

## <a name="supported-versions"></a>Obsługiwane wersje
Ten łącznik obsługuje następujące wersje systemu Salesforce: Developer Edition w wersji Professional, Enterprise Edition lub wersji bez ograniczeń. I obsługuje kopiowania z produkcji i piaskownicy usługi Salesforce, a także domeny niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne
* Musi być włączone uprawnienia do interfejsu API. Zobacz [jak włączyć dostęp do interfejsu API w usłudze Salesforce przez zestaw uprawnień?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Aby skopiować dane z usług Salesforce do lokalnych magazynów danych, konieczne jest posiadanie co najmniej danych zarządzania bramy zainstalowany w wersji 2.0 w swoim środowisku w środowisku lokalnym.

## <a name="salesforce-request-limits"></a>Limity żądań usługi SalesForce
Usługa SalesForce ma limity współbieżnych żądań interfejsu API i łączna liczba żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczbą jednoczesnych żądań przekracza limit, wystąpi ograniczanie przepustowości, a następnie zostanie wyświetlony losowo występować usterki.
- Jeśli całkowita liczba żądań przekracza limit, konta usług Salesforce zostanie zablokowana przez 24 godziny.

Błąd "REQUEST_LIMIT_EXCEEDED" może również występować w obu scenariuszach. Zobacz sekcję "Limity żądań interfejsu API" w [limity dla deweloperów usługi Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artykuł, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Salesforce za pomocą różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z usług Salesforce, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z usług Salesforce do usługi Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Salesforce:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi połączonej usługi Salesforce.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| — typ |Właściwość type musi być równa: **Salesforce**. |Yes |
| environmentUrl | Określ wystąpienie adres URL usługi Salesforce. <br><br> -Wartością domyślną jest "https:\//login.salesforce.com". <br> -Aby skopiować dane z piaskownicy, należy określić "https://test.salesforce.com". <br> — Aby skopiować dane z domeny niestandardowej, określić, na przykład "https://[domain].my.salesforce.com". |Nie |
| username |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| password |Określ hasło dla konta użytkownika. |Yes |
| securityToken |Określ token zabezpieczeń dla konta użytkownika. Zobacz [uzyskać token zabezpieczający](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje na temat sposobu resetowania/get tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokenów zabezpieczających ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów zestawu danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table i tak dalej).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **RelationalTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnego obiektu niestandardowego.

![Nazwa interfejsu API — połączenie usługi Salesforce — usługi fabryka danych](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenia potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i dane wyjściowe tabel i różnych zasad są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działań, z drugiej strony, zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

W działaniu kopiowania, gdy źródłem jest typu **RelationalSource** (w tym usług Salesforce), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Zapytanie SQL 92 lub [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) zapytania. Na przykład: `select * from MyTable__c`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnego obiektu niestandardowego.

![Nazwa interfejsu API — połączenie usługi Salesforce — usługi fabryka danych](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Porady dotyczące zapytań
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Pobieranie danych przy użyciu gdzie klauzula w kolumnie daty/godziny
Gdy Określ zapytanie SOQL lub SQL, zwróć uwagę na różnicę format daty/godziny. Na przykład:

* **Przykładowe SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Przykładowe SQL**:
    * **Przy użyciu Kreatora kopiowania, aby określić zapytanie:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Przy użyciu formatu JSON edycję do określenia zapytania (znak ucieczki prawidłowo):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Trwa pobieranie danych z raportu w usłudze Salesforce
Można pobierać dane z raportów usług Salesforce, określając zapytania, ponieważ `{call "<report name>"}`, np. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Przywracanie usuniętych rekordów z Kosza usługi Salesforce
Aby wysłać zapytanie nietrwale usunięte rekordy z Kosza usługi Salesforce, można określić **"IsDeleted = 1"** w zapytaniu. Na przykład

* Aby wysłać zapytanie usuniętych rekordów, należy określić "Wybierz * z MyTable__c **gdzie IsDeleted = 1**"
* Aby zbadać wszystkie rekordy, w tym istniejące i usunięte, należy określić "Wybierz * z MyTable__c **gdzie IsDeleted = 0 lub IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z usług Salesforce do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak skopiować dane z usług Salesforce do usługi Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

Poniżej przedstawiono artefaktów usługi Data Factory, które należy utworzyć w celu zaimplementowania scenariusza. Sekcje listy zawierają szczegółowe informacje na temat tych kroków.

* Połączona usługa typu [Salesforce](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Usługi połączone usługi SalesForce**

W tym przykładzie użyto **Salesforce** połączoną usługę. Zobacz [Salesforce połączoną usługę](#linked-service-properties) sekcja dotycząca właściwości, które są obsługiwane w tej połączonej usługi. Zobacz [uzyskać token zabezpieczający](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje na temat sposobu resetowania/get tokenu zabezpieczającego.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Połączona usługa Azure Storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Wejściowy zestaw danych usługi SalesForce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ustawienie **zewnętrznych** do **true** usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnego obiektu niestandardowego.

![Nazwa interfejsu API — połączenie usługi Salesforce — usługi fabryka danych](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource**i **ujścia** ustawiono typ **BlobSink**.

Zobacz [właściwości typu RelationalSource](#copy-activity-properties) listę właściwości, które są obsługiwane przez RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnego obiektu niestandardowego.

![Nazwa interfejsu API — połączenie usługi Salesforce — usługi fabryka danych](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapowanie typu usługi Salesforce

| Typ SalesForce | . Typ opartej na sieci |
| --- | --- |
| Auto Number |Ciąg |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |Datetime |
| Date/Time |Datetime |
| Email |String |
| Id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |Ciąg |
| Picklist |Ciąg |
| Text |Ciąg |
| Text Area |Ciąg |
| Text Area (Long) |Ciąg |
| Text Area (Rich) |String |
| Text (Encrypted) |Ciąg |
| URL |Ciąg |

> [!NOTE]
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
