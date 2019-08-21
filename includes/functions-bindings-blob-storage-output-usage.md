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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642099"
---
Aby napisać obiekty blob, można powiązać następujące typy:

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

<sup>1</sup> wymaga powiązania `direction` "in" w *funkcji Function. JSON* lub `FileAccess.Read` w bibliotece C# klas. Można jednak użyć obiektu kontenera, który środowisko uruchomieniowe zapewnia do wykonywania operacji zapisu, takich jak przekazywanie obiektów BLOB do kontenera.

<sup>2</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece C# klas.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

W funkcji asynchronicznej, użyj wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` typu lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](#trigger---concurrency-and-memory-usage) wcześniej w tym artykule.