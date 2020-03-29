---
title: UNH 2.5 segmentów w wiadomościach EDIFACT
description: Rozwiązywanie komunikatów EDIFACT w segmentach UNH2.5 w usłudze Azure Logic Apps z pakietem integracji dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792537"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Obsługa dokumentów EDIFACT w segmentach UNH2.5 w usłudze Azure Logic Apps

Jeśli w dokumencie EDIFACT istnieje segment UNH2.5, segment jest używany do wyszukiwania schematu. Na przykład w tym przykładowym komunikacie EDIFACT pole UNH to: `EAN008`

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Aby obsłużyć ten komunikat, wykonaj następujące kroki opisane poniżej:

1. Zaktualizuj schemat.

1. Sprawdź ustawienia umowy.

## <a name="update-the-schema"></a>Aktualizowanie schematu

Aby przetworzyć wiadomość, należy wdrożyć schemat, który ma nazwę węzła głównego UNH2.5. Na przykład nazwa główna schematu dla przykładowego `EFACT_D03B_ORDERS_EAN008`pola UNH jest . Dla `D03B_ORDERS` każdego, który ma inny segment UNH2.5, należy wdrożyć indywidualny schemat.

## <a name="add-schema-to-edifact-agreement"></a>Dodawanie schematu do umowy EDIFACT

### <a name="edifact-decode"></a>Dekodowanie EDIFACT

Aby zdekodować wiadomość przychodzącą, skonfiguruj schemat w ustawieniach odbierania umowy EDIFACT:

1. W [witrynie Azure portal](https://portal.azure.com)otwórz swoje konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach odbierania umowy EDIFACT.

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako JSON**. Dodaj wartość UNH2.5 do `schemaReferences` sekcji Receive Agreement:

   ![Dodaj UNH2.5, aby otrzymać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kodowanie EDIFACT

Aby zakodować wiadomość przychodzącą, skonfiguruj schemat w ustawieniach wysyłania umowy EDIFACT

1. W [witrynie Azure portal](https://portal.azure.com)otwórz swoje konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach wysyłania umowy EDIFACT.

1. Wybierz edifact umowy i kliknij przycisk **Edytuj jako JSON**.  Dodaj wartość UNH2.5 w **schemacie Wyślij umowyReferencje**

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako JSON**. Dodaj wartość UNH2.5 do `schemaReferences` sekcji Wyślij umowę:

   ![Dodaj UNH2.5, aby wysłać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [umowach dotyczących kont integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)