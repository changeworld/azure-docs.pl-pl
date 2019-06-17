---
title: Zdarzenie rozpoczęcia zadania w usłudze Azure Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie rozpoczęcia zadania usługi Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549873"
---
# <a name="task-start-event"></a>Zdarzenie rozpoczęcia zadania

 To zdarzenie jest emitowane, gdy zadanie zostało zaplanowane do uruchomienia w węźle obliczeniowym przez harmonogram. Należy pamiętać, że jeśli zadanie zostanie ponowiona lub ponownie umieszczone w kolejce to zdarzenie zostanie zostanie ponownie wyemitowane dla tego samego zadania, ale liczba ponownych prób i wersja zadania system będzie odpowiednio aktualizowana.


 Poniższy kod przedstawia zdarzenie początkowe treści zadania.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|jobId|String|Identyfikator zadania zawierającego zadanie.|
|id|String|Identyfikator zadania.|
|taskType|String|Typ zadania. Może to być "JobManager" wskazujący, że to zadanie podrzędne Menedżera zadań lub w obszarze "User", wskazująca, że nie jest to zadanie podrzędne Menedżera zadań.|
|systemTaskVersion|Int32|Jest to licznik wewnętrzny ponownych prób dla zadania. Wewnętrznie usługa Batch ponowić zadanie, aby uwzględnić problemy przejściowe. Te problemy mogą zawierać błędy wewnętrzne planowania lub próbuje odzyskać z węzłów obliczeniowych w złym stanie.|
|[nodeInfo](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie podrzędne.|
|[multiInstanceSettings](#multiInstanceSettings)|Typ złożony|Określa, czy zadanie jest zadaniem wieloma wystąpieniami wymagającym wielu węzłów obliczeniowych.  Zobacz [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) Aby uzyskać szczegółowe informacje.|
|[Ograniczenia](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[executionInfo](#executionInfo)|Typ złożony|Zawiera informacje dotyczące wykonywania zadania tego typu.|

###  <a name="nodeInfo"></a> nodeInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|poolId|String|Identyfikator puli, na którym uruchomiono zadanie podrzędne.|
|nodeId|String|Identyfikator węzła, na którym uruchomiono zadanie podrzędne.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|numberOfInstances|Int|Liczba węzłów obliczeń wymagana przez zadanie.|

###  <a name="constraints"></a> Ograniczenia

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maksymalna liczba przypadków, gdy zadanie może być ponawiane. Usługa partia zadań ponawia próbę zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy pamiętać, że ta wartość określa, w szczególności liczbę ponownych prób. Usługa Batch spróbuje zadanie raz, a może następnie podjąć kolejną próbę do tego limitu. Na przykład jeśli maksymalna liczba ponowień to 3, maksymalna liczba prób partii to zadanie maksymalnie 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponowień to 0, usługa partia zadań nie ponów próbę wykonania zadania.<br /><br /> Jeśli maksymalna liczba ponowień to -1, usługa partia zadań ponawia próbę zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (bez ponawiania).|

###  <a name="executionInfo"></a> executionInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|retryCount|Int32|Liczba przypadków, gdy zadanie było ponawiane przez usługę Batch. Zadanie zostanie ponowiony, jeśli kończy działanie z kodem zakończenia różny od zera, aż określony MaxTaskRetryCount|
