---
title: Przenoszenie danych z salesforce przy użyciu fabryki danych
description: Dowiedz się, jak przenosić dane z salesforce przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281134"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Przenoszenie danych z salesforce przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-salesforce-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-salesforce.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik Salesforce w wersji 2](../connector-salesforce.md).

W tym artykule opisano, jak można użyć działania kopiowania w fabryce danych platformy Azure do kopiowania danych z salesforce do dowolnego magazynu danych, który jest wymieniony w obszarze Sink kolumny w [obsługiwanych źródeł i sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. W tym artykule opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z copy activity i obsługiwanych kombinacji magazynu danych.

Usługa Azure Data Factory obsługuje obecnie tylko przenoszenie danych z Salesforce do [obsługiwanych magazynów danych ujścia,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)ale nie obsługuje przenoszenia danych z innych magazynów danych do salesforce.

## <a name="supported-versions"></a>Obsługiwane wersje
Ten łącznik obsługuje następujące wersje salesforce: Developer Edition, Professional Edition, Enterprise Edition lub Unlimited Edition. Obsługuje również kopiowanie z produkcji Salesforce, piaskownicy i domeny niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne
* Uprawnienie interfejsu API musi być włączone. Zobacz [Jak włączyć dostęp do interfejsu API w Salesforce według zestawu uprawnień?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Aby skopiować dane z salesforce do lokalnych magazynów danych, musisz mieć co najmniej bramę zarządzania danymi 2.0 zainstalowaną w środowisku lokalnym.

## <a name="salesforce-request-limits"></a>Limity żądań Salesforce
Salesforce ma limity zarówno dla wszystkich żądań interfejsu API, jak i równoczesnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba równoczesnych żądań przekracza limit, występuje ograniczanie przepustowości i zostaną wyświetlona losowe błędy.
- Jeśli łączna liczba żądań przekroczy limit, konto Salesforce zostanie zablokowane na 24 godziny.

Może również pojawić się błąd "REQUEST_LIMIT_EXCEEDED" w obu scenariuszach. Szczegółowe informacje można znaleźć w sekcji "Limity żądań interfejsu API" w artykule [Limity dla deweloperów Salesforce.](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane z Salesforce przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z salesforce, zobacz [przykład JSON: Kopiowanie danych z salesforce do usługi Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych specyficznych dla Salesforce:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi połączonej Salesforce.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **Salesforce**. |Tak |
| środowiskoUrl | Określ adres URL instancji Salesforce. <br><br> - Domyślnie jest\/to "https: /login.salesforce.com". <br> - Aby skopiować dane zhttps://test.salesforce.compiaskownicy, należy określić " ". <br> - Aby skopiować dane z domeny niestandardowej, należy na przykład określić "https://[domain].my.salesforce.com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| Securitytoken |Określ token zabezpieczający dla konta użytkownika. Zobacz [Pobierz token zabezpieczający,](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) aby uzyskać instrukcje dotyczące resetowania/uzyskania tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokenach zabezpieczających, zobacz [Zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure i tak dalej).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **RelationalTable** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w salesforce. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

> [!IMPORTANT]
> "__c" część nazwy interfejsu API jest potrzebna dla dowolnego obiektu niestandardowego.

![Fabryka danych — połączenie Salesforce — nazwa interfejsu API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz różne zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania, z drugiej strony, różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

W działaniu kopiowania, gdy źródłem jest typ **RelationalSource** (który obejmuje Salesforce), następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Kwerenda SQL-92 lub [kwerenda języka soql (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Na przykład: `select * from MyTable__c`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

> [!IMPORTANT]
> "__c" część nazwy interfejsu API jest potrzebna dla dowolnego obiektu niestandardowego.

![Fabryka danych — połączenie Salesforce — nazwa interfejsu API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Porady dotyczące zapytań
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Pobieranie danych przy użyciu klauzuli where w kolumnie DateTime
Podczas określania zapytania SOQL lub SQL należy zwrócić uwagę na różnicę formatu DateTime. Przykład:

* **Próbka SOKL:**`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Przykład SQL:**
    * **Określenie kwerendy za pomocą kreatora kopiowania:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Za pomocą edycji JSON, aby określić kwerendę (znak ucieczki poprawnie):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Pobieranie danych z raportu Salesforce
Dane z raportów Salesforce można pobierać, `{call "<report name>"}`określając kwerendę jako ,na przykład.You can retrieve data from Salesforce reports by specifying query as ,for example,. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Pobieranie usuniętych rekordów z Kosza Salesforce
Aby zbadać nietrwałe usunięte rekordy z Kosza Salesforce, można określić **"IsDeleted = 1"** w zapytaniu. Na przykład:

* Aby zbadać tylko usunięte rekordy, należy określić opcję "wybierz * z MyTable__c **gdzie jest usuwany= 1**"
* Aby zbadać wszystkie rekordy, w tym istniejące i usunięte, określ "wybierz * z MyTable__c **gdzie IsDeleted = 0 lub IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z salesforce do obiektu Blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane z Salesforce do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

Oto artefakty fabryki danych, które należy utworzyć w celu zaimplementowania scenariusza. Sekcje, które następują po liście zawierają szczegółowe informacje na temat tych kroków.

* Połączona usługa typu [Salesforce](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [relationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Usługa połączona Salesforce**

W tym przykładzie użyto usługi połączonej **Salesforce.** Zobacz sekcję [usługi połączonej Salesforce](#linked-service-properties) dla właściwości, które są obsługiwane przez tę usługę połączone. Zobacz [Pobierz token zabezpieczający,](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) aby uzyskać instrukcje dotyczące resetowania/ponajmowania tokenu zabezpieczającego.

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
**Usługa połączona usługi Usługi Azure Storage**

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
**Zestaw danych wejściowych Salesforce**

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

Ustawienie na **wartość true** **z zewnątrz** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

> [!IMPORTANT]
> "__c" część nazwy interfejsu API jest potrzebna dla dowolnego obiektu niestandardowego.

![Fabryka danych — połączenie Salesforce — nazwa interfejsu API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Zestaw danych wyjściowych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1).

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

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource,** a typ **ujścia** jest ustawiony na **BlobSink**.

Zobacz [Właściwości typu RelationalSource](#copy-activity-properties) dla listy właściwości, które są obsługiwane przez RelationalSource.

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
> "__c" część nazwy interfejsu API jest potrzebna dla dowolnego obiektu niestandardowego.

![Fabryka danych — połączenie Salesforce — nazwa interfejsu API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapowanie typów dla Salesforce

| Typ Salesforce | . Typ oparty na sieci |
| --- | --- |
| Automatyczny numer |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |Wartość dziesiętna |
| Data |DateTime |
| Data/godzina |DateTime |
| Adres e-mail |Ciąg |
| Identyfikator |Ciąg |
| Relacja odnośnika |Ciąg |
| Lista wyboru wielokrotnego wyboru |Ciąg |
| Liczba |Wartość dziesiętna |
| Wartość procentowa |Wartość dziesiętna |
| Telefon |Ciąg |
| Lista wyboru |Ciąg |
| Tekst |Ciąg |
| Obszar tekstu |Ciąg |
| Obszar tekstu (długi) |Ciąg |
| Obszar tekstu (bogaty) |Ciąg |
| Tekst (zaszyfrowany) |Ciąg |
| Adres URL |Ciąg |

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [przewodnik wydajności i dostrajania działania kopiowania,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby jej optymalizacji.
