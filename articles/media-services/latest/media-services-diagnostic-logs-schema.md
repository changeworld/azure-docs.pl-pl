---
title: Usługa Azure Media Services diagnostycznych dzienników schematów - Azure
description: Ten artykuł zawiera schematy dzienników diagnostycznych usługi Azure Media Services.
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
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850633"
---
# <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Można monitorować dzienników diagnostycznych usługi Media Services i tworzyć alerty i powiadomienia dotyczące zebranych metryk i dzienników. Możesz wysłać dzienniki do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), ich do usługi stream [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i wyeksportować je do [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/), lub użyj 3 usługi innych firm.

Aby uzyskać szczegółowe informacje, zobacz [metryk usługi Azure Monitor](../../azure-monitor/platform/data-platform.md) i [dzienników diagnostycznych platformy Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

W tym artykule opisano schematów dzienników diagnostycznych usługi Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat najwyższego poziomu dzienników diagnostycznych

Aby uzyskać szczegółowy opis schematu najwyższego poziomu dzienników diagnostycznych, zobacz [obsługiwane usługi, schematów i kategorie dla dzienników diagnostycznych usługi Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schemat dziennika dostarczania kluczy

### <a name="properties"></a>Właściwości

Te właściwości są specyficzne dla schematu dziennika dostarczania kluczy.

|Name (Nazwa)|Opis|
|---|---|
|keyId|Identyfikator klucza żądanej.|
|keyType|Może być jedną z następujących wartości: "Wyczyść" (bez szyfrowania), "FairPlay", "PlayReady" lub "Widevine".|
|policyName|Nazwa zasad usługi Azure Resource Manager.|
|tokenType|Typ tokenu.|
|Komunikat stanu|Komunikat o stanie.|

### <a name="examples"></a>Przykłady

Właściwości klucza dostawy schemat żądania.

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

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie metryk usług Media Services i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md)
