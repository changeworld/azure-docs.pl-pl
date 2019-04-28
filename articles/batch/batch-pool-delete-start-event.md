---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie rozpoczęcia usuwania puli usługi Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774543"
---
# <a name="pool-delete-start-event"></a>Zdarzenie rozpoczęcia usuwania puli

 To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli. Usuwanie puli jest zdarzenie asynchroniczne, można oczekiwać, że zdarzenie zakończenia usuwania puli być emitowane po ukończeniu operacji usuwania.

 Poniższy przykład pokazuje treści zdarzenie rozpoczęcia usuwania puli.

```
{
    "id": "myPool1"
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|id|String|Identyfikator puli.|

<!-- Update_Description: update metedata properties -->