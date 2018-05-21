---
title: Schemat zdarzeń grupy zasobów Azure siatki zdarzeń
description: Opisuje właściwości, które są dostępne dla zdarzenia grupy zasobów Azure zdarzeń siatki
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 163c32bdb8a3fdc278404b9e26fdc3097797d16c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure schematu zdarzeń siatki zdarzeń dla grupy zasobów

Ten artykuł zawiera właściwości i schematu dla zdarzenia grupy zasobów. Aby obejrzeć wprowadzenie do schematów zdarzeń, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md).

Subskrypcje platformy Azure i grup zasobów wyemitować tych samych typów zdarzeń. Typy zdarzeń, które odnoszą się do zmian w zasobach. Podstawowa różnica polega na grupy zasobów Emituj zdarzenia dla zasobów w grupie zasobów, czy subskrypcje platformy Azure Emituj zdarzenia dla zasobów w subskrypcji. 

## <a name="available-event-types"></a>Typy dostępnych zdarzeń

Grupy zasobów Emituj zdarzeń zarządzania z usługi Azure Resource Manager, takie jak podczas tworzenia maszyny Wirtualnej lub na konto magazynu zostało usunięte.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Wywoływane, gdy zasób utworzyć ani zaktualizować operacji zakończy się pomyślnie. |
| Microsoft.Resources.ResourceWriteFailure | Wywoływane, gdy tworzenie zasobu lub operacja aktualizacji nie powiedzie się. |
| Microsoft.Resources.ResourceWriteCancel | Wywoływane, gdy zasób utworzyć ani zaktualizować operacji zostało anulowane. |
| Microsoft.Resources.ResourceDeleteSuccess | Wywoływane, gdy operację usuwania zasobu zakończy się pomyślnie. |
| Microsoft.Resources.ResourceDeleteFailure | Wywoływane, gdy operacja usuwania zasobu nie powiedzie się. |
| Microsoft.Resources.ResourceDeleteCancel | Wywoływane, gdy operacja usuwania zasobów została anulowana. To zdarzenie odbywa się podczas wdrażania szablonu zostaną anulowane. |

## <a name="example-event"></a>Przykład zdarzeń

W poniższym przykładzie przedstawiono schematu zasobu utworzone zdarzenie: 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

Schemat dla zdarzenia Usunięto zasób jest podobne:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalny. Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana wydawcy podmiotem zdarzeń. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia grupy zasobów. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Autoryzacji | ciąg | Autoryzacja żądanej operacji. |
| Oświadczenia | ciąg | Właściwości oświadczenia. |
| correlationId | ciąg | Identyfikator operacji do rozwiązywania problemów. |
| httpRequest | ciąg | Szczegóły operacji. |
| resourceProvider | ciąg | Dostawca zasobów, wykonywanie operacji. |
| resourceUri | ciąg | Identyfikator URI zasobu podczas operacji. |
| operationName | ciąg | Operacja, która została wykonana. |
| status | ciąg | Stan operacji. |
| subscriptionId | ciąg | Identyfikator subskrypcji zasobu. |
| Dla identyfikatora dzierżawcy | ciąg | Identyfikator dzierżawy zasobu. |

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do usługi Azure Event siatki, zobacz [co to jest zdarzenie siatki?](overview.md)
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
