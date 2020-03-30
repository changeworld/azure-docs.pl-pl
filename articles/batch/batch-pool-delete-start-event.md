---
title: Zdarzenie rozpoczęcia usuwania puli usługi Azure Batch
description: Odwołanie do zdarzenia rozpoczęcia usuwania puli partii. To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022209"
---
# <a name="pool-delete-start-event"></a>Zdarzenie rozpoczęcia usuwania puli

 To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli. Ponieważ usuwanie puli jest zdarzeniem asynchronialnym, można oczekiwać, że zdarzenie complete usuwania puli zostanie wyemitowane po zakończeniu operacji usuwania.

 W poniższym przykładzie przedstawiono treść zdarzenia rozpoczęcia usuwania puli.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
