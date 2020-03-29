---
title: Niestandardowe schematy śledzenia wiadomości B2B
description: Tworzenie niestandardowych schematów śledzenia w celu monitorowania wiadomości B2B w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903053"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Tworzenie niestandardowych schematów śledzenia monitorujących kompleksowe przepływy pracy w usłudze Azure Logic A

Usługa Azure Logic Apps ma wbudowane śledzenie, które można włączyć dla części przepływu pracy. Można jednak skonfigurować niestandardowe śledzenie, które rejestruje zdarzenia od początku do końca przepływów pracy, na przykład przepływy pracy, które zawierają aplikację logiki, BizTalk Server, SQL Server lub dowolną inną warstwę. Ten artykuł zawiera niestandardowy kod, którego można używać w warstwach poza aplikacją logiki.

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
         "repeatItemIndex": ,
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
         "record": {}
      }
   ]
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| Sourcetype | Tak | Ciąg | Typ źródła uruchomienia z tymi dozwolonymi wartościami: `Microsoft.Logic/workflows`,`custom` |
| source | Tak | Ciąg lub JToken | Jeśli typem `Microsoft.Logic/workflows`źródła jest , informacje źródłowe muszą być zgodne z tym schematem. Jeśli typem `custom`źródłowym jest schemat jest JToken. |
| systemId | Tak | Ciąg | Identyfikator systemu aplikacji logiki |
| runId (ida) | Tak | Ciąg | Identyfikator uruchamiania aplikacji logiki |
| operationName | Tak | Ciąg | Nazwa operacji, na przykład akcja lub wyzwalacz |
| repeatItemScopeName | Tak | Ciąg | Powtórz nazwę elementu, jeśli `foreach`akcja `until` znajduje się wewnątrz lub pętli |
| powtarzalitemIndex | Tak | Liczba całkowita | Wskazuje, że akcja znajduje `foreach` `until` się wewnątrz lub pętli i jest powtarzającym się numerem indeksu elementu. |
| identyfikator śledzenia | Nie | Ciąg | Identyfikator śledzenia w celu skorelowania wiadomości |
| correlationId | Nie | Ciąg | Identyfikator korelacji w celu skorelowania komunikatów |
| identyfikator żądania klienta | Nie | Ciąg | Klient może wypełnić tę właściwość, aby skorelować wiadomości |
| Eventlevel | Tak | Ciąg | Poziom wydarzenia |
| eventTime | Tak | DateTime | Czas wydarzenia w formacie UTC: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | Tak | Ciąg | Typ rekordu toru tylko z tą dozwoloną wartością:`custom` |
| rejestrowanie | Tak | JToken (JToken) | Niestandardowy typ rekordu tylko w formacie JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołów B2B

Aby uzyskać informacje na temat schematów śledzenia protokołów B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [monitorowaniu komunikatów B2B za pomocą dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)