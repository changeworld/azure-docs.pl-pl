---
title: Przekształcanie danych za pomocą skryptu U-SQL - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposobu przetwarzania lub Przekształcanie danych za pomocą skryptów U-SQL w usłudze obliczeniowych Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a0f4a823d9a484dcae35a2741bf7904eb17a1502
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622857"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych za pomocą skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-usql-activity.md)
> * [Wersja 2 — wersja zapoznawcza](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [działanie U-SQL w wersji 2](../transform-data-using-data-lake-analytics.md).

Potok w fabryce danych Azure przetwarza dane w usługach magazynu połączone, przy użyciu obliczeniowego połączonej usługi. Zawiera sekwencję działań, gdzie każde działanie wykonuje operację przetwarzania specyficznego dla. W tym artykule opisano **Data Lake Analytics U-SQL działania** , na którym działa **U-SQL** skryptom na **Azure Data Lake Analytics** obliczeniowe połączonej usługi. 

Przed utworzeniem potoku z działaniem Data Lake Analytics U-SQL, należy utworzyć konto usługi Azure Data Lake Analytics. Aby zapoznać się z usługą Azure Data Lake Analytics, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Przegląd [kompilacji pierwszy samouczek potoku](data-factory-build-your-first-pipeline.md) szczegółowy opis kroków można utworzyć fabryki danych, połączone usługi, zestawy danych i potoku. Za pomocą fragmenty kodu JSON Edytor fabryki danych lub Visual Studio lub Azure PowerShell do tworzenia jednostek fabryki danych.

## <a name="supported-authentication-types"></a>Typy obsługiwane uwierzytelniania
Działanie U-SQL obsługuje poniżej typy uwierzytelniania względem usługi Data Lake Analytics:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie użytkownika poświadczeń (OAuth) 

Firma Microsoft zaleca korzystanie z uwierzytelniania głównej usługi, zwłaszcza w przypadku zaplanowane wykonanie U-SQL. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Szczegółowe informacje dotyczące konfiguracji, zobacz [połączona usługa właściwości](#azure-data-lake-analytics-linked-service) sekcji.

## <a name="azure-data-lake-analytics-linked-service"></a>Usługi Azure Data Lake Analytics połączona usługa
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć z usługą Azure Data Lake Analytics obliczeniowe usługi fabryka danych Azure. Data Lake Analytics U-SQL działania w potoku odwołuje się do tej połączonej usługi. 

Poniższa tabela zawiera opisy ogólne właściwości używane w definicji JSON. Dodatkowo można wybrać nazwy głównej usługi i uwierzytelnianie poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Powinien mieć ustawioną właściwość type: **AzureDataLakeAnalytics**. |Yes |
| **accountName** |Nazwa konta usługi Azure Data Lake Analytics. |Yes |
| **dataLakeAnalyticsUri** |Identyfikator URI, usługi Azure Data Lake Analytics. |Nie |
| **Identyfikator subskrypcji** |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określony, używany subskrypcji fabryki danych). |
| **resourceGroupName** |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie zostanie określony, używana grupa zasobów z fabryką danych). |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie główna usługi (zalecane)
Aby używać uwierzytelniania głównej usługi, Zarejestruj podmiot aplikacji w usłudze Azure Active Directory (Azure AD) i przyznać jej dostęp do usługi Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [do usługi uwierzytelniania](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Uwierzytelnianie usługi głównej przez określenie następujących właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator aplikacji klienta. | Yes |
| **servicePrincipalKey** | Określ klucz aplikacji. | Yes |
| **Dzierżawy** | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Yes |

**Przykład: Usługa podmiotu zabezpieczeń uwierzytelniania**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
Alternatywnie można uwierzytelnienia poświadczeń użytkownika dla usługi Data Lake Analytics przez określenie następujących właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **Autoryzacji** | Kliknij przycisk **autoryzacji** przycisk Edytor fabryki danych i wprowadź Twoje poświadczenia, który przypisuje do tej właściwości adresu URL autoryzacji wygenerowana automatycznie. | Yes |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowane automatycznie, gdy używasz Edytor fabryki danych. | Yes |

**Przykład: Użytkownik poświadczeń uwierzytelniania**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcia tokenu
Kod autoryzacji wygenerowanych przy użyciu **autoryzacji** przycisk wygaśnie po upływie pewnego czasu. Czas wygaśnięcia dla różnych typów kont użytkowników znajduje się w tabeli poniżej. Może zostać wyświetlony następujący błąd komunikatu podczas uwierzytelniania **wygaśnięcia tokenu**: poświadczeń błąd operacji: invalid_grant - AADSTS70002: błąd podczas sprawdzania poprawności poświadczeń. AADSTS70008: Udzielone prawa dostępu jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników, które nie są zarządzane przez usługę Azure Active Directory (@hotmail.com, @live.comitp.) |12 godzin |
| Konta użytkowników zarządzanych przez usługi Azure Active Directory (AAD) |Uruchom 14 dni od ostatniego wycinka. <br/><br/>90 dni, jeśli wycinek oparte na podstawie OAuth połączonej usługi jest uruchamiana co najmniej raz na 14 dni. |

Aby uniknąć/Rozwiąż ten błąd, ponownie autoryzować przy użyciu **autoryzacji** przycisku, gdy **wygaśnięcia tokenu** i wdrożenie połączonej usługi. Można również tworzyć wartości **sessionId** i **autoryzacji** właściwości programowo przy użyciu kodu w następujący sposób:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Zobacz [klasy AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [klasy AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), i [klasy AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematy, aby uzyskać więcej informacji o klasach fabryki danych używana w kodzie. Dodaj odwołanie do: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla klasy WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potoku z działaniem Data Lake Analytics U-SQL. Definicji działania zawiera odwołanie do usługi Azure Data Lake Analytics połączone utworzony wcześniej.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania. 

| Właściwość            | Opis                              | Wymagane                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | Właściwość type musi mieć ustawioną **DataLakeAnalyticsU SQL**. | Yes                                      |
| linkedServiceName   | Odwołanie do usługi Azure Data Lake Analytics zarejestrowany jako połączonej usługi z fabryki danych | Yes                                      |
| scriptPath          | Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. | Nie (Jeśli używasz skryptu)                   |
| scriptLinkedService | Połączonej usługi, która łączy magazynu, który zawiera skrypt do fabryki danych | Nie (Jeśli używasz skryptu)                   |
| Skrypt              | Określ skrypt wbudowany zamiast określania scriptPath i scriptLinkedService. Na przykład: `"script": "CREATE DATABASE test"`. | Nie (Jeśli używasz scriptPath i scriptLinkedService) |
| degreeOfParallelism | Maksymalna liczba węzłów jednocześnie użyta do uruchomienia zadania. | Nie                                       |
| priorytet            | Określa, które spośród wszystkich znajdujących się w kolejce zadań należy wybrać ma być uruchomiony. Im niższy numer, tym wyższy priorytet. | Nie                                       |
| parameters          | Parametry skryptu U-SQL          | Nie                                       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparatu U-SQL do użycia | Nie                                       |
| właściwość compilationMode     | <p>Tryb kompilacji U-SQL. Musi być jedną z następujących wartości:</p> <ul><li>**Semantycznej:** wykonywać tylko semantycznego kontroli i potrzeby związane z poprawnością kontroli.</li><li>**Pełna:** wykonania pełnej kompilacji, takich jak sprawdzanie składni, optymalizacja, generowania kodu, itp.</li><li>**SingleBox:** wykonania pełnej kompilacji, z ustawieniem TargetType do SingleBox.</li></ul><p>Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb optymalne kompilacji. </p> | Nie                                       |

Zobacz [definicji skryptu SearchLogProcessing.txt](#sample-u-sql-script) definicji skryptu. 

## <a name="sample-input-and-output-datasets"></a>Przykładowe dane wejściowe i wyjściowe zestawy danych
### <a name="input-dataset"></a>Wejściowy zestaw danych
W tym przykładzie dane wejściowe znajduje się w usłudze Azure Data Lake Store (plik SearchLog.tsv plik w folderze datalake/wprowadzania). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Wyjściowy zestaw danych
W tym przykładzie danych wyjściowych generowanych przez skrypt U-SQL jest przechowywane w usłudze Azure Data Lake Store (datalake/wyjścia folder). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Przykładowe Data Lake Store połączona usługa
W tym miejscu znajduje się definicja próbki Azure Data Lake Store połączonej usługi używana przez zestaw danych wejścia/wyjścia. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Zobacz [przenoszenie danych do i z usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md) artykułu Opis właściwości JSON. 

## <a name="sample-u-sql-script"></a>Przykładowy skrypt U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Wartości **@in** i **@out** Parametry skryptu U-SQL są przekazywane dynamicznie przez ADF zgodnie z sekcją "parameters". Zobacz sekcję "parameters" w definicji potoku.

Inne właściwości, takie jak degreeOfParallelism i priorytet można określić również w definicji potoku dla zadań, które są uruchamiane w usłudze Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametry dynamiczne
W definicji potoku próbki i wylogowywanie parametry są przypisywane z zakodowanych wartości. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Istnieje możliwość zamiast tego użyj parametrów dynamicznych. Na przykład: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

W takim przypadku pliki wejściowe nadal są pobierane z folderu /datalake/input i pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia wycinka.  


