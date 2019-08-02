---
title: Przekształcanie danych za pomocą notesu datakosteks — Azure | Microsoft Docs
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając Notes datakostks.
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
ms.openlocfilehash: 2bc8b84d4b98036acc93788dee88444786df139e
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335855"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Przekształcanie danych przez uruchamianie notesu datakostks

Działanie notesu Azure Databricks w [potoku Data Factory](concepts-pipelines-activities.md) uruchamia Notes datacegły w obszarze roboczym Azure Databricks. W tym artykule przedstawiono artykuł [działania](transform-data.md) przekształcania danych, który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definicja działania notesu dla elementów datacegłs

Poniżej znajduje się przykładowa definicja JSON działania notesu datakostky:

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

## <a name="databricks-notebook-activity-properties"></a>Właściwości działania notesu dla elementów datacegły

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie działania.|Nie|
|— typ|W przypadku działania notesu datacegły typem działania jest DatabricksNotebook.|Tak|
|linkedServiceName|Nazwa połączonej usługi datakostki, w której działa Notes datakostki. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług](compute-linked-services.md) obliczeniowych.|Yes|
|notebookPath|Ścieżka bezwzględna notesu do uruchomienia w obszarze roboczym datakostki. Ta ścieżka musi zaczynać się od ukośnika.|Tak|
|baseParameters|Tablica par klucz-wartość. Parametry podstawowe mogą być używane dla każdego uruchomienia działania. Jeśli Notes przyjmuje parametr, który nie jest określony, zostanie użyta wartość domyślna z notesu. Więcej informacji na temat parametrów [](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)w notesach datakostks.|Nie|
|bibliotece|Lista bibliotek do zainstalowania w klastrze, w którym będą wykonywane zadania. Może to być tablica \<ciągu, > obiektu.|Nie|


## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwane dla działań datakostek

W powyższych definicjach działań datacegły należy określić następujące typy biblioteki: *jar*, *jaja*, *WHL*, *Maven*, *PyPi*, *Cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

Aby uzyskać więcej informacji, zobacz [dokumentację](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) dotyczącą typów bibliotek.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przekazać bibliotekę w kostkach

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Korzystanie z interfejsu użytkownika obszaru roboczego](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Aby uzyskać ścieżkę dBfs biblioteki dodanej przy użyciu interfejsu użytkownika, możesz użyć interfejsu [wiersza polecenia datakosteks (instalacja)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Zazwyczaj biblioteki jar są przechowywane w obszarze dBfs:/FileStore/Jars przy użyciu interfejsu użytkownika. Wszystkie te listę można wyświetlić za pomocą interfejsu wiersza polecenia: datakosteks *FS LS dBfs:/FileStore/Jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiowanie biblioteki przy użyciu interfejsu wiersza polecenia datakosteks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *datakosteks FS CP sparkpi-Assembly-0,1. jar dBfs:/FileStore/Jars*
