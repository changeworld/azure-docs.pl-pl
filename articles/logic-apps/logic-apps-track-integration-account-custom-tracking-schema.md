---
title: Śledzenie niestandardowych schematów B2B monitorowania - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz schematy śledzenia niestandardowych do monitorowania wiadomości B2B transakcji na koncie Azure integracji.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 431235370c52be4c6e1ad6cd1af6a412e9eac230
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299838"
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Włącz śledzenie w celu monitorowania ukończenia przepływu pracy, end-to-end
Są wbudowane, śledzenie, możesz włączyć dla różnych części przepływu pracy business-to-business, takie jak śledzenie AS2 lub X12 wiadomości. Podczas tworzenia przepływów pracy zawiera aplikację logiki, BizTalk Server, SQL Server lub inną warstwę, można włączyć śledzenie niestandardowej, która zapisuje zdarzenia od początku do końca przepływu pracy, a następnie. 

Ten temat zawiera kod niestandardowy, który można użyć w warstwach poza aplikację logiki. 

## <a name="custom-tracking-schema"></a>Niestandardowy schemat śledzenia
````java

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

````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| Źródłowa |   | Typ działania źródłowego. Dozwolone wartości to **Microsoft.Logic/workflows** i **niestandardowych**. (Wymagane) |
| Element źródłowy |   | Jeśli typ źródła jest **Microsoft.Logic/workflows**, informacje o źródle musi wykonać tego schematu. Jeśli typ źródła jest **niestandardowe**, schemat jest JToken. (Wymagane) |
| systemId | Ciąg | Identyfikator logiki aplikacji systemu. (Wymagane) |
| runId | Ciąg | Uruchom identyfikatora aplikacji logiki (Wymagane) |
| operationName | Ciąg | Nazwa operacji (na przykład, akcji lub wyzwalacz). (Wymagane) |
| repeatItemScopeName | Ciąg | Powtórz nazwy elementu, jeśli akcja jest wewnątrz `foreach` / `until` pętli. (Wymagane) |
| repeatItemIndex | Liczba całkowita | Czy akcja jest wewnątrz `foreach` / `until` pętli. Wskazuje indeks elementu powtarzanego. (Wymagane) |
| trackingId | Ciąg | Identyfikator, aby skorelować komunikaty śledzenia. (Opcjonalnie) |
| correlationId | Ciąg | Identyfikator korelacji służące do skorelowania wiadomości. (Opcjonalnie) |
| clientRequestId | Ciąg | Klienta można umieścić w nim służące do skorelowania wiadomości. (Opcjonalnie) |
| eventLevel |   | Poziom zdarzenia. (Wymagane) |
| eventTime |   | Godzina zdarzenia w formacie UTC RRRR-MM-DDTHH:MM:SS.00000Z. (Wymagane) |
| recordType |   | Typ rekordu śledzenia. Dozwolone wartości to **niestandardowych**. (Wymagane) |
| rekord |   | Typ niestandardowy rekord. Dozwolony format to JToken. (Wymagane) |

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B
Aby uzyskać informacji na temat protokołu B2B śledzenia schematów zobacz:
* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [monitorowanie wiadomości B2B](logic-apps-monitor-b2b-message.md).   
* Dowiedz się więcej o [śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md).
