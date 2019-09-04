---
title: Zdarzenie ukończenia zmiany rozmiaru puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia ukończenia zmiany rozmiaru puli usługi Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 8c0843db216ff99aabfda9074ee751597b43a2a2
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258403"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowane, gdy zmiana rozmiaru puli zakończyła się lub zakończyła się niepowodzeniem.

 Poniższy przykład pokazuje treść puli, która zmienia rozmiar dla puli, która zwiększyła rozmiar i została ukończona pomyślnie.

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

|Element|Type|Uwagi|
|-------------|----------|-----------|
|`id`|String|Identyfikator puli.|
|`nodeDeallocationOption`|String|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości to:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|`currentDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są obecnie przypisane do puli.|
|`targetDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są żądane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są obecnie przypisane do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są żądane dla puli.|
|`enableAutoScale`|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|`isAutoPool`|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
|`startTime`|DateTime|Godzina, o której zmiana rozmiaru puli została rozpoczęta.|
|`endTime`|DateTime|Godzina, o której zmiana rozmiaru puli została zakończona.|
|`resultCode`|String|Wynik zmiany rozmiaru.|
|`resultMessage`|String| Szczegółowy komunikat dotyczący wyniku.<br /><br /> Jeśli zmiana rozmiaru została zakończona pomyślnie, stwierdza, że operacja powiodła się.|
