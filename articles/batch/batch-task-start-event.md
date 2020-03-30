---
title: Zdarzenie rozpoczęcia zadania usługi Azure Batch
description: Informacje referencyjne dla zdarzenia początkowego zadania usługi Batch. To zdarzenie jest emitowane po zaplanowaniu rozpoczęcia zadania w węźle obliczeniowym przez harmonogram.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022821"
---
# <a name="task-start-event"></a>Zdarzenie rozpoczęcia zadania

 To zdarzenie jest emitowane po zaplanowaniu rozpoczęcia zadania w węźle obliczeniowym przez harmonogram. Należy zauważyć, że jeśli zadanie zostanie ponowione lub ponownie w kolejce to zdarzenie zostanie wyemitowane ponownie dla tego samego zadania, ale liczba ponownych prób i wersja zadania systemowego zostaną odpowiednio zaktualizowane.


 W poniższym przykładzie przedstawiono treść zdarzenia początkowego zadania.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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
|`jobId`|Ciąg|Identyfikator zadania zawierającego zadanie.|
|`id`|Ciąg|Identyfikator zadania.|
|`taskType`|Ciąg|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie menedżera zadań lub "Użytkownik", wskazując, że nie jest to zadanie menedżera zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób w zadaniu. Wewnętrznie usługa Batch może ponowić próbę wykonania zadania w celu uwzględnienia problemów przejściowych. Te problemy mogą obejmować błędy planowania wewnętrznego lub próby odzyskania z węzłów obliczeniowych w złym stanie.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadaniem jest zadanie wieloodstanowiowe wymagające wielu węzłów obliczeniowych.  Zobacz [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) uzyskać szczegółowe informacje.|
|[`constraints`](#constraints)|Typ złożony|Ograniczenia wykonywania, które mają zastosowanie do tego zadania.|
|[`executionInfo`](#executionInfo)|Typ złożony|Zawiera informacje o wykonaniu zadania.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`poolId`|Ciąg|Identyfikator puli, na której uruchomiono zadanie.|
|`nodeId`|Ciąg|Identyfikator węzła, na którym uruchomiono zadanie.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>obliczeń wieloinsytowych

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`numberOfInstances`|int|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a><a name="constraints"></a>Ograniczenia

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba ponownych ponawianych zadań. Usługa Batch ponawia ponawia ponawia zadanie, jeśli jego kod zakończenia jest niezerowy.<br /><br /> Należy zauważyć, że ta wartość w szczególności kontroluje liczbę ponownych prób. Usługa Batch spróbuje tego zadania raz, a następnie może ponowić próbę do tego limitu. Na przykład jeśli maksymalna liczba ponownych prób wynosi 3, Batch próbuje zadanie do 4 razy (jedna próba początkowa i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie ponawia prób.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi -1, usługa Batch ponawia próby zadań bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>Executioninfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`retryCount`|Int32|Liczba ponownych ponownych ponownych ponownych ponów wykonania zadania przez usługę Batch. Zadanie jest ponowione, jeśli kończy działanie z kodem zakończenia niezerowego, do określonego MaxTaskRetryCount|
