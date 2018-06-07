---
title: Przenieść dane z witryny Salesforce przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z usług Salesforce przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dddb29d2a796449d6a2c9b68e01e94f32d3022e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621993"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Przenieść dane z witryny Salesforce przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-salesforce-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-salesforce.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika usług Salesforce w wersji 2](../connector-salesforce.md).


W tym artykule opisano, jak używasz działanie kopiowania w fabryce danych Azure można skopiować danych z usług Salesforce na dowolnym magazynem danych, który znajduje się w kolumnie zbiornika w [obsługiwanych źródeł i wychwytywanie](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z kombinacji magazynu obsługiwane dane i działanie kopiowania.

Fabryka danych Azure aktualnie obsługuje tylko przenoszenia danych z usługi Salesforce, aby [obsługiwane magazyny danych zbiornika](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nie obsługuje przenoszenia danych z innych danych przechowuje do usług Salesforce.

## <a name="supported-versions"></a>Obsługiwane wersje
Ten łącznik obsługuje następujące wersje usług Salesforce: Developer Edition w wersji Professional, Enterprise Edition albo nieograniczone Edition. I obsługuje kopiowanie z Salesforce produkcji, piaskownicy i domeny niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne
* Musi być włączone uprawnienia interfejsu API. Zobacz [jak włączyć dostęp do interfejsu API w usłudze Salesforce przez zestaw uprawnień?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Aby skopiować dane z witryny Salesforce do lokalnych magazynów danych, użytkownik musi mieć co najmniej 2.0 bramy zarządzania danych zainstalowanej w środowisku lokalnym.

## <a name="salesforce-request-limits"></a>Limity żądań usług SalesForce
Usługa SalesForce obejmuje limity dla całkowita liczba żądań interfejsu API i równoczesnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba jednoczesnych żądań przekracza limit, ograniczanie występuje i będzie wyświetlany losowe awarie.
- Jeśli całkowita liczba żądań przekracza limit, konta usług Salesforce jest blokowana przez 24 godziny.

W obu przypadkach może być również wyświetlony błąd "REQUEST_LIMIT_EXCEEDED". Zobacz sekcję "Limity żądań interfejsu API" w [Salesforce Developer limity](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artykułu, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z witryny Salesforce przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych z usług Salesforce, zobacz [przykład JSON: kopiowanie danych z usług Salesforce do obiektów Blob platformy Azure](#json-example-copy-data-from-salesforce-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do usług Salesforce: 

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi Salesforce połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **Salesforce**. |Yes |
| environmentUrl | Określ wystąpienie adres URL usługi Salesforce. <br><br> -Domyślna to "https://login.salesforce.com". <br> -Aby skopiować dane z piaskownicy, określ "https://test.salesforce.com". <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład "https://[domain].my.salesforce.com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| hasło |Określ hasło dla konta użytkownika. |Yes |
| securityToken |Określ tokenu zabezpieczającego dla konta użytkownika. Zobacz [uzyskać token zabezpieczeń](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje dotyczące resetowania/Get tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokeny zabezpieczające ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, tabeli platformy Azure i tak dalej).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **RelationalTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnych niestandardowych obiektów.

![Nazwa danych fabryki — połączenie usługi Salesforce — interfejs API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak tabele nazwę, opis, danych wejściowych i wyjściowych i różnych zasad są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony, zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

W przypadku działania kopiowania, gdy źródłem jest typu **RelationalSource** (która obejmuje usługi Salesforce), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Zapytania SQL 92 lub [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) zapytania. Przykład: `select * from MyTable__c`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnych niestandardowych obiektów.

![Nazwa danych fabryki — połączenie usługi Salesforce — interfejs API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Wskazówki zapytania
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Podczas pobierania danych przy użyciu where klauzuli według kolumny daty i godziny
Gdy Określ SOQL lub SQL zapytanie, zwrócić uwagę różnica format daty/godziny. Na przykład:

* **Przykładowe SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Przykładowe SQL**:
    * **Określ zapytanie za pomocą Kreatora kopiowania:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Za pomocą edycji, aby określić zapytanie JSON (prawidłowo ucieczki znaku):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Pobieranie danych z raportu usług Salesforce
Można pobrać dane z raportów usług Salesforce, określając kwerendy w postaci `{call "<report name>"}`, na przykład. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Przywracanie usuniętych rekordów z Kosza usług Salesforce
Kwerenda nietrwałego usuniętych rekordów z Kosza usług Salesforce, można określić **"IsDeleted = 1"** w zapytaniu. Na przykład:

* Aby odpytać tylko usunięte rekordy, określ "Wybierz * z MyTable__c **gdzie IsDeleted = 1**"
* Aby wysyłać zapytania o wszystkie rekordy tym usuniętych i istniejących, określ "Wybierz * z MyTable__c **gdzie IsDeleted = 0 lub IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Przykład JSON: kopiowanie danych z usług Salesforce do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych z usług Salesforce do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.   

Poniżej przedstawiono artefakty fabryki danych, które należy utworzyć w celu zaimplementowania scenariusza. Sekcje listy zawierają szczegółowe informacje na temat tych kroków.

* Połączonej usługi typu [Salesforce](#linked-service-properties)
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Usługa SalesForce połączone**

W tym przykładzie użyto **Salesforce** połączonej usługi. Zobacz [Salesforce połączona usługa](#linked-service-properties) sekcję właściwości, które są obsługiwane przez tej połączonej usługi.  Zobacz [uzyskać token zabezpieczeń](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje dotyczące resetowania/Get tokenu zabezpieczającego.

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
**Wejściowy zestaw danych usług SalesForce**

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

Ustawienie **zewnętrznych** do **true** usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnych niestandardowych obiektów.

![Nazwa danych fabryki — połączenie usługi Salesforce — interfejs API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1).

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

**Potok wraz z działanie kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource**i **zbiornika** ustawiono typ **BlobSink**.

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
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnych niestandardowych obiektów.

![Nazwa danych fabryki — połączenie usługi Salesforce — interfejs API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapowanie typu dla usług Salesforce
| Typ Salesforce | . Typ opartej na sieci |
| --- | --- |
| Automatyczny numer |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |O podwójnej precyzji |
| Date |DateTime |
| Data/godzina |DateTime |
| Email |Ciąg |
| Identyfikator |Ciąg |
| Relacja wyszukiwania |Ciąg |
| Lista wyboru wielokrotnego wyboru |Ciąg |
| Liczba |O podwójnej precyzji |
| Procent |O podwójnej precyzji |
| Numer telefonu |Ciąg |
| Lista wyboru |Ciąg |
| Tekst |Ciąg |
| Obszar tekstu |Ciąg |
| Obszar tekstu (Liczba długa) |Ciąg |
| Obszar tekstu (zaawansowana) |Ciąg |
| Tekst (zaszyfrowane) |Ciąg |
| Adres URL |Ciąg |

> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
