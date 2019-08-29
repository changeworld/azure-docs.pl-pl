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
ms.openlocfilehash: 668d686f296763f5ed51dc6361691352d22e0738
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094718"
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowane po rozpoczęciu zmiany rozmiaru puli. Ponieważ zmiana rozmiaru puli jest zdarzeniem asynchronicznym, można oczekiwać, że zdarzenie ukończenia zmiany rozmiaru puli będzie emitowane po zakończeniu operacji zmiany rozmiaru.

 W poniższym przykładzie pokazano treść puli zmiana rozmiaru zdarzenia uruchomienia dla puli zmiana rozmiaru z 0 na 2 węzły z ręczną zmianę rozmiaru.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|poolId|String|Identyfikator puli.|
|nodeDeallocationOption|String|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości to:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|enableAutoScale|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|isAutoPool|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
