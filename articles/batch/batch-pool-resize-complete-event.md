---
title: Zdarzenie ukończenia zmiany rozmiaru puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia ukończenia zmiany rozmiaru puli usługi Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c2544bd2be683b731c3dac0bea651d4b64dff75e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323206"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowane, gdy zmiana rozmiaru puli zakończyła się lub zakończyła się niepowodzeniem.

 Poniższy przykład pokazuje treść puli, która zmienia rozmiar dla puli, która zwiększyła rozmiar i została ukończona pomyślnie.

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

|Element|Type|Uwagi|
|-------------|----------|-----------|
|id|Ciąg|Identyfikator puli.|
|nodeDeallocationOption|String|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości to:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|currentDedicated|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|targetDedicated|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|enableAutoScale|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|isAutoPool|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
|startTime|Datetime|Godzina, o której zmiana rozmiaru puli została rozpoczęta.|
|endTime|Datetime|Godzina, o której zmiana rozmiaru puli została zakończona.|
|resultCode|Ciąg|Wynik zmiany rozmiaru.|
|resultMessage|String|Błąd zmiany rozmiaru zawiera szczegóły wyniku.<br /><br /> Jeśli zmiana rozmiaru została zakończona pomyślnie, stwierdza, że operacja powiodła się.|