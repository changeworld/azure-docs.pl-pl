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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202114"
---
Dla wyzwalającego obiektu blob można użyć następujących typów parametrów:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Serializacja POCO jako JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Wymaga powiązania `direction` "inout" w `FileAccess.ReadWrite` *function.json* lub w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednym z typów SDK magazynu i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Powiązanie `string` `Byte[]`z programem , lub POCO jest zalecane tylko wtedy, gdy rozmiar obiektu blob jest mały, ponieważ cała zawartość obiektu blob jest ładowana do pamięci. Ogólnie rzecz biorąc, zaleca się `Stream` `CloudBlockBlob` użycie typu lub. Aby uzyskać więcej informacji, zobacz [współbieżność i użycie pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) w dalszej części tego artykułu.