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
ms.openlocfilehash: 76f3a15d88166160f28461c34ee03ff7c8880a59
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258416"
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
|`id`|String|Identyfikator puli.|
