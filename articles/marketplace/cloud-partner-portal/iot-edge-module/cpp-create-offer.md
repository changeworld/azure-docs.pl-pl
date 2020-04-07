---
title: Tworzenie oferty modułów usługi Azure IoT Edge | Azure Marketplace
description: Jak opublikować nowy moduł IoT Edge dla portalu Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 112c2ba831ab27faaca755079e9d20e2aebfcf9f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744401"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Tworzenie nowej oferty modułów Usługi IoT Edge za pomocą portalu Cloud Partner Portal

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureCreateIoT) aby zarządzać zmigrowanymi ofertami.

W tym artykule opisano sposób tworzenia i publikowania wpisu oferty modułu usługi IoT Edge dla portalu Azure Marketplace. Każda oferta jest wyświetlana jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta modułu Usługi IoT Edge składa się z następujących grup zasobów i usług pomocniczych:

|  **Grupa aktywów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejsza rozmieszczona jednostka oferty. Z ofertą może być skojarzonych pojedyncza oferta (klasa produktu). Jednostek SKU można używać do rozróżniania między obsługiwanymi funkcjami a modelami rozliczeń. |
|  Portal Marketplace       | Zawiera aktywa i specyfikacje marketingu, zarządzania prawnego i ołowiu.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Aktywa prawne obejmują politykę prywatności, warunki użytkowania i inną dokumentację prawną</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z portalu użytkowników końcowych usługi Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach pomocy technicznej |


## <a name="new-offer-form"></a>Formularz Nowa oferta 

Zaloguj się do [portalu cloud partner,](https://cloudpartner.azure.com/)a następnie wybierz **+ Nowa oferta** na pasku menu po lewej stronie. W menu Nowa oferta wybierz **pozycję Moduły brzegowe IoT,** aby wyświetlić formularz **Nowa oferta** i rozpocząć proces definiowania zasobów dla nowej oferty modułu krawędzi ioT. 

![Nowy moduł IoT Edge oferuje wybór interfejsu użytkownika](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Następne kroki

Strona **Nowa oferta** dla typu oferty modułu Usługi IoT Edge zawiera zestaw kart i pól formularzy, których użyjesz do utworzenia nowej oferty. Każdy z poniższych artykułów wyjaśnia, jak używać karty do definiowania grup zasobów i usług pomocniczych dla nowej oferty modułu usługi IoT Edge.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
