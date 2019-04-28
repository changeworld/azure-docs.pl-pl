---
title: Niestandardowe śledzenie schematów na potrzeby obsługi wiadomości B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz schematy śledzenia niestandardowych, które monitorowanie komunikatów B2B w kont integracji dla usługi Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: f919e9a7cca210fa5920bcc6bed05a9a41fba8bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847190"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Utwórz schematy śledzenia niestandardowych, które monitorują end-to-end przepływów w usłudze Azure Logic Apps

Ma wbudowanych, śledzenie, możesz włączyć dla różnych części przepływu pracy business-to-business, takich jak śledzenia AS2 lub X12 wiadomości. Podczas tworzenia przepływów pracy zawiera aplikację logiki, programu BizTalk Server, SQL Server lub inną warstwę, a następnie można włączyć śledzenia niestandardowego, który będzie rejestrował zdarzenia od początku do końca przepływu pracy. 

Ten artykuł zawiera kod niestandardowy, który można użyć w warstwach poza aplikację logiki. 

## <a name="custom-tracking-schema"></a>Niestandardowy schemat śledzenia

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| sourceType |   | Typ uruchamiania źródła. Dozwolone wartości to **Microsoft.Logic/workflows** i **niestandardowe**. (Obowiązkowe) |
| Element źródłowy |   | Jeśli typ źródła jest **Microsoft.Logic/workflows**, informacje o źródle musi wykonać tego schematu. Jeśli typ źródła jest **niestandardowe**, schemat jest JToken. (Obowiązkowe) |
| systemId | String | Identyfikator logiki aplikacji systemu. (Obowiązkowe) |
| runId | String | Identyfikator przebiegu aplikacji logiki (Obowiązkowe) |
| operationName | String | Nazwa operacji (na przykład akcję lub wyzwalacz). (Obowiązkowe) |
| repeatItemScopeName | String | Powtórz nazwa elementu, jeśli akcja znajduje się wewnątrz `foreach` / `until` pętli. (Obowiązkowe) |
| repeatItemIndex | Liczba całkowita | Czy akcja jest wewnątrz `foreach` / `until` pętli. Wskazuje indeks elementu powtarzanego. (Obowiązkowe) |
| trackingId | String | Identyfikator śledzenia: korelowanie komunikatów. (Opcjonalnie) |
| correlationId | String | Identyfikator korelacji, aby skorelować komunikaty. (Opcjonalnie) |
| clientRequestId | String | Klienta można wypełnić go do skorelowania wiadomości. (Opcjonalnie) |
| eventLevel |   | Poziom zdarzenia. (Obowiązkowe) |
| eventTime |   | Czas w UTC w formacie RRRR-MM-DDTHH:MM:SS.00000Z wydarzenia. (Obowiązkowe) |
| recordType |   | Typ rekordu śledzenia. Dozwolona wartość to **niestandardowe**. (Obowiązkowe) |
| rekord |   | Typ rekordu niestandardowego. Dozwolony format to JToken. (Obowiązkowe) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacji na temat protokołu B2B schematy śledzenia zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md)
* Dowiedz się więcej o [śledzenie komunikatów B2B w dziennikach w usłudze Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)