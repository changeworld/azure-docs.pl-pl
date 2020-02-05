---
title: Zdarzenie ukończenia usuwania puli Azure Batch
description: Odwołanie do zdarzenia ukończenia usuwania puli usługi Batch. To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.
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
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023620"
---
# <a name="pool-delete-complete-event"></a>Zdarzenie zakończenia usuwania puli

 To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.

 Poniższy przykład pokazuje treść zdarzenia ukończenia usuwania puli.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`startTime`|Data i godzina|Godzina, o której rozpoczęto Usuwanie puli.|
|`endTime`|Data i godzina|Godzina, o której ukończono Usuwanie puli.|

## <a name="remarks"></a>Uwagi
Aby uzyskać więcej informacji o stanach i kodach błędów dla operacji zmiany rozmiaru puli, zobacz [Usuwanie puli z konta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
