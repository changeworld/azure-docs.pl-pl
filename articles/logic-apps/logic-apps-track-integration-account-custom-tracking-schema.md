---
title: Niestandardowe schematy śledzenia dla komunikatów B2B
description: Utwórz niestandardowe schematy śledzenia, które monitorują komunikaty B2B na kontach integracji dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792801"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Tworzenie niestandardowych schematów śledzenia, które monitorują kompleksowe przepływy pracy w Azure Logic Apps

Istnieje Wbudowana funkcja śledzenia, która umożliwia obsługę różnych części przepływu pracy między firmami, takich jak śledzenie komunikatów AS2 lub X12. Podczas tworzenia przepływów pracy, które obejmują aplikację logiki, BizTalk Server, SQL Server lub dowolnej innej warstwy, można włączyć śledzenie niestandardowe, które rejestruje zdarzenia od początku do końca przepływu pracy. 

Ten artykuł zawiera kod niestandardowy, którego można użyć w warstwach poza aplikacją logiki. 

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

| Właściwość | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| sourceType | Tak |   | Typ źródła przebiegu. Dozwolone wartości to **Microsoft. logiki/przepływy pracy** i **niestandardowe**. |
| source | Tak |   | Jeśli typem źródła jest **Microsoft. logiki/przepływy pracy**, informacje źródłowe muszą być zgodne z tym schematem. Jeśli typ źródła jest **niestandardowy**, schemat jest obiektem JToken. |
| systemId | Tak | Ciąg | Identyfikator systemu aplikacji logiki. |
| RunId | Tak | Ciąg | Identyfikator przebiegu aplikacji logiki. |
| operationName | Tak | Ciąg | Nazwa operacji (na przykład akcja lub wyzwalacz). |
| repeatItemScopeName | Tak | Ciąg | Powtórz nazwę elementu, jeśli akcja znajduje się w `foreach`/pętla `until`. |
| repeatItemIndex | Tak | Liczba całkowita | Czy akcja znajduje się wewnątrz pętli `until` `foreach`/. Wskazuje powtarzalny indeks elementu. |
| trackingId | Nie | Ciąg | Identyfikator śledzenia w celu skorelowania komunikatów. |
| correlationId | Nie | Ciąg | Identyfikator korelacji w celu skorelowania komunikatów. |
| Identyfikatorem żądania klienta | Nie | Ciąg | Klient może wypełnić go, aby skorelować komunikaty. |
| eventLevel | Tak |   | Poziom zdarzenia. |
| eventTime | Tak |   | Czas zdarzenia w formacie UTC RRRR-MM-DDTgg: MM: SS. 00000Z. |
| RecordType | Tak |   | Typ rekordu śledzenia. Dozwolona wartość jest **niestandardowa**. |
| rekord | Tak |   | Typ rekordu niestandardowego. Dozwolony format to JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [monitorowaniu komunikatów B2B](logic-apps-monitor-b2b-message.md)
* Informacje [na temat śledzenia komunikatów B2B w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
