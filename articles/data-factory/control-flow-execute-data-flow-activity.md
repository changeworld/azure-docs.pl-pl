---
title: Wykonywanie działań przepływu danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Działanie przepływu danych wykonaj uruchamia przepływy danych.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557570"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Wykonywanie działań przepływu danych w usłudze Azure Data Factory
Działanie przepływu danych wykonaj służy do uruchamiania przepływu danych ADF uruchomienia debugowania (piaskownicy) potoku i uruchomień potoków wyzwolone.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Właściwości typu

* ```dataflow``` jest nazwą jednostki przepływu danych, który chcesz wykonać
* ```compute``` w tym artykule opisano środowiska wykonawczego platformy Spark
* ```coreCount``` jest to liczba rdzeni, aby przypisać do wykonania tego działania przepływu danych

![Wykonywania przepływu danych](media/data-flow/activity-data-flow.png "wykonywania przepływu danych")

### <a name="run-on"></a>Uruchom na

Wybierz środowisko obliczeniowe dla wykonania przepływu danych. Wartość domyślna to Azure automatyczne rozwiązanie domyślne środowisko Integration Runtime. Ten wybór spowoduje to wykonanie przepływu danych w środowisku platformy Spark w tym samym regionie, co fabryką danych. Typ obliczenia będą klastra zadań, co oznacza, że środowisko obliczeniowe może potrwać kilka minut do uruchamiania.

### <a name="debugging-pipelines-with-data-flows"></a>Debugowanie potoków przy użyciu przepływów danych

![Debugowanie przycisk](media/data-flow/debugbutton.png "przycisk debugowania")

Korzystanie z warmed klastra do badania przepływów danych interaktywnie podczas debugowania procesu, uruchom, użyj danych przepływu Debug. Opcja Pipleine debugowania do testowania przepływów danych w potoku.

### <a name="compute-type"></a>Typ obliczeń

Możesz wybrać ogólnego przeznaczenia, obliczenia zoptymalizowane pod kątem lub zoptymalizowane pod kątem pamięci, w zależności od wymagań przepływu danych.

### <a name="core-count"></a>Liczba rdzeni

Wybierz liczbę rdzeni, które chcesz przypisać do zadania. W przypadku mniejszych zadań mniejszą liczbą rdzeni będzie działać lepiej.

### <a name="staging-area"></a>Obszar przejściowy

Jeśli dane są wychwytywania do usługi Azure Data Warehouse, musisz wybrać lokalizację tymczasową dla obciążenia funkcji Polybase usługi batch.

## <a name="parameterized-datasets"></a>Sparametryzowany zestawów danych

Jeśli używasz sparametryzowane zestawów danych, należy podać wartości parametrów.

![Parametry przepływu danych wykonaj](media/data-flow/params.png "parametrów")

### <a name="debugging-parameterized-data-flows"></a>Debugowanie sparametryzowane przepływów danych

Możesz debugować można tylko przepływy danych za pomocą sparametryzowanych zestawy danych z potoku debugowanie, uruchamianie przy użyciu działań przepływu danych wykonaj. Obecnie sesji debugowania interakcyjnego w przepływ danych ADF nie działają z sparametryzowane zestawów danych. Potok wykonań i uruchomień debugowania będzie działać z parametrami.

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
