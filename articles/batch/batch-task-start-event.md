---
title: Zdarzenie uruchomienia zadania Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia uruchomienia zadania usługi Batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: e8265286a5d33c9a8a118dafa66a83b5ed36f8a6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029623"
---
# <a name="task-start-event"></a>Zdarzenie rozpoczęcia zadania

 To zdarzenie jest emitowane po zaplanowaniu uruchomienia zadania w węźle obliczeniowym przez harmonogram. Należy pamiętać, że jeśli zadanie zostanie ponowione lub zostanie ponownie poddana w kolejce, to zdarzenie zostanie wyemitowane dla tego samego zadania, ale liczba ponownych prób i wersja zadania systemowego zostaną odpowiednio zaktualizowane.


 Poniższy przykład przedstawia treść zdarzenia rozpoczęcia zadania.

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
|`taskType`|Ciąg|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie Menedżera zadań lub "użytkownika" wskazujące, że nie jest to zadanie Menedżera zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób dla zadania. Wewnętrznie usługa Batch może ponowić zadanie, aby uwzględnić przejściowe problemy. Te problemy mogą obejmować wewnętrzne błędy planowania lub próby odzyskania z węzłów obliczeniowych w nieprawidłowym stanie.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem z wieloma wystąpieniami wymagającymi wielu węzłów obliczeniowych.  Aby uzyskać szczegółowe informacje, zobacz [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[`executionInfo`](#executionInfo)|Typ złożony|Zawiera informacje o wykonywaniu zadania.|

###  <a name="nodeInfo"></a>nodeInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`poolId`|Ciąg|Identyfikator puli, w której uruchomiono zadanie.|
|`nodeId`|Ciąg|IDENTYFIKATOR węzła, w którym uruchomiono zadanie.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`numberOfInstances`|Int|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a>powiązanych

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba prób wykonania zadania. Usługa Batch ponawia próbę wykonania zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy zauważyć, że ta wartość kontroluje liczbę ponownych prób. Usługa Batch spróbuje wykonać zadanie jeden raz, a następnie ponowić próbę wykonania tego limitu. Jeśli na przykład maksymalna liczba ponownych prób wynosi 3, zadanie wsadowe próbuje wykonać zadania do 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie wykonuje żadnych ponownych prób wykonywania zadań.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi-1, usługa Batch ponawia zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|

###  <a name="executionInfo"></a>executionInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`retryCount`|Int32|Liczba ponownych prób wykonania zadania przez usługę Batch. Zadanie jest ponawiane, jeśli zostanie zakończone z niezerowym kodem zakończenia, do określonego MaxTaskRetryCount|
