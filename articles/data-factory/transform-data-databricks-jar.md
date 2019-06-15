---
title: Przekształcanie danych przy użyciu pliku Jar usługi Databricks — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposób przetwarzania lub przekształcać dane, uruchamiając Jar usługi Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d299a785d50657ef40c0c49cb2dce33b8939fd02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860991"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Przekształcanie danych za pomocą działania Jar w usłudze Azure Databricks

Azure Databricks Jar aktywność [potoku usługi Data Factory](concepts-pipelines-activities.md) uruchamia Jar platformy Spark w klastrze usługi Azure Databricks. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych. Usługa Azure Databricks to platforma zarządzanych dla platformy Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definicji działania usługi Databricks Jar

Oto przykład definicji JSON działania Jar usługi Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Właściwości działania usługi Databricks Jar

W poniższej tabeli opisano właściwości JSON używanych w definicji JSON:

|Właściwość|Opis|Wymagane|
|:--|---|:-:|
|name|Nazwa działania w potoku.|Yes|
|description|Tekst opisujący, co działanie robi.|Nie|
|— typ|Typ działania jest DatabricksSparkJar Jar działania usługi Databricks.|Tak|
|linkedServiceName|Nazwa połączonej usługi, w którym uruchamiany jest plik Jar działania usługi Databricks. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.|Yes|
|mainClassName|Pełna nazwa klasy zawierającej metodę głównego, do wykonania. Ta klasa musi być zawarty w pliku JAR w bibliotece.|Yes|
|parameters|Parametry, które zostaną przekazane do metody głównej.  Jest to tablica ciągów.|Nie|
|Biblioteki|Lista bibliotek można zainstalować w klastrze, które spowodują wykonanie zadania. Może to być tablica < string, object >|Tak (co najmniej jeden zawierający metody mainClassName)|

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

Zazwyczaj bibliotek Jar są przechowywane w dbfs: / FileStore/jars przy użyciu interfejsu użytkownika. Możesz wyświetlić listę wszystkich za pomocą interfejsu wiersza polecenia: *usługi databricks fs ls dbfs: / FileStore/zadanie-plikach JAR* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiuj bibliotekę przy użyciu interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Użyj interfejsu wiersza polecenia Databricks [(kroki instalacji)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Przykład — kopiowanie pliku JAR dbfs: *dbfs:/docs/sparkpi.jar cp SparkPi — zestawu 0.1.jar dbfs*
