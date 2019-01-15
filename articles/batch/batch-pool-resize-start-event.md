---
title: Zdarzenie początkowe Azure zmiany rozmiaru puli usługi Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie rozpoczęcia zmiany rozmiaru puli usługi Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 3cabf18cfd771151e62d64dc1d2b47b250ac5471
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258275"
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowane po rozpoczęciu rozmiaru puli. Ponieważ zmiany rozmiaru puli jest zdarzenie asynchroniczne, można oczekiwać, że zdarzenie zakończenia zmiany rozmiaru puli być emitowane po zakończeniu operacji zmiany rozmiaru.

 Poniższy przykład pokazuje treści zdarzenie rozpoczęcia zmiany rozmiaru puli, pula zmiany rozmiaru z zakresu od 0 do 2 węzły za pomocą ręcznej zmiany rozmiaru.

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

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|poolId|Ciąg|Identyfikator puli.|
|nodeDeallocationOption|Ciąg|Określa, kiedy węzłów może zostać usunięty z puli, jeśli zmniejsza się rozmiar puli.<br /><br /> Możliwe wartości:<br /><br /> **ponownego dodania do kolejki** — Zakończ uruchomione zadania podrzędne i umieścić je. Zadania zostaną uruchomione ponownie, gdy zadanie jest włączone. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **Zakończenie** — Zakończ uruchomione zadania podrzędne. Zadania nie zostaną uruchomione ponownie. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **taskcompletion** — Zezwalaj na aktualnie wykonywanych zadań do wykonania. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wszystkie zadania zostały ukończone.<br /><br /> **Retaineddata** — Zezwalaj na aktualnie wykonywanych zadań do wykonania, a następnie zaczekaj na zakończenie wszystkich zadań okresów przechowywania danych wygaśnie. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wygasły wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to ponownego dodania do kolejki.<br /><br /> Jeśli jest zwiększenie rozmiaru puli, a następnie wartość jest równa **nieprawidłowy**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych, które aktualnie przypisana do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|wartość logiczna|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|isAutoPool|wartość logiczna|Określa, czy pula została utworzona za pośrednictwem mechanizmu AutoPool zadania.|
