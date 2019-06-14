---
title: Usługa Azure zdarzenie ukończenia zadania usługi Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie ukończenia zadania usługi Batch.
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
ms.openlocfilehash: b5fd1a8020c8e95323bc2333c0583dafe58e8456
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549975"
---
# <a name="task-complete-event"></a>Zdarzenie ukończenia zadania

 To zdarzenie jest emitowane, gdy zadanie jest ukończone, niezależnie od tego, kod zakończenia. To zdarzenie może służyć do ustalenia czasu trwania zadania, w przypadku, gdy uruchomiono zadanie podrzędne i czy jej prób ponownego wykonania.


 Poniższy przykład pokazuje treści zdarzenie ukończenia zadania.

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

|Nazwa elementu|Typ|Uwagi|
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

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|poolId|String|Identyfikator puli, na którym uruchomiono zadanie podrzędne.|
|nodeId|String|Identyfikator węzła, na którym uruchomiono zadanie podrzędne.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|numberOfInstances|Int32|Liczba węzłów obliczeń wymagana przez zadanie.|

###  <a name="constraints"></a> Ograniczenia

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maksymalna liczba przypadków, gdy zadanie może być ponawiane. Usługa partia zadań ponawia próbę zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy pamiętać, że ta wartość określa, w szczególności liczbę ponownych prób. Usługa Batch spróbuje zadanie raz, a może następnie podjąć kolejną próbę do tego limitu. Na przykład jeśli maksymalna liczba ponowień to 3, maksymalna liczba prób partii to zadanie maksymalnie 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponowień to 0, usługa partia zadań nie ponów próbę wykonania zadania.<br /><br /> Jeśli maksymalna liczba ponowień to -1, usługa partia zadań ponawia próbę zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (bez ponawiania).|

###  <a name="executionInfo"></a> executionInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|startTime|DateTime|Czas, w którym zadania został uruchomiony. "Uruchomiona" odnosi się do **systemem** stanu, więc jeśli zadanie Określa pliki zasobów lub pakiety aplikacji, następnie czas rozpoczęcia uwzględnia czas, w którym zadanie zostanie uruchomione, pobierania lub ich wdrażania.  Jeśli zadanie został ponownie uruchomiony lub ponowione, to jest ostatni czas, w którym zadania został uruchomiony.|
|endTime|DateTime|Czas, w którym zadanie jest ukończone.|
|ExitCode|Int32|Kod zakończenia zadania.|
|retryCount|Int32|Liczba przypadków, gdy zadanie było ponawiane przez usługę Batch. Zadanie zostanie ponowiony, jeśli kończy działanie z kodem zakończenia różny od zera, aż określony MaxTaskRetryCount.|
|requeueCount|Int32|Liczba przypadków, gdy zadanie ma zostać ponownie umieszczone w kolejce przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy węzły Usuwa użytkownika z puli (przez zmianę rozmiaru lub zmniejszania puli) lub gdy zadanie jest wyłączone, użytkownik może określić, czy uruchamianie zadań w węzłach można ponownie umieszczone w kolejce do wykonania. Ta liczba śledzi, ile razy zadanie ma zostać ponownie umieszczone w kolejce z tych powodów.|
