---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 9ad721be84263969a91b8c27c349676be35ec663
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019104"
---
Dodaj następujący blok kodu wewnątrz klasy Program:

```csharp
public struct Response
{
    public HttpResponseHeaders headers;
    public string response;

    public Response(HttpResponseHeaders headers, string response)
    {
        this.headers = headers;
        this.response = response;
    }
}

static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```