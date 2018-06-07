---
title: Przekształcanie danych za pomocą skryptu U-SQL - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposobu przetwarzania lub Przekształcanie danych za pomocą skryptów U-SQL w usłudze obliczeniowych Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: abnarain
ms.openlocfilehash: 1bf030d7eaba5c8aa608c504f65c5ebf291eab3d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619698"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych za pomocą skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-usql-activity.md)
> * [Wersja 2 — wersja zapoznawcza](transform-data-using-data-lake-analytics.md)

Potok w fabryce danych Azure przetwarza dane w usługach magazynu połączone, przy użyciu obliczeniowego połączonej usługi. Zawiera sekwencję działań, gdzie każde działanie wykonuje operację przetwarzania specyficznego dla. W tym artykule opisano **Data Lake Analytics U-SQL działania** , na którym działa **U-SQL** skryptom na **Azure Data Lake Analytics** obliczeniowe połączonej usługi. 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [USQL działania w wersji 1](v1/data-factory-usql-activity.md).

Przed utworzeniem potoku z działaniem Data Lake Analytics U-SQL, należy utworzyć konto usługi Azure Data Lake Analytics. Aby zapoznać się z usługą Azure Data Lake Analytics, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics połączone usługi
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć z usługą Azure Data Lake Analytics obliczeniowe usługi fabryka danych Azure. Data Lake Analytics U-SQL działania w potoku odwołuje się do tej połączonej usługi. 

Poniższa tabela zawiera opisy ogólne właściwości używane w definicji JSON. 

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Powinien mieć ustawioną właściwość type: **AzureDataLakeAnalytics**. | Yes                                      |
| **accountName**          | Nazwa konta usługi Azure Data Lake Analytics.  | Yes                                      |
| **dataLakeAnalyticsUri** | Identyfikator URI, usługi Azure Data Lake Analytics.           | Nie                                       |
| **Identyfikator subskrypcji**       | Identyfikator subskrypcji platformy Azure                    | Nie (Jeśli nie zostanie określony, używany subskrypcji fabryki danych). |
| **resourceGroupName**    | Nazwa grupy zasobów platformy Azure                | Nie (Jeśli nie zostanie określony, używana grupa zasobów z fabryką danych). |

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi
Usługa Azure Data Lake Analytics połączone wymaga uwierzytelniania głównej usługi, aby połączyć się z usługą Azure Data Lake Analytics. Aby używać uwierzytelniania głównej usługi, Zarejestruj podmiot aplikacji w usłudze Azure Active Directory (Azure AD) i przyznać jej dostęp do usługi Data Lake Analytics i Data Lake Store używa. Aby uzyskać szczegółowe instrukcje, zobacz [do usługi uwierzytelniania](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:

* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Udziel uprawnienia głównego usługi przy użyciu usługi Azure Data Lake Anatlyics [Kreatora dodawania użytkownika](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Uwierzytelnianie usługi głównej przez określenie następujących właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator aplikacji klienta.     | Yes      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Yes      |
| **Dzierżawy**              | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Yes      |

**Przykład: Usługa podmiotu zabezpieczeń uwierzytelniania**
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
            "tenant": "<tenant ID>",
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

Aby dowiedzieć się więcej na temat połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potoku z działaniem Data Lake Analytics U-SQL. Definicji działania zawiera odwołanie do usługi Azure Data Lake Analytics połączone utworzony wcześniej. Do uruchomienia skryptu U-SQL w programie Data Lake Analytics, fabryki danych przesyła skryptu, określone do usługi Data Lake Analytics, i wymagają danych wejściowych i wyjściowych jest zdefiniowany w skrypcie dla usługi Data Lake Analytics pobrać i danych wyjściowych. 

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
| name                | Nazwa działania w potoku     | Yes      |
| description         | Tekst opisujący działanie robi.  | Nie       |
| type                | Dla działania Data Lake Analytics U-SQL jest typ działania **DataLakeAnalyticsU SQL**. | Yes      |
| linkedServiceName   | Połączona usługa do usługi Azure Data Lake Analytics. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu.  |Yes       |
| scriptPath          | Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. | Yes      |
| scriptLinkedService | Połączona usługa, która łączy **Azure Data Lake Store** lub **usługi Azure Storage** zawierający skrypt do fabryki danych | Yes      |
| degreeOfParallelism | Maksymalna liczba węzłów jednocześnie użyta do uruchomienia zadania. | Nie       |
| priorytet            | Określa, które spośród wszystkich znajdujących się w kolejce zadań należy wybrać ma być uruchomiony. Im niższy numer, tym wyższy priorytet. | Nie       |
| parameters          | Parametry do przekazania do skryptu U-SQL.    | Nie       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparatu U-SQL do użycia. | Nie       |
| właściwość compilationMode     | <p>Tryb kompilacji U-SQL. Musi być jedną z następujących wartości: **semantycznej:** wykonywać tylko semantycznego kontroli i potrzeby związane z poprawnością kontroli, **pełna:** wykonania pełnej kompilacji, takich jak sprawdzanie składni, optymalizacja, generowania kodu, itp., **SingleBox:** wykonania pełnej kompilacji, z ustawieniem TargetType do SingleBox. Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb optymalne kompilacji. | Nie |

Fabryka danych przesyła zobacz [definicji skryptu SearchLogProcessing.txt](#sample-u-sql-script) definicji skryptu. 

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

W powyżej przykładowy skrypt danych wejściowych i wyjściowych skryptu jest zdefiniowany w **@in** i **@out** parametrów. Wartości **@in** i **@out** Parametry skryptu U-SQL są przekazywane dynamicznie przez fabrykę danych zgodnie z sekcją "parameters". 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

W takim przypadku pliki wejściowe nadal są pobierane z folderu /datalake/input i pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia okna przekazywany w potoku pobiera wyzwolenia.  

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego usługi uczenie maszyny](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
