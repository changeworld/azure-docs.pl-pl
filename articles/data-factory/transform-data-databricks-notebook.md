---
title: Przekształć dane z notesem Databricks - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sposobu przetwarzania lub Przekształcanie danych za pomocą notesu Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: fbf713b2d52469ae12fc284e0a3d7e3bc369daeb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620507"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Przekształcanie danych za pomocą notesu Databricks

Działanie usługi Azure Databricks notesu w [potoku fabryki danych](concepts-pipelines-activities.md) uruchamia notesu Databricks w obszarze roboczym Azure Databricks. W tym artykule opiera się na [działań przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań obsługiwanych transformacji. Azure Databricks jest zarządzana platforma Apache Spark uruchamiania.

## <a name="databricks-notebook-activity-definition"></a>Definicja działania Databricks notesu

Oto przykładowe definicji JSON aktywności notesu Databricks:

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
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Właściwości działania Databricks notesu

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Yes|
|description|Tekst opisujący działanie robi.|Nie|
|type|Dla działania notesu Databricks typ działania jest DatabricksNotebook.|Yes|
|linkedServiceName|Nazwa połączonej usługi, na którym jest uruchomiony notesu Databricks Databricks. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu.|Yes|
|notebookPath|Ścieżka bezwzględna do uruchomienia w obszarze roboczym Databricks notesu. Ta ścieżka musi rozpoczynać się od ukośnika.|Yes|
|baseParameters|Tablica par klucz-wartość. Podstawowe parametry mogą być używane do uruchamiania każdego działania. Jeśli notesu przyjmuje parametr, który nie jest określony, wartością domyślną z notesu będzie używany. Znajdź więcej informacji na temat parametrów w [notesów Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nie|
