---
title: Zdarzenie ukończenia usuwania puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia ukończenia usuwania puli usługi Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 9bf50bd19ca3f4316c4c2ddbcdd3333745ebefd7
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258565"
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

|Element|Type|Uwagi|
|-------------|----------|-----------|
|`id`|String|Identyfikator puli.|
|`startTime`|DateTime|Godzina, o której rozpoczęto Usuwanie puli.|
|`endTime`|DateTime|Godzina, o której ukończono Usuwanie puli.|

## <a name="remarks"></a>Uwagi
Aby uzyskać więcej informacji o stanach i kodach błędów dla operacji zmiany rozmiaru puli, zobacz [Usuwanie puli z konta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
