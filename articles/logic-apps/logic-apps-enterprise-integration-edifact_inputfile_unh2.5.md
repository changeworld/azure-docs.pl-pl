---
title: Segmenty UNH 2,5 w komunikatach EDIFACT — Azure Logic Apps
description: Rozpoznawanie komunikatów EDIFACT z segmentami UNH 2.5 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c30c35375a45171c938f80e94dd7d9be4c3ee8b1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679967"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Obsługa dokumentów EDIFACT z segmentami UNH 2.5 w Azure Logic Apps

Jeśli w dokumencie EDIFACT istnieje segment UNH 2.5, segment jest używany do wyszukiwania w schemacie. Na przykład w tym przykładowym komunikacie EDIFACT pole UNH jest `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Aby obsłużyć ten komunikat, wykonaj następujące kroki opisane poniżej:

1. Zaktualizuj schemat.

1. Sprawdź ustawienia umowy.

## <a name="update-the-schema"></a>Aktualizowanie schematu

Aby przetworzyć komunikat, należy wdrożyć schemat, który ma nazwę węzła głównego UNH 2.5. Na przykład nazwa głównego schematu dla przykładowego pola UNH jest `EFACT_D03B_ORDERS_EAN008`. Dla każdego `D03B_ORDERS`, który ma inny segment UNH 2,5, należy wdrożyć pojedynczy schemat.

## <a name="add-schema-to-edifact-agreement"></a>Dodaj schemat do umowy EDIFACT

### <a name="edifact-decode"></a>Dekodowanie EDIFACT

Aby zdekodować komunikat przychodzący, skonfiguruj schemat w ustawieniach odbioru umowy EDIFACT:

1. W [Azure Portal](https://portal.azure.com)Otwórz konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach odbioru umowy EDIFACT.

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako kod JSON**. Dodaj wartość UNH 2,5 do sekcji `schemaReferences` umowy odbioru:

   ![Dodaj UNH 2.5, aby uzyskać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kodowanie EDIFACT

Aby zakodować komunikat przychodzący, skonfiguruj schemat w ustawieniach umowy EDIFACT

1. W [Azure Portal](https://portal.azure.com)Otwórz konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach wysyłania umowy EDIFACT.

1. Wybierz umowę EDIFACT, a następnie kliknij pozycję **Edytuj jako plik JSON**.  Dodaj wartość UNH 2,5 w umowie Send **schemaReferences**

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako kod JSON**. Dodaj wartość UNH 2,5 do sekcji `schemaReferences` umowy wysyłania:

   ![Dodaj UNH 2.5, aby wysłać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [umowach dotyczących kont integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)