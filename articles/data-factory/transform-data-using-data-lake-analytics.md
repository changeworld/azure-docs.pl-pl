---
title: Przekształcanie danych przy użyciu skryptu U-SQL — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposób przetwarzania lub przekształcać dane, uruchamiając skrypty U-SQL na usługi obliczeniowej Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: d5b074fcf182bcc9bf4dc17ba21215d27e13cbdd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888439"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych przez uruchamianie skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-usql-activity.md)
> * [Bieżąca wersja](transform-data-using-data-lake-analytics.md)

Potok w fabryce danych Azure przetwarza danych w usługach połączonego magazynu za pomocą usług obliczeniowych połączone. Zawiera ona sekwencja działań, w którym każde działanie wykonuje operację przetwarzania specyficznego. W tym artykule opisano **działania języka U-SQL usługi Data Lake Analytics** , które jest uruchamiane **U-SQL** skryptom na **Azure Data Lake Analytics** obliczeniową usługę połączoną. 

Przed utworzeniem potoku za pomocą działania języka U-SQL usługi Data Lake Analytics, należy utworzyć konto usługi Azure Data Lake Analytics. Aby dowiedzieć się więcej na temat usługi Azure Data Lake Analytics, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics, połączone usługi
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługi Azure Data Lake Analytics obliczeń Usługa do usługi Azure data factory. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

Poniższa tabela zawiera opisy ogólne właściwości używane w definicji JSON. 

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Właściwość type powinna być równa: **AzureDataLakeAnalytics**. | Tak                                      |
| **accountName**          | Nazwa konta usługi Azure Data Lake Analytics.  | Yes                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | Nie                                       |
| **subscriptionId**       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| **resourceGroupName**    | Nazwa grupy zasobów platformy Azure                | Nie                                       |

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi
Usługę połączoną usługi Azure Data Lake Analytics wymaga uwierzytelniania jednostki usługi, aby nawiązać połączenie z usługą Azure Data Lake Analytics. Aby użyć uwierzytelniania jednostki usługi, zarejestrować jednostki aplikacji w usłudze Azure Active Directory (Azure AD), a następnie przyznać jej dostęp do usługi Data Lake Analytics i Data Lake Store, używa. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Przyznaj uprawnienia podmiotu zabezpieczeń usługi za pomocą usługi Azure Data Lake Anatlyics [Kreatora dodawania użytkownika](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Użyj uwierzytelniania jednostki usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **dzierżawy**              | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Tak      |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Aby dowiedzieć się więcej na temat połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok za pomocą działania języka U-SQL usługi Data Lake Analytics. Definicja aktywności zawiera odwołanie do usługi połączonej usługi Azure Data Lake Analytics, która została utworzona wcześniej. Do uruchomienia skryptu U-SQL usługi Data Lake Analytics, Data Factory przesyła skrypt, który określono, aby usługa Data Lake Analytics i wymagane dane wejściowe i wyjściowe jest zdefiniowana w skrypcie usługi Data Lake Analytics pobrać i dane wyjściowe. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania. 

| Właściwość            | Opis                              | Wymagane |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nazwa działania w potoku     | Tak      |
| description         | Tekst opisujący, co działanie robi.  | Nie       |
| type                | Działanie U-SQL usługi Data Lake Analytics jest typ działania **DataLakeAnalyticsU SQL**. | Tak      |
| linkedServiceName   | Połączoną usługę służącą do usługi Azure Data Lake Analytics. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.  |Tak       |
| scriptPath          | Ścieżka do folderu zawierającego skrypt U-SQL. Nazwa pliku jest uwzględniana wielkość liter. | Tak      |
| scriptLinkedService | Połączona usługa, która łączy **usługi Azure Data Lake Store** lub **usługi Azure Storage** zawierający skrypt w usłudze data factory | Tak      |
| degreeOfParallelism | Maksymalna liczba węzłów równocześnie używane do uruchamiania zadania. | Nie       |
| priorytet            | Określa, które spośród wszystkich, które są umieszczane w kolejce zadań, należy wybrać do uruchomienia jako pierwsza. Im mniejsza liczba, tym wyższy priorytet. | Nie       |
| parameters          | Parametry do przekazania do skryptu U-SQL.    | Nie       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparat języka U-SQL do użycia. | Nie       |
| compilationMode     | <p>Tryb kompilacji języka U-SQL. Musi być jedną z następujących wartości: **Semantyczne:** Wykonywać tylko semantycznego testy i wykonuje niezbędne testów, **pełna:** Wykonania pełnej kompilacji, w tym sprawdzanie składni, optymalizacja, generowanie kodu itp., **SingleBox:** Wykonywanie pełnej kompilacji, za pomocą ustawienia TargetType SingleBox. Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb optymalne kompilacji. | Nie |

Zobacz [SearchLogProcessing.txt](#sample-u-sql-script) definicji skryptu. 

## <a name="sample-u-sql-script"></a>Przykładowy skrypt U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

W wyżej przykładowy skrypt danych wejściowych i wyjściowych skrypt jest zdefiniowany w  **\@w** i  **\@się** parametrów. Wartości  **\@w** i  **\@się** parametry w skrypcie U-SQL są przekazywane dynamicznie przez usługę Data Factory przy użyciu sekcji "parameters". 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

W takim przypadku pliki wejściowe nadal są pobierane z folderu /datalake/input i pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne, na podstawie czasu rozpoczęcia okna przekazywany pobiera wyzwolenia potoku.  

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
