---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzenie rozpoczęcia usuwania puli usługi Batch.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
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

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|id|String|Identyfikator puli.|