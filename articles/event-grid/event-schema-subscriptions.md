---
title: Schemat zdarzeń subskrypcji w usłudze Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla subskrypcji zdarzeń za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561680"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Schemat zdarzeń Azure Event Grid dla subskrypcji

Ten artykuł zawiera właściwości i schematu dla zdarzeń dotyczących subskrypcji platformy Azure. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Subskrypcje platformy Azure i grup zasobów emitują te same typy zdarzeń. Typy zdarzeń, które odnoszą się do zmian zasobów lub akcji. Główną różnicą jest, że grupy zasobów emitować zdarzenia dla zasobów w grupie zasobów i subskrypcji platformy Azure emitować zdarzenia dla zasobów w subskrypcji.

Zasób zdarzenia są tworzone dla WPIS PUT, PATCH i Usuń operacje, które są wysyłane do `management.azure.com`. Pobierz operacje nie twórz zdarzeń. Wysłane do płaszczyzny danych operacje (takie jak `myaccount.blob.core.windows.net`) nie należy tworzyć zdarzenia. Zdarzenia działań udostępniają danych zdarzenia dla operacji takich jak lista kluczy dla zasobu.

Podczas subskrybowania zdarzeń dla subskrypcji platformy Azure do punktu końcowego odbiera wszystkie zdarzenia dla tej subskrypcji. Zdarzenia mogą obejmować zdarzenia, które chcesz wyświetlić, takie jak aktualizowanie maszynę wirtualną, ale także zdarzenia, które być może nie są szczególnie ważne, takie jak zapisywanie nowy wpis w historii wdrażania. Możesz otrzymywać wszystkie zdarzenia w punkcie końcowym usługi i napisać kod, który przetwarza zdarzenia, które mają być obsługiwane. Alternatywnie można zdefiniować filtr, podczas tworzenia subskrypcji zdarzeń.

Programowe obsługi zdarzeń, zdarzenia można sortować, analizując `operationName` wartość. Na przykład zdarzenie punktu końcowego może przetwarzać tylko zdarzenia dla operacji, które są równe `Microsoft.Compute/virtualMachines/write` lub `Microsoft.Storage/storageAccounts/write`.

Temat zdarzenia jest identyfikator zasobu, zasób, który jest miejscem docelowym operacji. Aby filtrować zdarzenia dla zasobu, należy podać tego zasobu IDENTYFIKATORA podczas tworzenia subskrypcji zdarzeń. Aby filtrować według typu zasobu, należy użyć wartości w następującym formacie: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń subskrypcji platformy Azure](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Subskrypcje platformy Azure emitować zdarzenia zarządzania z usługi Azure Resource Manager, takie jak po utworzeniu maszyny Wirtualnej lub na konto magazynu zostało usunięte.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Wywoływane, gdy akcja zasobu została anulowana. |
| Microsoft.Resources.ResourceActionFailure | Wywoływane, gdy akcja zasobu zakończy się niepowodzeniem. |
| Microsoft.Resources.ResourceActionSuccess | Wywoływane, gdy akcja zasobu zakończy się pomyślnie. |
| Microsoft.Resources.ResourceDeleteCancel | Wywołane, gdy usuwanie operacja została anulowana. To zdarzenie występuje, gdy wdrożenie szablonu zostanie anulowane. |
| Microsoft.Resources.ResourceDeleteFailure | Wywołane, gdy usunięcie operacji nie powiodło się. |
| Microsoft.Resources.ResourceDeleteSuccess | Wywołane, gdy operacja usuwania powiedzie się. |
| Microsoft.Resources.ResourceWriteCancel | Wywołane, gdy tworzenie lub operacja aktualizacji została anulowana. |
| Microsoft.Resources.ResourceWriteFailure | Wywołane, gdy tworzenie lub aktualizowanie operacja kończy się niepowodzeniem. |
| Microsoft.Resources.ResourceWriteSuccess | Wywołane, gdy tworzenie lub operacji aktualizacji zakończy się pomyślnie. |

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
  "topic": "/subscriptions/{subscription-id}"
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
  "topic": "/subscriptions/{subscription-id}"
}]
```

W poniższym przykładzie przedstawiono schematu dla **ResourceActionSuccess** zdarzeń. Ten sam schemat jest używany dla **ResourceActionFailure** i **ResourceActionCancel** zdarzenia z różnymi wartościami dla `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| topic | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzeń dla subskrypcji. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| authorization | obiekt | Żądana autoryzacji dla tej operacji. |
| claims | obiekt | Właściwości oświadczenia. Aby uzyskać więcej informacji, zobacz [specyfikacji JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | string | Identyfikator operacji do rozwiązywania problemów. |
| httpRequest | obiekt | Szczegóły operacji. Ten obiekt jest tylko uwzględnione podczas aktualizowania istniejącego zasobu lub usunięcie zasobu. |
| resourceProvider | string | Dostawca zasobów dla tej operacji. |
| resourceUri | string | Identyfikator URI zasobów w ramach operacji. |
| operationName | string | Operacja, która została wykonana. |
| status | string | Stan operacji. |
| subscriptionId | string | Identyfikator subskrypcji zasobu. |
| tenantId | string | Identyfikator dzierżawy zasobu. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
