---
title: Przekształcanie danych za pomocą notesu usługi Databricks — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposób przetwarzania lub przekształcać dane, uruchamiając notesu usługi Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 8036a8694bb8c8d0db236eba831f13dc2bf47d0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311669"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Przekształcanie danych przez uruchamianie notesu usługi Databricks

Działania notesu usługi Databricks platformy Azure w [potoku usługi Data Factory](concepts-pipelines-activities.md) uruchamia notesu usługi Databricks w obszarze roboczym usługi Azure Databricks. W tym artykule opiera się na [działania przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych i przekształcania danych. Usługa Azure Databricks to platforma zarządzanych dla platformy Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definicji działania notesu usługi Databricks

Oto przykład definicji JSON działania notesu usługi Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Właściwości działania notesu usługi Databricks

W poniższej tabeli opisano właściwości JSON używanych w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Yes|
|description|Tekst opisujący, co działanie robi.|Nie|
|type|W przypadku działania notesu usługi Databricks typ działania jest DatabricksNotebook.|Tak|
|linkedServiceName|Nazwa połączonej usługi, na którym działa notesu usługi Databricks usługi Databricks. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.|Tak|
|notebookPath|Ścieżka bezwzględna Notes do uruchomienia w obszarze roboczym usługi Databricks. Ta ścieżka musi zaczynać się od ukośnika.|Tak|
|baseParameters|Tablica par klucz-wartość. Podstawowe parametry może służyć do uruchamiania każdego działania. Jeśli notes przyjmuje parametr, który nie jest określona, zostanie użyta wartość domyślna, z notesu. Znajdź więcej informacji na temat parametrów w [elementów Databricks notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nie|
|Biblioteki|Lista bibliotek można zainstalować w klastrze, które spowodują wykonanie zadania. Może to być tablica \<string, object >.|Nie|


## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwanych dla działania usługi Databricks

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

Zazwyczaj bibliotek Jar są przechowywane w dbfs: / FileStore/jars przy użyciu interfejsu użytkownika. Możesz wyświetlić listę wszystkich za pomocą interfejsu wiersza polecenia: *usługi databricks fs ls dbfs: plikachJAR/FileStore/* .



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiuj bibliotekę przy użyciu interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *databricks fs cp dbfs SparkPi — zestawu 0.1.jar: / FileStore/plikach JAR*
