---
title: Importowanie środowiska listonosza dla wywołań REST usługi Azure Media Services
description: Ten temat zawiera definicję środowiska Postman dla usługi Azure Media Services REST wywołania.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8254d121c62a20de0a1593920b7793195f8eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926711"
---
# <a name="import-the-postman-environment"></a>Importowanie środowiska Listonosza  

Ten artykuł zawiera definicję zmiennych środowiskowych **listonosza,** które są używane [w kolekcji Postman,](postman-collection.md) która zawiera zgrupowane żądania HTTP, które wywołują interfejsy API REST usługi Media Services. Pliki środowiska i kolekcji są używane przez [konfigurowanie listonosza dla interfejsu API usługi Media Services REST wywołuje](media-rest-apis-with-postman.md) samouczek.

> [!NOTE]
> Wartość . `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` Aby uzyskać identyfikator dzierżawy, możesz najechać kursorem myszy na nazwę użytkownika w portalu (w prawym górnym rogu) i będzie znajdować się w katalogu: Microsoft ( {{TENANTID}} ).

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
