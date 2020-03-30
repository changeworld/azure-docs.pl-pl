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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202101"
---
Można powiązać z następującymi typami, aby napisać obiekty blob:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Wymaga powiązania `direction` "w" w `FileAccess.Read` *function.json* lub w bibliotece klasy C#. Jednak można użyć obiektu kontenera, który środowisko wykonawcze zapewnia do wykonywania operacji zapisu, takich jak przekazywanie obiektów blob do kontenera.

<sup>2</sup> Wymaga powiązania `direction` "inout" w `FileAccess.ReadWrite` *function.json* lub w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednym z typów SDK magazynu i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

W funkcjach asynchronizowanych `IAsyncCollector` należy użyć `out` wartości zwracanej lub zamiast parametru.

Powiązanie `string` `Byte[]` lub jest zalecane tylko wtedy, gdy rozmiar obiektu blob jest mały, ponieważ cała zawartość obiektu blob są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się `Stream` `CloudBlockBlob` użycie typu lub. Aby uzyskać więcej informacji, zobacz [współbieżność i użycie pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) wcześniej w tym artykule.
