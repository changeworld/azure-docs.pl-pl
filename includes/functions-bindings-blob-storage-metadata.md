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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69642138"
---
Wyzwalacz obiektu blob udostępnia kilka właściwości metadanych. Te właściwości mogą służyć jako część wyrażeń wiązania w innych powiązań lub jako parametry w kodzie. Te wartości mają taką samą semantyką jak [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) typu.

|Właściwość  |Typ  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalającego obiektu blob.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu blob dla lokalizacji podstawowej.|
|`Properties` |[Właściwości obiektów BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Właściwości systemu obiektu blob. |
|`Metadata` |`IDictionary<string,string>`|Metadane zdefiniowane przez użytkownika dla obiektu blob.|

Na przykład następujące przykłady skryptu Języka C# i JavaScript rejestrują ścieżkę do wyzwalającego obiektu blob, w tym kontenera:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
