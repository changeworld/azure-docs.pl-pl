---
title: Wykonywanie potoków Azure Machine Learning w potokach Azure Data Factory
description: Dowiedz się, jak uruchamiać potoki Azure Machine Learning w potokach Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: a257c7d3f288f9a2e1998bf0679b7146b089cb50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683893"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Wykonaj potoki Azure Machine Learning w Azure Data Factory

Uruchom potoki Azure Machine Learning jako krok w potokach Azure Data Factory. Działanie Machine Learning wykonywania potoku włącza scenariusze prognozowania partii, takie jak identyfikowanie możliwych wartości domyślnych pożyczek, Określanie tonacji oraz analizowanie wzorców zachowania klientów.

## <a name="syntax"></a>Składnia

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania w potoku | Ciąg | Tak
type | Typ działania to "AzureMLExecutePipeline" | Ciąg | Tak
linkedServiceName | Połączona usługa do Azure Machine Learning | Odwołanie do połączonej usługi | Tak
mlPipelineId | Identyfikator opublikowanego potoku Azure Machine Learning | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak
eksperymentname | Nazwa eksperymentu uruchomienia dla Machine Learning uruchomienia potoku | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie
mlPipelineParameters | Klucz, pary wartości do przesłania do opublikowanego punktu końcowego potoku Azure Machine Learning. Klucze muszą być zgodne z nazwami parametrów potoku zdefiniowanych w opublikowanym potoku Machine Learning | Obiekt z parami wartości klucza (lub wyrażeniem z obiektem ResultType) | Nie
mlParentRunId | Identyfikator uruchomienia potoku Azure Machine Learning nadrzędnego | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie
continueOnStepFailure | Czy kontynuować wykonywanie innych kroków w potoku Machine Learning uruchomienie w przypadku niepowodzenia kroku | wartość logiczna | Nie

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
