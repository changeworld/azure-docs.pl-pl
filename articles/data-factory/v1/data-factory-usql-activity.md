---
title: Przekształcanie danych przy użyciu skryptu U-SQL — Azure
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając skrypty U-SQL w usłudze obliczeniowej usługi Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927900"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych przez uruchamianie skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-usql-activity.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Działanie U-SQL w wersji 2](../transform-data-using-data-lake-analytics.md).

Potok w fabryce danych platformy Azure przetwarza dane w połączonych usługach magazynu przy użyciu połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano **działanie U-SQL analizy usługi Data Lake Analytics,** która uruchamia skrypt **U-SQL** w połączonej usłudze obliczeniowej **usługi Azure Data Lake Analytics.** 

Utwórz konto usługi Azure Data Lake Analytics przed utworzeniem potoku z aktywność U-SQL analizy usługi Data Lake Analytics. Aby dowiedzieć się więcej o usłudze Azure Data Lake Analytics, zobacz [Wprowadzenie do usługi Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Przejrzyj [samouczek Tworzenie pierwszego potoku,](data-factory-build-your-first-pipeline.md) aby uzyskać szczegółowe kroki w celu utworzenia fabryki danych, połączonych usług, zestawów danych i potoku. Użyj fragmentów JSON z edytorem fabryki danych lub programem Visual Studio lub programem Azure PowerShell, aby utworzyć jednostki usługi Data Factory.

## <a name="supported-authentication-types"></a>Obsługiwane typy uwierzytelniania
Działanie U-SQL obsługuje poniższe typy uwierzytelniania w usłudze Data Lake Analytics:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie poświadczeń użytkownika (OAuth) 

Zaleca się użycie uwierzytelniania jednostkowego usługi, szczególnie w przypadku zaplanowanego wykonania języka U-SQL. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Aby uzyskać szczegółowe informacje o konfiguracji, zobacz sekcję [Połączone właściwości usługi.](#azure-data-lake-analytics-linked-service)

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa połączona usługi Usługi usługi Azure Data Lake Analytics
Utwórz usługę połączony **usługi Usługi Azure Data Lake Analytics,** aby połączyć usługę obliczeniową usługi Azure Data Lake Analytics z fabryką danych platformy Azure. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

Poniższa tabela zawiera opisy właściwości ogólnych używanych w definicji JSON. Można dodatkowo wybrać między jednostką usługi a uwierzytelnianiem poświadczeń użytkownika.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **Typu** |Właściwość typu powinna być ustawiona na: **AzureDataLakeAnalytics**. |Tak |
| **Accountname** |Nazwa konta usługi Azure Data Lake Analytics. |Tak |
| **dataLakeAnalyticsUri** |Identyfikator URI usługi Azure Data Lake Analytics. |Nie |
| **Subscriptionid** |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określona, używana jest subskrypcja fabryki danych). |
| **nazwa grupy zasobów** |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie określono, używana jest grupa zasobów fabryki danych). |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostkowe usługi (zalecane)
Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD) i udziel jej dostępu do magazynu Usługi Data Lake. Aby uzyskać szczegółowe kroki, zobacz [Uwierzytelnianie usługi do usługi](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania głównego usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Tak |
| **servicePrincipalKey** | Określ klucz aplikacji. | Tak |
| **Dzierżawy** | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |

**Przykład: uwierzytelnianie jednostki usługi**
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
Alternatywnie można użyć uwierzytelniania poświadczeń użytkownika dla usługi Data Lake Analytics, określając następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **Autoryzacji** | Kliknij przycisk **Autoryzuj** w Edytorze fabryki danych i wprowadź poświadczenia, które przypisuje do tej właściwości automatyczniegenerowany adres URL autoryzacji. | Tak |
| **Sessionid** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie podczas korzystania z Edytora fabryki danych. | Tak |

**Przykład: uwierzytelnianie poświadczeń użytkownika**
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
Kod autoryzacji wygenerowany za pomocą przycisku **Autoryzuj** wygasa po pewnym czasie. Zobacz poniższą tabelę dla czasów wygaśnięcia dla różnych typów kont użytkowników. Po **wygaśnięciu tokenu**uwierzytelniania może zostać wyświetlony następujący komunikat o błędzie : Błąd operacji poświadczeń: invalid_grant — AADSTS70002: Error validating credentials. AADSTS70008: Udzielona dotacja dostępu wygasła lub została odwołana. Identyfikator śledzenia: d18629e8-af88-43c5-88e3-d8419eb1fca1 Identyfikator korelacji: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Sygnatura czasowa: 2015-12-15 21:09:31Z

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników NIE zarządzane przez@hotmail.comusługę @live.comAzure Active Directory ( , itp.) |12 godz. |
| Konta użytkowników zarządzane przez usługę Azure Active Directory (AAD) |14 dni po ostatnim uruchomieniu plasterka. <br/><br/>90 dni, jeśli plasterek oparty na połączonej usłudze opartej na UAuth działa co najmniej raz na 14 dni. |

Aby uniknąć/rozwiązać ten błąd, ponownie autoryzuj za pomocą przycisku **Autoryzuj** po **wygaśnięciu tokenu** i ponownie wdrożyć usługę połączoną. Można również wygenerować wartości dla **sessionId** i **autoryzacji** właściwości programowo przy użyciu kodu w następujący sposób:

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

Zobacz [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)i [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematy szczegółowe informacje na temat klasy fabryki danych używane w kodzie. Dodaj odwołanie do: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla klasy WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok z działaniem U-SQL analizy usługi Data Lake Analytics. Definicja działania ma odwołanie do usługi połączonej usługi Usługi Azure Data Lake Analytics utworzonej wcześniej.   

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

| Właściwość            | Opis                              | Wymagany                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | Właściwość typu musi być ustawiona na **DataLakeAnalyticsU-SQL**. | Tak                                      |
| linkedServiceName   | Odwołanie do usługi Azure Data Lake Analytics zarejestrowanej jako usługa połączona w fabryce danych | Tak                                      |
| scriptPath          | Ścieżka do folderu zawierającego skrypt U-SQL. W nazwie pliku jest rozróżniana wielkość liter. | Nie (jeśli używasz skryptu)                   |
| scriptLinkedService | Połączona usługa łącząca magazyn zawierający skrypt z fabryką danych | Nie (jeśli używasz skryptu)                   |
| skrypt              | Określ skrypt wbudowany zamiast określania scriptPath i scriptLinkedService. Na przykład: `"script": "CREATE DATABASE test"`. | Nie (jeśli używasz scriptPath i scriptLinkedService) |
| stopieńParalelizm | Maksymalna liczba węzłów jednocześnie używanych do uruchamiania zadania. | Nie                                       |
| priority            | Określa, które zadania ze wszystkich, które są w kolejce, powinny być wybrane do uruchomienia jako pierwsze. Im mniejsza liczba, tym wyższy priorytet. | Nie                                       |
| parameters          | Parametry skryptu U-SQL          | Nie                                       |
| wersja runtimeVersion      | Wersja runtime aparatu U-SQL do użycia | Nie                                       |
| kompilacjaMode     | <p>Tryb kompilacji U-SQL. Musi być jedną z następujących wartości:</p> <ul><li>**Semantyczne:** Przeprowadzaj tylko kontrole semantyczne i niezbędne kontrole poczytalności.</li><li>**Pełna wersja:** Wykonaj pełną kompilację, w tym sprawdzanie składni, optymalizację, generowanie kodu itp.</li><li>**SingleBox:** Wykonaj pełną kompilację, z ustawieniem TargetType na SingleBox.</li></ul><p>Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb kompilacji optymalne. </p> | Nie                                       |

Definicję skryptu można znaleźć w pliku [SearchLogProcessing.txt Script Definition.](#sample-u-sql-script) 

## <a name="sample-input-and-output-datasets"></a>Przykładowe zestawy danych wejściowych i wyjściowych
### <a name="input-dataset"></a>Wejściowy zestaw danych
W tym przykładzie dane wejściowe znajdują się w magazynie usługi Azure Data Lake (plik SearchLog.tsv w folderze datalake/input). 

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
W tym przykładzie dane wyjściowe produkowane przez skrypt U-SQL są przechowywane w magazynie usługi Azure Data Lake Store (folderzie datalake/output). 

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

### <a name="sample-data-lake-store-linked-service"></a>Przykładowa usługa połączona z usługą Data Lake Store
Oto definicja przykładowej usługi połączonej usługi Usługi Azure Data Lake Store używanej przez zestawy danych wejściowych/wyjściowych. 

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

Opisy właściwości usługi JSON można znaleźć w artykule Przenoszenie [danych do i z usługi Azure Data Lake Store.](data-factory-azure-datalake-connector.md) 

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

Wartości parametrów ** \@** ** \@in** i out w skrypcie U-SQL są przekazywane dynamicznie przez podajnik ADF przy użyciu sekcji "parametry". Zobacz sekcję "parametry" w definicji potoku.

Można określić inne właściwości, takie jak stopieńParallelism i priorytet, a także w definicji potoku dla zadań, które są uruchamiane w usłudze Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametry dynamiczne
W definicji potoku próbki parametry in i out są przypisywane z wartościami zakodowanych na czas. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Zamiast tego można użyć parametrów dynamicznych. Przykład: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

W takim przypadku pliki wejściowe są nadal pobierane z folderu /datalake/input, a pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia plasterka.  


