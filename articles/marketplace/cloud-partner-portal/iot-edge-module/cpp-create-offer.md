---
title: Tworzenie oferty modułu Azure IoT Edge | Portal Azure Marketplace
description: Jak opublikować nowy moduł IoT Edge dla portalu Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: c1e2f5ac6078309b7bd8753a3fc57a592a67dcec
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826093"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Utwórz nową ofertę modułu IoT Edge przy użyciu portal Cloud Partner

W tym artykule opisano sposób tworzenia i publikowania wpisu oferty modułu IoT Edge w portalu Azure Marketplace. Każda oferta pojawia się jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta modułu IoT Edge składa się z następujących grup zasobów i usług pomocniczych:

|  **Grupa zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    SKU            |  Najmniejsza dostępna jednostka oferty. Pojedyncza oferta (Klasa produktu) może mieć wiele jednostek SKU skojarzonych z ofertą. Jednostek SKU można użyć do rozróżnienia między obsługiwanymi funkcjami i modelami rozliczeń. |
|  Portal Marketplace       | Zawiera zasoby i specyfikacje zarządzania marketingiem, prawną i liderem.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Zasoby prawne obejmują zasady ochrony prywatności, warunki użytkowania i inne dokumenty prawne</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z poziomu portalu użytkowników końcowych platformy Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach |


## <a name="new-offer-form"></a>Formularz nowej oferty 

Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/), a następnie wybierz pozycję **+ Nowa oferta** na pasku menu po lewej stronie. W menu Nowa oferta wybierz pozycję **moduły IoT Edge** , aby wyświetlić **nowy formularz oferty** i rozpocząć proces definiowania zasobów dla nowej oferty modułu usługi IoT Edge. 

![Nowy moduł IoT Edge oferuje wybór interfejsu użytkownika](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Następne kroki

**Nowa strona oferty** dla typu oferty modułu IoT Edge zawiera zestaw kart i pól formularza, które będą używane do tworzenia nowej oferty. W każdym z poniższych artykułów wyjaśniono, jak za pomocą karty definiować grupy zasobów i usługi pomocnicze dla nowej oferty modułu IoT Edge.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
