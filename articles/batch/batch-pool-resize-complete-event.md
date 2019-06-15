---
title: Usługa Azure Batch pool rozmiar zdarzenie ukończenia | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca puli usługi Batch, zmienić rozmiar zdarzenie ukończenia.
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
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776426"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowane podczas zmiany rozmiaru puli zostało ukończone lub nie powiodło się.

 Poniższy przykład pokazuje treści zdarzenie zakończenia zmiany rozmiaru puli dla puli, zwiększyć rozmiar, która została ukończona pomyślnie.

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
|id|String|Identyfikator puli.|
|nodeDeallocationOption|String|Określa, kiedy węzłów może zostać usunięty z puli, jeśli zmniejsza się rozmiar puli.<br /><br /> Możliwe wartości:<br /><br /> **ponownego dodania do kolejki** — Zakończ uruchomione zadania podrzędne i umieścić je. Zadania zostaną uruchomione ponownie, gdy zadanie jest włączone. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **Zakończenie** — Zakończ uruchomione zadania podrzędne. Zadania nie zostaną uruchomione ponownie. Usuwanie węzłów, tak szybko, jak zadania zostaną zakończone.<br /><br /> **taskcompletion** — Zezwalaj na aktualnie wykonywanych zadań do wykonania. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wszystkie zadania zostały ukończone.<br /><br /> **Retaineddata** — Zezwalaj na aktualnie wykonywanych zadań do wykonania, a następnie zaczekaj na zakończenie wszystkich zadań okresów przechowywania danych wygaśnie. Nie planuj nowych zadań podczas oczekiwania. Usuwanie węzłów, gdy wygasły wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to ponownego dodania do kolejki.<br /><br /> Jeśli jest zwiększenie rozmiaru puli, a następnie wartość jest równa **nieprawidłowy**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych, które aktualnie przypisana do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są wymagane dla puli.|
|enableAutoScale|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się wraz z upływem czasu.|
|isAutoPool|Bool|Określa, czy pula została utworzona za pośrednictwem mechanizmu AutoPool zadania.|
|startTime|DateTime|Podczas zmiany rozmiaru puli pracę.|
|endTime|DateTime|Podczas zmiany rozmiaru puli ukończone.|
|resultCode|String|Wynik zmiany rozmiaru.|
|resultMessage|String|Błąd dotyczący zmiany rozmiaru zawiera szczegółowe informacje o wyniku.<br /><br /> Jeśli rozmiar została ukończona pomyślnie go stwierdza, że operacja zakończyła się pomyślnie.|