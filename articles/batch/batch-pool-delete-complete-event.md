---
title: Usługa Azure Batch pool Usuń zdarzenie ukończenia | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca puli usługi Batch, Usuń zdarzenie ukończenia.
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
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775767"
---
# <a name="pool-delete-complete-event"></a>Zdarzenie zakończenia usuwania puli

 To zdarzenie jest emitowane po ukończeniu operacji usuwania puli.

 Poniższy przykład pokazuje treści zdarzenie zakończenia usuwania puli.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|id|String|Identyfikator puli.|
|startTime|DateTime|Czas usunięcia puli pracę.|
|endTime|DateTime|Godzina usunięcia puli ukończenia.|

## <a name="remarks"></a>Uwagi
Aby uzyskać więcej informacji na temat stanów i kody błędów dla operacji zmiany rozmiaru puli, zobacz [usunąć pulę z konta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).