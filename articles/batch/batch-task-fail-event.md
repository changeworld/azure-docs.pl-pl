---
title: Zdarzenie niepowodzenie zadania usługi Azure Batch
description: Odwołanie do zdarzenia niepowodzenia zadania usługi Batch. To zdarzenie zostanie wyemitowane oprócz zdarzenia zakończenia zadania i może służyć do wykrywania, gdy zadanie nie powiodło się.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: labrenne
ms.openlocfilehash: 2bc958d6dca2b3caae665e6f9b080c651ace9ea0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022889"
---
# <a name="task-fail-event"></a>Zdarzenie niepowodzenia zadania

 To zdarzenie jest emitowane po zakończeniu zadania z błędem. Obecnie wszystkie kody wyjścia niezerowe są uważane za błędy. To zdarzenie zostanie wyemitowane *oprócz* zdarzenia zakończenia zadania i może służyć do wykrywania, gdy zadanie nie powiodło się.


 W poniższym przykładzie przedstawiono treść zdarzenia niepowodzenia zadania.

```
{
    "jobId": "myJob",
    "id": "myTask",
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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`jobId`|Ciąg|Identyfikator zadania zawierającego zadanie.|
|`id`|Ciąg|Identyfikator zadania.|
|`taskType`|Ciąg|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie menedżera zadań lub "Użytkownik", wskazując, że nie jest to zadanie menedżera zadań. To zdarzenie nie jest emitowane dla zadań przygotowania zadania, zadań zwalniania zadania lub uruchamiania zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób w zadaniu. Wewnętrznie usługa Batch może ponowić próbę wykonania zadania w celu uwzględnienia problemów przejściowych. Te problemy mogą obejmować błędy planowania wewnętrznego lub próby odzyskania z węzłów obliczeniowych w złym stanie.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem wieloodstanowienia wymagającym wielu węzłów obliczeniowych.  Zobacz [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) szczegóły.|
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
|`numberOfInstances`|Int32|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a><a name="constraints"></a>Ograniczenia

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba ponownych ponawianych zadań. Usługa Batch ponawia ponawia ponawia zadanie, jeśli jego kod zakończenia jest niezerowy.<br /><br /> Należy zauważyć, że ta wartość w szczególności kontroluje liczbę ponownych prób. Usługa Batch spróbuje tego zadania raz, a następnie może ponowić próbę do tego limitu. Na przykład jeśli maksymalna liczba ponownych prób wynosi 3, Batch próbuje zadanie do 4 razy (jedna próba początkowa i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie ponawia prób.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi -1, usługa Batch ponawia próby zadań bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|


###  <a name="executioninfo"></a><a name="executionInfo"></a>Executioninfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`startTime`|DateTime|Czas, w którym zadanie rozpoczęło pracę. "Uruchomiony" odpowiada **uruchomionemu** stanowi, więc jeśli zadanie określa pliki zasobów lub pakiety aplikacji, czas rozpoczęcia odzwierciedla czas, w którym zadanie rozpoczęło pobieranie lub wdrażanie tych pakietów.  Jeśli zadanie zostało ponownie uruchomione lub ponowione, jest to ostatni czas, w którym zadanie zostało uruchomione.|
|`endTime`|DateTime|Czas, w którym zadanie zostało ukończone.|
|`exitCode`|Int32|Kod zakończenia zadania.|
|`retryCount`|Int32|Liczba ponownych ponownych ponownych ponownych ponów wykonania zadania przez usługę Batch. Zadanie jest ponowione, jeśli kończy pracę z kodem zakończenia niezerowego, do określonego MaxTaskRetryCount.|
|`requeueCount`|Int32|Liczba ponownych kolejek przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy użytkownik usuwa węzły z puli (przez zmiana rozmiaru lub zmniejszanie puli) lub gdy zadanie jest wyłączone, użytkownik może określić, że uruchamianie zadań w węzłach zostanie ponownie wysunięty do wykonania. Ta liczba śledzi, ile razy zadanie zostało ponownie wysunął się z tych powodów.|
