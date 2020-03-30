---
title: Schematy dzienników diagnostycznych usługi Azure Media Services — Azure
description: W tym artykule przedstawiono schematy dzienników diagnostycznych usługi Azure Media Services.
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750876"
---
# <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Można monitorować dzienniki diagnostyczne usługi Media Services i tworzyć alerty i powiadomienia dla zebranych metryk i dzienników. Dzienniki można wysyłać do [usługi Azure Storage,](https://azure.microsoft.com/services/storage/)przesyłać strumieniowo do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportować je do [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/)lub korzystać z usług innych firm.

Aby uzyskać szczegółowe informacje, zobacz [Metryki usługi Azure Monitor](../../azure-monitor/platform/data-platform.md) i [dzienniki diagnostyczne usługi Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md).

W tym artykule opisano schematy dzienników diagnostycznych usługi Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych najwyższego poziomu

Aby uzyskać szczegółowy opis schematu dzienników diagnostycznych najwyższego poziomu, zobacz [Obsługiwane usługi, schematy i kategorie dzienników diagnostycznych platformy Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schemat dziennika dostarczania kluczy

### <a name="properties"></a>Właściwości

Te właściwości są specyficzne dla schematu dziennika dostarczania klucza.

|Nazwa|Opis|
|---|---|
|keyId (ida)|Identyfikator żądanego klucza.|
|Keytype|Może to być jedna z następujących wartości: "Wyczyść" (bez szyfrowania), "FairPlay", "PlayReady" lub "Widevine".|
|policyName|Nazwa usługi Azure Resource Manager zasad.|
|Tokentype|Typ tokenu.|
|statusMessage|Komunikat o stanie.|

### <a name="examples"></a>Przykłady

Właściwości schematu żądań dostarczania kluczy.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

[Monitorowanie metryk i dzienników diagnostycznych usługi Media Services](media-services-metrics-diagnostic-logs.md)
