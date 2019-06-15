---
title: Zdarzenie początkowe Azure zmiany rozmiaru puli usługi Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie rozpoczęcia zmiany rozmiaru puli usługi Batch.
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
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60774237"
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
|poolId|String|Identyfikator puli.|
|nodeDeallocationOption|String|Określa, kiedy węzłów może zostać usunięty z puli, jeśli zmniejsza się rozmiar puli.<br /><br /> Możliwe wartości:<br /><br /> **ponownego dodania do kolejki** — Zakończ uruchomione zadania podrzędne i umieścić je. Zadania zostaną uruchomione ponownie, gdy zadanie jest włączone. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **Zakończenie** — Zakończ uruchomione zadania podrzędne. Zadania nie zostaną uruchomione ponownie. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **taskcompletion** — Zezwalaj na aktualnie wykonywanych zadań do wykonania. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wszystkie zadania zostały ukończone.<br /><br /> **Retaineddata** — Zezwalaj na aktualnie wykonywanych zadań do wykonania, a następnie zaczekaj na zakończenie wszystkich zadań okresów przechowywania danych wygaśnie. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wygasły wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to ponownego dodania do kolejki.<br /><br /> Jeśli jest zwiększenie rozmiaru puli, a następnie wartość jest równa **nieprawidłowy**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych, które aktualnie przypisana do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|isAutoPool|Bool|Określa, czy pula została utworzona za pośrednictwem mechanizmu AutoPool zadania.|
