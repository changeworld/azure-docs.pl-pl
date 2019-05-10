---
title: Wykonywanie działań przepływu danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Jak wykonać na danych przepływów z wewnątrz potok usługi data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e1d4ce355f34014d5099c4b46f4420d032363fce
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236680"
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

Korzystanie z warmed klastra do badania przepływów danych interaktywnie podczas debugowania procesu, uruchom, użyj danych przepływu Debug. Użyj opcji potoku debugowania do testowania przepływów danych w potoku.

### <a name="run-on"></a>Uruchom na

Jest to wymagane pola, które definiuje, które środowisko IR do użycia dla wykonania działania przepływu danych w sieci. Domyślnie Data Factory użyje domyślnej automatyczne rozwiązanie Azure Integration runtime. Można jednak utworzyć własne Azure środowiska Integration Runtime, zdefiniuj konkretnych regionów, obliczeniowa to typ, liczby rdzeni i czas wygaśnięcia dla wykonywanie działań przepływu danych.

Ustawieniem domyślnym dla wykonania przepływu danych jest 8 rdzeni obliczeniowych ogólnego z wartością TTL równą 60 minut.

### <a name="staging-area"></a>Obszar przejściowy

Jeśli dane są wychwytywania do usługi Azure Data Warehouse, musisz wybrać lokalizację tymczasową dla obciążenia funkcji Polybase usługi batch.

## <a name="parameterized-datasets"></a>Sparametryzowany zestawów danych

Jeśli używasz sparametryzowane zestawów danych, należy podać wartości parametrów.

![Parametry przepływu danych wykonaj](media/data-flow/params.png "parametrów")

### <a name="debugging-parameterized-data-flows"></a>Debugowanie sparametryzowane przepływów danych

Możesz debugować można tylko przepływy danych za pomocą sparametryzowanych zestawy danych z potoku debugowanie, uruchamianie przy użyciu działań przepływu danych wykonaj. Obecnie sesji debugowania interakcyjnego w przepływ danych ADF nie działają z sparametryzowane zestawów danych. Potok wykonań i uruchomień debugowania będzie działać z parametrami.

Dobrym rozwiązaniem jest tworzenie przepływu danych z zestawu danych statycznych, by były pełne metadane kolumny propagacji dostępne w czasie projektowania. Następnie zastąp statyczny zestaw danych dynamicznych sparametryzowane zestawu danych podczas operacjonalizacja potoku przepływu danych.

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
