---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia uruchamiania usuwania puli usługi Batch.
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
ms.openlocfilehash: 6f8b1bdd680d43cb14707338d2e37e41114126b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094759"
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
|id|String|Identyfikator puli.|