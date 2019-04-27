---
title: Zdarzenie niepowodzenia zadania w usłudze Azure Batch | Dokumentacja firmy Microsoft
description: Odwołanie do zadania usługi Batch nie zdarzeń.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/15/2018
ms.author: v-junlch
ms.openlocfilehash: f37769ceb761b8c8bc4834568813bb1b7af7f66a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549992"
---
# <a name="task-fail-event"></a>Zdarzenie niepowodzenia zadania

 To zdarzenie jest emitowane, gdy zadanie zostanie ukończone z błędem. Obecnie wszystkie kody zakończenia różny od zera są traktowane jako błędy. To zdarzenie będzie emitowane *oprócz* zadanie ukończenia zdarzeń i może służyć do wykrywania, jeśli zadanie zakończyło się niepowodzeniem.


 Poniższy przykład pokazuje, w treści zadania niepowodzenie zdarzenia.

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
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|jobId|String|Identyfikator zadania zawierającego zadanie.|
|id|String|Identyfikator zadania.|
|taskType|String|Typ zadania. Może to być "JobManager" wskazujący, że to zadanie podrzędne Menedżera zadań lub w obszarze "User", wskazująca, że nie jest to zadanie podrzędne Menedżera zadań. To zdarzenie nie jest emitowane dla zadania przygotowania zadania, zadania podrzędne zwolnienia zadania lub zadania uruchamiania.|
|systemTaskVersion|Int32|Jest to licznik wewnętrzny ponownych prób dla zadania. Wewnętrznie usługa Batch ponowić zadanie, aby uwzględnić problemy przejściowe. Te problemy mogą zawierać błędy wewnętrzne planowania lub próbuje odzyskać z węzłów obliczeniowych w złym stanie.|
|[nodeInfo](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie podrzędne.|
|[multiInstanceSettings](#multiInstanceSettings)|Typ złożony|Określa, czy zadanie jest zadaniem wieloma wystąpieniami wymagającym wielu węzłów obliczeniowych.  Zobacz [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) Aby uzyskać szczegółowe informacje.|
|[Ograniczenia](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[executionInfo](#executionInfo)|Typ złożony|Zawiera informacje dotyczące wykonywania zadania tego typu.|

###  <a name="nodeInfo"></a> nodeInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|poolId|String|Identyfikator puli, na którym uruchomiono zadanie podrzędne.|
|nodeId|String|Identyfikator węzła, na którym uruchomiono zadanie podrzędne.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|numberOfInstances|Int32|Liczba węzłów obliczeń wymagana przez zadanie.|

###  <a name="constraints"></a> Ograniczenia

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maksymalna liczba przypadków, gdy zadanie może być ponawiane. Usługa partia zadań ponawia próbę zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy pamiętać, że ta wartość określa, w szczególności liczbę ponownych prób. Usługa Batch spróbuje zadanie raz, a może następnie podjąć kolejną próbę do tego limitu. Na przykład jeśli maksymalna liczba ponowień to 3, maksymalna liczba prób partii to zadanie maksymalnie 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponowień to 0, usługa partia zadań nie ponów próbę wykonania zadania.<br /><br /> Jeśli maksymalna liczba ponowień to -1, usługa partia zadań ponawia próbę zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (bez ponawiania).|


###  <a name="executionInfo"></a> executionInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|startTime|DateTime|Czas, w którym zadania został uruchomiony. "Uruchomiona" odnosi się do **systemem** stanu, więc jeśli zadanie Określa pliki zasobów lub pakiety aplikacji, następnie czas rozpoczęcia uwzględnia czas, w którym zadanie zostanie uruchomione, pobierania lub ich wdrażania.  Jeśli zadanie został ponownie uruchomiony lub ponowione, to jest ostatni czas, w którym zadania został uruchomiony.|
|endTime|DateTime|Czas, w którym zadanie jest ukończone.|
|ExitCode|Int32|Kod zakończenia zadania.|
|retryCount|Int32|Liczba przypadków, gdy zadanie było ponawiane przez usługę Batch. Zadanie zostanie ponowiony, jeśli kończy działanie z kodem zakończenia różny od zera, aż określony MaxTaskRetryCount.|
|requeueCount|Int32|Liczba przypadków, gdy zadanie ma zostać ponownie umieszczone w kolejce przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy węzły Usuwa użytkownika z puli (przez zmianę rozmiaru lub zmniejszania puli) lub gdy zadanie jest wyłączone, użytkownik może określić, czy uruchamianie zadań w węzłach można ponownie umieszczone w kolejce do wykonania. Ta liczba śledzi, ile razy zadanie ma zostać ponownie umieszczone w kolejce z tych powodów.|

<!-- Update_Description: update metedata properties -->