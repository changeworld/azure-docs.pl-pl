---
title: Obsługa komunikatów EDIFACT z segmentów UNH 2.5 — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Rozwiąż dokumenty EDIFACT z segmentów UNH2.5 w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681676"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Obsługiwać dokumenty EDIFACT UNH2.5 segmentów w usłudze Azure Logic Apps

UNH2.5 znajduje się w dokumencie EDIFACT, jest on używany do wyszukiwania schematu. 

Przykład: Pole UNH **EAN008** komunikatu EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Kroki do wykonania, by obsłużyć komunikat 
1. Aktualizacja schematu
2. Sprawdź ustawienia umowy  

## <a name="update-the-schema"></a>Aktualizacja schematu
Przetworzyć komunikatu, należy wdrożyć schematu z UNH2.5 Nazwa węzła głównego.  Dla danego przykładowi, główna nazwa schematu będzie **EFACT_D03B_ORDERS_EAN008**  

Dla każdego D03B_ORDERS z segmentem UNH2.5 różnych trzeba wdrożyć schemat indywidualnych.  

## <a name="add-schema-to-the-edifact-agreement"></a>Dodawanie schematu do Umowy EDIFACT
### <a name="edifact-decode"></a>Dekodowanie EDIFACT
Do dekodowania komunikatu przychodzącego, skonfigurował schemat w EDIFACT umowie odbierania ustawień
1. Dodaj schemat konta integracji    
2. Konfigurowanie schematu w EDIFACT umowie odbierania ustawień. 
3. Wybierz umowę EDIFACT, a następnie kliknij przycisk **Edytuj jako dane JSON**.  Dodaj wartość UNH2.5 w umowie odbierania **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kodowanie EDIFACT
Do kodowania komunikatu przychodzącego, należy skonfigurować schematu w ustawieniach wysyłania umowy EDIFACT
1. Dodaj schemat konta integracji    
2. Skonfiguruj schematu w ustawieniach wysyłania umowy EDIFACT. 
3. Wybierz umowę EDIFACT, a następnie kliknij przycisk **Edytuj jako dane JSON**.  Dodaj wartość UNH2.5 w umowie wysyłania **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o umowach dotyczących konta integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md "Dowiedz się więcej o umowach dotyczących integracji przedsiębiorstw")  