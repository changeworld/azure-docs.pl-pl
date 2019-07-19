---
title: Zdarzenie ukończenia zadania Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia ukończenia zadania wsadowego.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 60e5e6cc6fdd839c8bbe44d8e1d2e794e7afb34d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323056"
---
# <a name="task-complete-event"></a>Zdarzenie ukończenia zadania

 To zdarzenie jest emitowane po zakończeniu zadania, niezależnie od kodu zakończenia. To zdarzenie może służyć do określenia czasu trwania zadania, miejsce uruchomienia zadania oraz tego, czy podjęto próbę.


 Poniższy przykład przedstawia treść zdarzenia ukończenia zadania.

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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|jobId|String|Identyfikator zadania zawierającego zadanie.|
|id|String|Identyfikator zadania.|
|taskType|String|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie Menedżera zadań lub "użytkownika" wskazujące, że nie jest to zadanie Menedżera zadań. To zdarzenie nie jest emitowane w przypadku zadań związanych z przygotowaniem zadania, zadań wydania zadania lub uruchamiania zadań.|
|systemTaskVersion|Int32|Jest to wewnętrzny licznik ponownych prób dla zadania. Wewnętrznie usługa Batch może ponowić zadanie, aby uwzględnić przejściowe problemy. Te problemy mogą obejmować wewnętrzne błędy planowania lub próby odzyskania z węzłów obliczeniowych w nieprawidłowym stanie.|
|[nodeInfo](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[multiInstanceSettings](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem z wieloma wystąpieniami wymagającymi wielu węzłów obliczeniowych.  Aby uzyskać szczegółowe informacje, zobacz [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
|[powiązanych](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[executionInfo](#executionInfo)|Typ złożony|Zawiera informacje o wykonywaniu zadania.|

###  <a name="nodeInfo"></a>nodeInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|poolId|String|Identyfikator puli, w której uruchomiono zadanie.|
|nodeId|String|Identyfikator węzła, w którym uruchomiono zadanie.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|numberOfInstances|Int32|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a>powiązanych

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maksymalna liczba prób wykonania zadania. Usługa Batch ponawia próbę wykonania zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy zauważyć, że ta wartość kontroluje liczbę ponownych prób. Usługa Batch spróbuje wykonać zadanie jeden raz, a następnie ponowić próbę wykonania tego limitu. Jeśli na przykład maksymalna liczba ponownych prób wynosi 3, zadanie wsadowe próbuje wykonać zadania do 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie wykonuje żadnych ponownych prób wykonywania zadań.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi-1, usługa Batch ponawia zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|

###  <a name="executionInfo"></a>executionInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|startTime|Datetime|Godzina, o której uruchomiono zadanie. "Uruchomiona" odpowiada stanie **uruchomienia** , więc jeśli zadanie Określa pliki zasobów lub pakiety aplikacji, czas rozpoczęcia odzwierciedla czas, w którym zadanie rozpoczęło pobieranie lub wdrażanie.  Jeśli zadanie zostało ponownie uruchomione lub ponowione, jest to Ostatnia godzina, o której uruchomiono zadanie.|
|endTime|Datetime|Godzina ukończenia zadania.|
|exitCode|Int32|Kod zakończenia zadania.|
|retryCount|Int32|Liczba ponownych prób wykonania zadania przez usługę Batch. Zadanie zostanie ponowione, jeśli zostanie zakończone z niezerowym kodem zakończenia, do określonego MaxTaskRetryCount.|
|requeueCount|Int32|Liczba uruchomień zadania w kolejce przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy użytkownik usuwa węzły z puli (zmieniając rozmiar lub zmniejszając pulę) lub gdy zadanie jest wyłączone, użytkownik może określić, że uruchomione zadania w węzłach będą ponownie umieszczane w kolejce do wykonania. Ta liczba śledzi, ile razy zadanie zostało ponownie poddane kolejce z tych przyczyn.|
