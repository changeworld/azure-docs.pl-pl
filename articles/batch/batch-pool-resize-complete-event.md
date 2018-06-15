---
title: Zdarzenie ukończenia zmiany rozmiaru puli usługi partia zadań Azure | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca puli partii resize zdarzenie ukończenia.
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
ms.openlocfilehash: e91ba664a69d28cae1f82710d427bd2a391305a2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30315665"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowany podczas zmiany rozmiaru puli zakończone lub nie powiodło się.

 W poniższym przykładzie przedstawiono treści zdarzenie ukończenia zmiany rozmiaru puli dla puli, zwiększyć rozmiar, która została ukończona pomyślnie.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|id|Ciąg|Identyfikator puli.|
|nodeDeallocationOption|Ciąg|Określa, kiedy można usunąć węzłów z puli, jeśli rozmiar puli będzie zmniejszany.<br /><br /> Możliwe wartości:<br /><br /> **requeue** — Przerwij wykonywane zadania i requeue je. Zadania zostaną ponownie uruchomione, gdy zadanie jest włączone. Usuń węzły zaraz po zakończeniu zadania.<br /><br /> **przerwanie** — przerwania uruchomionych zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadania.<br /><br /> **taskcompletion** — Zezwalaj na aktualnie wykonywanych zadań do wykonania. Nie planuj nowych zadań czasu oczekiwania. Usuń węzły, po zakończeniu wykonywania wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na aktualnie wykonywanych zadań do wykonania, a następnie poczekaj na zakończenie wszystkich zadań okresów przechowywania danych wygaśnie. Nie planuj nowych zadań czasu oczekiwania. Usuń węzły, gdy wygasły wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> Jeśli jest zwiększenie rozmiaru puli, wartość jest równa **nieprawidłowy**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych aktualnie przypisane do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|Wartość logiczna|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|isAutoPool|Wartość logiczna|Określa, czy pula został utworzony za pomocą mechanizmu AutoPool zadania.|
|startTime|DateTime|Rozpoczęto podczas zmiany rozmiaru puli.|
|endTime|DateTime|Podczas zmiany rozmiaru puli ukończone.|
|resultCode|Ciąg|Wynik zmiany rozmiaru.|
|resultMessage|Ciąg|Błąd podczas zmiany rozmiaru zawiera szczegóły wyniku.<br /><br /> Jeśli rozmiar ukończone pomyślnie go stwierdza, że operacja zakończyła się pomyślnie.|