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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202127"
---
Dla powiązania wejściowego obiektu blob można użyć następujących typów parametrów:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Wymaga powiązania `direction` "inout" w `FileAccess.ReadWrite` *function.json* lub w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednym z typów SDK magazynu i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Powiązanie `string` `Byte[]` lub jest zalecane tylko wtedy, gdy rozmiar obiektu blob jest mały, ponieważ cała zawartość obiektu blob są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się `Stream` `CloudBlockBlob` użycie typu lub. Aby uzyskać więcej informacji, zobacz [współbieżność i użycie pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) wcześniej w tym artykule.
