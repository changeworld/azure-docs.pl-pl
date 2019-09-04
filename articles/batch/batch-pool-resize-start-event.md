---
title: Zdarzenie rozpoczęcia zmiany rozmiaru puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia rozpoczęcia zmiany rozmiaru puli usługi Batch.
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
ms.openlocfilehash: 965c1181399b76523b624d53dc47e59de0208ecb
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258262"
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowane po rozpoczęciu zmiany rozmiaru puli. Ponieważ zmiana rozmiaru puli jest zdarzeniem asynchronicznym, można oczekiwać, że zdarzenie ukończenia zmiany rozmiaru puli będzie emitowane po zakończeniu operacji zmiany rozmiaru.

 W poniższym przykładzie pokazano treść puli zmiana rozmiaru zdarzenia uruchomienia dla puli zmiana rozmiaru z 0 na 2 węzły z ręczną zmianę rozmiaru.

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

|Element|Type|Uwagi|
|-------------|----------|-----------|
|`id`|String|Identyfikator puli.|
|`nodeDeallocationOption`|String|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości to:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|`currentDedicatedNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetDedicatedNodes`|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|`enableAutoScale`|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|`isAutoPool`|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
