---
title: Przekształcanie danych przy użyciu skryptu U-SQL — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposób przetwarzania lub przekształcać dane, uruchamiając skrypty U-SQL na usługi obliczeniowej Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 5835c37363c7e9d2dd3253c08ab97f17852725f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248151"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych przez uruchamianie skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-usql-activity.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [działanie U-SQL w wersji 2](../transform-data-using-data-lake-analytics.md).

Potok w fabryce danych Azure przetwarza danych w usługach połączonego magazynu za pomocą usług obliczeniowych połączone. Zawiera ona sekwencja działań, w którym każde działanie wykonuje operację przetwarzania specyficznego. W tym artykule opisano **działania języka U-SQL usługi Data Lake Analytics** , które jest uruchamiane **U-SQL** skryptom na **Azure Data Lake Analytics** obliczeniową usługę połączoną. 

Przed utworzeniem potoku za pomocą działania języka U-SQL usługi Data Lake Analytics, należy utworzyć konto usługi Azure Data Lake Analytics. Aby dowiedzieć się więcej na temat usługi Azure Data Lake Analytics, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Przegląd [Tworzenie pierwszego potoku samouczek](data-factory-build-your-first-pipeline.md) szczegółowe instrukcje dotyczące tworzenia fabryki danych, połączonej usługi, zestawy danych i potoku. Użyj fragmentów kodu JSON przy użyciu edytora fabryki danych lub Visual Studio lub programu Azure PowerShell do utworzenia jednostki usługi Data Factory.

## <a name="supported-authentication-types"></a>Typy obsługiwane uwierzytelnianie
Działanie U-SQL obsługuje poniższe typy uwierzytelniania względem usługi Data Lake Analytics:
* Uwierzytelnianie jednostki usługi
* Uwierzytelniania poświadczeń (OAuth) użytkownika 

Zaleca się, że używasz uwierzytelniania jednostki usługi, szczególnie w przypadku zaplanowanego wykonania U-SQL. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Szczegółowe informacje dotyczące konfiguracji, zobacz [właściwości usługi połączonej](#azure-data-lake-analytics-linked-service) sekcji.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics Linked Service
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługi Azure Data Lake Analytics obliczeń Usługa do usługi Azure data factory. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

Poniższa tabela zawiera opisy ogólne właściwości używane w definicji JSON. Dodatkowo można wybrać nazwy głównej usługi i uwierzytelnienia poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Właściwość type powinna być równa: **AzureDataLakeAnalytics**. |Yes |
| **accountName** |Nazwa konta usługi Azure Data Lake Analytics. |Yes |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |Nie |
| **subscriptionId** |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określony, używany subskrypcji usługi data factory). |
| **resourceGroupName** |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie zostanie określony, używany grupy zasobów usługi data factory). |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), a następnie przyznać jej dostęp do programu Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania jednostki usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Yes |
| **servicePrincipalKey** | Określ klucz aplikacji. | Yes |
| **dzierżawy** | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Yes |

**Przykład: Uwierzytelnianie jednostki usługi**
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

### <a name="user-credential-authentication"></a>Uwierzytelnienia poświadczeń użytkownika
Alternatywnie można użyć uwierzytelniania poświadczeń użytkownika usługi Data Lake Analytics, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **Autoryzacja** | Kliknij przycisk **Autoryzuj** znajdujący się w edytorze fabryki danych i wprowadź swoje poświadczenia, które przypisuje adres URL autoryzacji wygenerowany automatycznie do tej właściwości. | Yes |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowany automatycznie, korzystając z edytora fabryki danych. | Yes |

**Przykład: Uwierzytelnienia poświadczeń użytkownika**
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

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji wygenerowany przy użyciu **Autoryzuj** przycisk wygasa po upływie pewnego czasu. Zobacz poniższą tabelę, do czasu wygaśnięcia dla różnych typów kont użytkowników. Może zostać wyświetlony następujący błąd komunikatu podczas uwierzytelniania **wygaśnięcia ważności tokenu**: Błąd operacji dotyczącej poświadczeń: invalid_grant - AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS70008: Udzielenie dostępu podany jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników, które nie są zarządzane przez usługę Azure Active Directory (@hotmail.com, @live.comitp.) |12 godzin |
| Konta użytkowników zarządzanych przez usługę Azure Active Directory (AAD) |Uruchom 14 dni od ostatniego wycinka. <br/><br/>90 dni, jeśli wycinek oparte na podstawie OAuth połączonej usługi działa co najmniej raz na 14 dni. |

Aby uniknąć/rozwiązania tego błędu, ponownie autoryzować przy użyciu **autoryzacji** przycisk, kiedy **wygaśnięcia ważności tokenu** i ponownie wdrożyć połączoną usługę. Możesz również generować wartości **sessionId** i **autoryzacji** właściwości programowo przy użyciu kodu w następujący sposób:

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

Zobacz [klasy AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [klasy AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), i [klasy AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematy, aby uzyskać szczegółowe informacje o klasach usługi Data Factory używane w kodzie. Dodaj odwołanie do: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll for the WindowsFormsWebAuthenticationDialog class. 

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok za pomocą działania języka U-SQL usługi Data Lake Analytics. Definicja aktywności zawiera odwołanie do usługi połączonej usługi Azure Data Lake Analytics, która została utworzona wcześniej.   

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
| type                | Właściwość type musi być równa **DataLakeAnalyticsU SQL**. | Yes                                      |
| linkedServiceName   | Odwołanie do usługi Azure Data Lake Analytics zarejestrowany jako połączonej usługi w usłudze Data Factory | Yes                                      |
| scriptPath          | Ścieżka do folderu zawierającego skrypt U-SQL. Nazwa pliku jest uwzględniana wielkość liter. | Nie (Jeśli używany jest skrypt)                   |
| scriptLinkedService | Połączonej usługi, która łączy magazyn, który zawiera skrypt w usłudze data factory | Nie (Jeśli używany jest skrypt)                   |
| skrypt              | Określ zamiast określania scriptPath i element scriptLinkedService wykonanie wbudowanego skryptu. Na przykład: `"script": "CREATE DATABASE test"`. | Nie (Jeśli używasz scriptPath i element scriptLinkedService) |
| degreeOfParallelism | Maksymalna liczba węzłów równocześnie używane do uruchamiania zadania. | Nie                                       |
| priority            | Określa, które spośród wszystkich, które są umieszczane w kolejce zadań, należy wybrać do uruchomienia jako pierwsza. Im mniejsza liczba, tym wyższy priorytet. | Nie                                       |
| parameters          | Parametry skryptu U-SQL          | Nie                                       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparatu U-SQL do użycia | Nie                                       |
| compilationMode     | <p>Tryb kompilacji języka U-SQL. Musi być jedną z następujących wartości:</p> <ul><li>**Semantyczne:** Należy wykonać tylko semantycznego testy i wykonuje niezbędne testów.</li><li>**Pełna:** Wykonywanie pełnej kompilacji, w tym sprawdzanie składni, optymalizacja, generowanie kodu itp.</li><li>**SingleBox:** Wykonywanie pełnej kompilacji, za pomocą ustawienia TargetType SingleBox.</li></ul><p>Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb optymalne kompilacji. </p> | Nie                                       |

Zobacz [definicji skryptu SearchLogProcessing.txt](#sample-u-sql-script) definicji skryptu. 

## <a name="sample-input-and-output-datasets"></a>Przykładowe dane wejściowe i wyjściowe zestawy danych
### <a name="input-dataset"></a>Wejściowy zestaw danych
W tym przykładzie dane wejściowe znajdują się w usługi Azure Data Lake Store (plik SearchLog.tsv plik w folderze datalake/input). 

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
W tym przykładzie dane wyjściowe generowane przy użyciu skryptu U-SQL znajduje się w Store jezioro danych Azure (datalake/output folder). 

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
Oto definicja przykładowego usługi Azure Data Lake Store połączonej usługi używana na potrzeby dane wejściowe/wyjściowe zestawy danych. 

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

Zobacz [przenoszenie danych do i z usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md) artykuł dotyczący opisy właściwości JSON. 

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

Wartości  **\@w** i  **\@się** parametry w skrypcie U-SQL są przekazywane dynamicznie przez usługę ADF zgodnie z sekcją "parameters". Zobacz sekcję "parameters" w definicji potoku.

Również inne właściwości, takie jak degreeOfParallelism i priorytet można określić w definicji potoku dla zadań, które są uruchamiane w usłudze Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametry dynamiczne
W definicja przykładowego potoku i pomniejszać parametry są przypisywane przy użyciu zakodowanych wartości. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Jest to możliwe, zamiast tego użyć parametrów dynamicznych. Na przykład: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

W takim przypadku pliki wejściowe nadal są pobierane z folderu /datalake/input i pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne, na podstawie czasu uruchomienia wycinka.  


