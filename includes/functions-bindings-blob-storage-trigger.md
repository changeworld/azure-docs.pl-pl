---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642164"
---
Dla wyzwalanego obiektu BLOB można użyć następujących typów parametrów:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO możliwy do serializacji jako kod JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece C# klas.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string`, `Byte[]`lub poco jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` typu lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](#trigger---concurrency-and-memory-usage) w dalszej części tego artykułu.