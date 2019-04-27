---
title: Przekształcanie danych za pomocą języka Python usługi Databricks — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposób przetwarzania lub przekształcać dane, uruchamiając Python usługi Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 60aafd983d1c21777276683a8685376a247d11f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589205"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Przekształcanie danych za pomocą działania języka Python w usłudze Azure Databricks

Działania języka Python usługi Azure Databricks w [potoku usługi Data Factory](concepts-pipelines-activities.md) jest uruchamiany plik w języku Python w klastrze usługi Azure Databricks. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych. Usługa Azure Databricks to platforma zarządzanych dla platformy Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definicji działania usługi Databricks w języku Python

Oto przykład definicji JSON działania języka Python usługi Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Właściwości działania usługi Databricks w języku Python

W poniższej tabeli opisano właściwości JSON używanych w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Yes|
|description|Tekst opisujący, co działanie robi.|Nie|
|type|W przypadku działania języka Python usługi Databricks typ działania jest DatabricksSparkPython.|Yes|
|linkedServiceName|Nazwa połączonej usługi, na którym działa działania języka Python usługi Databricks. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.|Yes|
|pythonFile|Identyfikator URI pliku Python, do wykonania. Obsługiwane są tylko DBFS ścieżki.|Yes|
|parameters|Parametry wiersza polecenia, które zostaną przekazane do pliku języka Python. Jest to tablica ciągów.|Nie|
|Biblioteki|Lista bibliotek można zainstalować w klastrze, które spowodują wykonanie zadania. Może to być tablica < string, object >|Nie|

## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwanych dla działania usługi databricks

W powyższej definicji działania usługi Databricks, należy określić te typy biblioteki: *jar*, *egg*, *maven*, *pypi*,  *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) dla typów biblioteki.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przekazać biblioteki w usłudze Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Korzystanie z obszaru roboczego usługi Databricks interfejsu użytkownika](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Aby uzyskać ścieżkę dbfs biblioteki dodać za pomocą interfejsu użytkownika, można użyć [interfejsu wiersza polecenia Databricks (instalacja)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Zazwyczaj bibliotek Jar są przechowywane w dbfs: / FileStore/jars przy użyciu interfejsu użytkownika. Możesz wyświetlić listę wszystkich za pomocą interfejsu wiersza polecenia: *usługi databricks fs ls dbfs: / FileStore/plikach JAR* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiuj bibliotekę przy użyciu interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *databricks fs cp dbfs SparkPi — zestawu 0.1.jar: / FileStore/plikach JAR*