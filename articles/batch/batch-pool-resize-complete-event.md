---
title: Ponowne rozmiary puli usługi Azure Batch
description: Odwołanie do zdarzenia complete complete puli partii. Zobacz przykład puli, która zwiększyła rozmiar i została ukończona pomyślnie.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022226"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowane, gdy rozmiar puli został ukończony lub nie powiódł się.

 W poniższym przykładzie przedstawiono treść puli rozmiar complete zdarzenia dla puli, która zwiększyła rozmiar i ukończone pomyślnie.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`nodeDeallocationOption`|Ciąg|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli maleje.<br /><br /> Możliwe wartości:<br /><br /> **requeue** — zakończ uruchamianie zadań i ponownie je wysuń w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie jest włączone. Usuń węzły, gdy tylko zadania zostały zakończone.<br /><br /> **terminate** — zakończenie wykonywania zadań. Zadania nie będą uruchamiane ponownie. Usuń węzły, gdy tylko zadania zostały zakończone.<br /><br /> **wykonanie zadań** — zezwalaj na wykonywanie aktualnie uruchomionych zadań. Nie planuj żadnych nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Dane zatrzymane** — zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie poczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Nie planuj żadnych nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartością domyślną jest ponowne wysunienie.<br /><br /> Jeśli rozmiar puli wzrasta, wartość jest ustawiona na **nieprawidłową**.|
|`currentDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są wymagane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie aktualnie przypisanych do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są wymagane dla puli.|
|`enableAutoScale`|Wartość logiczna|Określa, czy rozmiar puli jest automatycznie dostosowywany w czasie.|
|`isAutoPool`|Wartość logiczna|Określa, czy pula została utworzona za pomocą mechanizmu Autopool zadania.|
|`startTime`|DateTime|Czas rozpoczęcia rozmiaru puli.|
|`endTime`|DateTime|Czas, w której rozmiar puli został ukończony.|
|`resultCode`|Ciąg|Wynik rozmiaru.|
|`resultMessage`|Ciąg| Szczegółowy komunikat o wyniku.<br /><br /> Jeśli rozmiar został pomyślnie ukończony, stwierdza, że operacja powiodła się.|
