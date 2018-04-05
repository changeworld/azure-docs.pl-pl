---
title: Azure zmiany rozmiaru puli partii rozpoczęcia zdarzenia | Dokumentacja firmy Microsoft
description: Informacje dotyczące zmiany rozmiaru puli partii rozpoczęcia zdarzenia.
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
ms.openlocfilehash: b4412764c313669dc154fccaa56f22417262994d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowany rozpoczęcie rozmiaru puli. Ponieważ zmiany rozmiaru puli jest zdarzenie asynchroniczne, można oczekiwać, że zdarzenie ukończenia zmiany rozmiaru puli na obliczanie po zakończeniu operacji zmiany rozmiaru.

 W poniższym przykładzie przedstawiono treści zdarzenia rozpoczęcia zmiany rozmiaru puli do puli zmiany rozmiaru z zakresu od 0 do 2 węzłów z ręcznej zmiany rozmiaru.

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
|nodeDeallocationOption|Ciąg|Określa, kiedy można usunąć węzłów z puli, jeśli rozmiar puli będzie zmniejszany.<br /><br /> Możliwe wartości:<br /><br /> **requeue** — Przerwij wykonywane zadania i requeue je. Zadania zostaną ponownie uruchomione, gdy zadanie jest włączone. Usuń węzły zaraz po zakończeniu zadania.<br /><br /> **przerwanie** — przerwania uruchomionych zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadania.<br /><br /> **taskcompletion** — Zezwalaj na aktualnie wykonywanych zadań do wykonania. Nie planuj nowych zadań czasu oczekiwania. Usuń węzły, po zakończeniu wykonywania wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na aktualnie wykonywanych zadań do wykonania, a następnie poczekaj na zakończenie wszystkich zadań okresów przechowywania danych wygaśnie. Nie planuj nowych zadań czasu oczekiwania. Usuń węzły, gdy wygasły wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> Jeśli jest zwiększenie rozmiaru puli, wartość jest równa **nieprawidłowy**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych aktualnie przypisane do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|Wartość logiczna|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|isAutoPool|Wartość logiczna|Speficies czy puli został utworzony za pomocą mechanizmu AutoPool zadania.|
