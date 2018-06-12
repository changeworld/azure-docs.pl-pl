---
title: Dekodowanie edifact B2B aplikacje logiki rozwiązać UNH2.5 - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Azure B2B aplikacje logiki dekodowania edifact rozwiązać UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e04e7515a8ddfe512ba2efbbd45c3d1b571c1fd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297951"
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Sposób obsługi EDIFACT dokumentów, w których UNH2.5 segment
Gdy UNH2.5 znajduje się w dokumencie EDIFACT, jest używany do wyszukiwania schematu. 

Przykład: Pole UNH jest **EAN008** w komunikacie EDIFACT  
UNH + SSDD1 + ZAMÓWIEŃ: D: 03B: UN:**EAN008**"  

Kroki do wykonania w celu obsługi wiadomości 
1. Aktualizacja schematu
2. Sprawdź ustawienia umowy  

## <a name="update-the-schema"></a>Aktualizacja schematu
Przetworzyć komunikatu, należy wdrożyć schematu z UNH2.5 Nazwa węzła głównego.  Dla danego przykładem, nazwa głównego schematu jest **EFACT_D03B_ORDERS_EAN008**  

Dla każdego D03B_ORDERS inny segment UNH2.5 trzeba wdrażania poszczególnych schematu.  

## <a name="add-schema-to-the-edifact-agreement"></a>Dodaj umowy EDIFACT schematu
### <a name="edifact-decode"></a>Dekodowanie EDIFACT
Zdekodować komunikatu przychodzącego, należy skonfigurować schematu w EDIFACT umowy odbierają ustawienia
1. Dodać schematu do konta integracji    
2. Konfigurowanie schematu w EDIFACT umowy odbierają ustawienia. 
3. Wybierz umowy EDIFACT i kliknij przycisk **edycji w formacie JSON**.  Dodaj wartość UNH2.5 Umowy odbierania **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kodowanie EDIFACT
Kodowanie komunikatu przychodzącego, należy skonfigurować schematu w ustawieniach wysyłania umowy EDIFACT
1. Dodać schematu do konta integracji    
2. Konfigurowanie schematu w ustawieniach wysyłania umowy EDIFACT. 
3. Wybierz umowy EDIFACT i kliknij przycisk **edycji w formacie JSON**.  Dodaj wartość UNH2.5 Umowy wysyłania **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o integracji konta umów](../logic-apps/logic-apps-enterprise-integration-agreements.md "więcej informacji na temat umowy integracji dla przedsiębiorstw")  