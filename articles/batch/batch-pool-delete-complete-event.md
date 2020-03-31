---
title: Usuwanie pełnego zdarzenia puli usługi Azure Batch
description: Odwołanie do puli partii usuń pełne zdarzenie. To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023620"
---
# <a name="pool-delete-complete-event"></a>Zdarzenie zakończenia usuwania puli

 To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.

 W poniższym przykładzie przedstawiono treść puli usuń pełne zdarzenie.

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
|`startTime`|DateTime|Rozpoczął się czas usuwania puli.|
|`endTime`|DateTime|Czas usunięcia puli ukończony.|

## <a name="remarks"></a>Uwagi
Aby uzyskać więcej informacji o stanach i kodach błędów dla operacji ponownego rozmiaru puli, zobacz [Usuwanie puli z konta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
