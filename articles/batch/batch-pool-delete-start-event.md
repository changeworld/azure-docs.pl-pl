---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia uruchamiania usuwania puli usługi Batch.
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
ms.openlocfilehash: 65d20f2194b2bf83ecf32e19c1ab5b0f7bc7a004
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323212"
---
# <a name="pool-delete-start-event"></a>Zdarzenie rozpoczęcia usuwania puli

 To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli. Ponieważ usunięcie puli jest zdarzeniem asynchronicznym, można oczekiwać, że po zakończeniu operacji usuwania zostanie wyemitowane zdarzenie ukończenia usuwania puli.

 Poniższy przykład pokazuje treść zdarzenia uruchamiania puli Usuń.

```
{
    "id": "myPool1"
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|id|Ciąg|Identyfikator puli.|