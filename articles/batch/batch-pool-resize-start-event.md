---
title: Zdarzenie rozpoczęcia ponownego rozmiaru puli usługi Azure Batch
description: Odwołanie do zdarzenia rozpoczęcia ponownego rozmiaru puli partii. Przykład pokazuje treść zdarzenia rozpoczęcia zmiany rozmiaru puli dla zmiany rozmiaru puli z 0 do 2 węzłów z ręcznym rozmiarem.
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
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023586"
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowane po rozpoczęciu rozmiaru puli. Ponieważ rozmiar puli jest zdarzeniem asynchronialnym, można oczekiwać, że zdarzenie complete o rozmiarze rozmiaru puli zostanie wyemitowane po zakończeniu operacji ponownego rozmiaru.

 W poniższym przykładzie przedstawiono treść zdarzenia rozpoczęcia zmiany rozmiaru puli dla zmiany rozmiaru puli z 0 do 2 węzłów z ręcznym rozmiarem.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`nodeDeallocationOption`|Ciąg|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli maleje.<br /><br /> Możliwe wartości:<br /><br /> **requeue** — zakończ uruchamianie zadań i ponownie je wysuń w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie jest włączone. Usuń węzły, gdy tylko zadania zostały zakończone.<br /><br /> **terminate** — zakończenie wykonywania zadań. Zadania nie będą uruchamiane ponownie. Usuń węzły, gdy tylko zadania zostały zakończone.<br /><br /> **wykonanie zadań** — zezwalaj na wykonywanie aktualnie uruchomionych zadań. Nie planuj żadnych nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Dane zatrzymane** — zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie poczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Nie planuj żadnych nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartością domyślną jest ponowne wysunienie.<br /><br /> Jeśli rozmiar puli wzrasta, wartość jest ustawiona na **nieprawidłową**.|
|`currentDedicatedNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetDedicatedNodes`|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|`enableAutoScale`|Wartość logiczna|Określa, czy rozmiar puli jest automatycznie dostosowywany w czasie.|
|`isAutoPool`|Wartość logiczna|Określa, czy pula została utworzona za pomocą mechanizmu Autopool zadania.|
