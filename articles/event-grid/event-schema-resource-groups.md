---
title: Schemat zdarzeń grupy zasobów platformy Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzenia grupę zasobów za pomocą usługi Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: 006e1c88e10013085722927b8a9b909d98b89aae
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480169"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Schemat zdarzeń Azure Event Grid dla grup zasobów

Ten artykuł zawiera właściwości i schematu zdarzeń grupy zasobów. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Subskrypcje platformy Azure i grup zasobów emitują te same typy zdarzeń. Typy zdarzeń, które odnoszą się do zmian w zasobach. Główną różnicą jest, że grupy zasobów emitować zdarzenia dla zasobów w grupie zasobów i subskrypcji platformy Azure emitować zdarzenia dla zasobów w subskrypcji. 

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Grupy zasobów emitować zdarzenia zarządzania z usługi Azure Resource Manager, takie jak po utworzeniu maszyny Wirtualnej lub na konto magazynu zostało usunięte.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Wywoływane, gdy zasób utworzyć lub zaktualizować operacji powiedzie się. |
| Microsoft.Resources.ResourceWriteFailure | Wywoływane, gdy tworzenie zasobu lub operacja aktualizacji nie powiedzie się. |
| Microsoft.Resources.ResourceWriteCancel | Wywoływane, gdy zasób utworzyć lub zaktualizować operacji zostało anulowane. |
| Microsoft.Resources.ResourceDeleteSuccess | Wywoływane, gdy operacja usuwania zasobu zakończy się pomyślnie. |
| Microsoft.Resources.ResourceDeleteFailure | Wywoływane, gdy operacja usuwania zasobu nie powiodło się. |
| Microsoft.Resources.ResourceDeleteCancel | Wywoływane, gdy operacja usuwania zasobu zostało anulowane. To zdarzenie występuje, gdy wdrożenie szablonu zostanie anulowane. |

## <a name="example-event"></a>Przykład zdarzenia

W poniższym przykładzie przedstawiono schematu dla **ResourceWriteSuccess** zdarzeń. Ten sam schemat jest używany dla **ResourceWriteFailure** i **ResourceWriteCancel** zdarzenia z różnymi wartościami dla `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

W poniższym przykładzie przedstawiono schematu dla **ResourceDeleteSuccess** zdarzeń. Ten sam schemat jest używany dla **ResourceDeleteFailure** i **ResourceDeleteCancel** zdarzenia z różnymi wartościami dla `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia grupy zasobów. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Autoryzacja | obiekt | Żądana autoryzacji dla tej operacji. |
| oświadczenia | obiekt | Właściwości oświadczenia. Aby uzyskać więcej informacji, zobacz [specyfikacji JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | ciąg | Identyfikator operacji do rozwiązywania problemów. |
| httpRequest | obiekt | Szczegóły operacji. Ten obiekt jest tylko uwzględnione podczas aktualizowania istniejącego zasobu lub usunięcie zasobu. |
| resourceProvider | ciąg | Dostawca zasobów wykonującego operację. |
| resourceUri | ciąg | Identyfikator URI zasobów w ramach operacji. |
| operationName | ciąg | Operacja, która została wykonana. |
| status | ciąg | Stan operacji. |
| subscriptionId | ciąg | Identyfikator subskrypcji zasobu. |
| Identyfikator dzierżawy | ciąg | Identyfikator dzierżawy zasobu. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
