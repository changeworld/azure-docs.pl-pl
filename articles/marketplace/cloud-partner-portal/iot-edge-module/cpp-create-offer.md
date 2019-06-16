---
title: Utwórz ofertę modułu usługi Azure IoT Edge | Portal Azure Marketplace
description: Jak opublikować nowy moduł usługi IoT Edge w portalu Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 98eb537a5c064cd22b995ae214c8d61db7a8b6b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942495"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Tworzenie nowej oferty modułu usługi IoT Edge przy użyciu portalu Cloud Partner

W tym artykule opisano, jak tworzyć i publikować wpis oferty modułu usługi IoT Edge w portalu Azure Marketplace. Każdy oferta pojawia się jako własny element w witrynie Azure Marketplace i jest skojarzony z przynajmniej jednej jednostki SKU.  W ramach oferty modułu usługi IoT Edge składa się z następujących grup zasobów i usługi pomocnicze:

|  **Grupy zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejszy możliwy do wdrożenia jednostka oferty. Jednej oferty (produktu klasy) może mieć wielu jednostek SKU, skojarzone z ofertą. Jednostki SKU służy do rozróżnienia między obsługiwane funkcje i modelami rozliczeń. |
|  Portal Marketplace       | Zawiera marketingu, prawne i prowadzić zarządzania zasobami i specyfikacji.  <ul><li> Marketing zasoby obejmują oferty nazwę, opis i logo</li> <li> Zasoby prawne zawierają zasady zachowania poufności informacji, warunki użytkowania i inne dokumenty prawne</li>  <li> Włącza zasady zarządzania prowadzić można określić sposób obsługi potencjalnych klientów z poziomu portalu Azure Marketplace przez użytkownika końcowego.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje dotyczące kontaktu i zasady pomocy technicznej |


## <a name="new-offer-form"></a>Nowy formularz oferty 

Zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com/), a następnie wybierz pozycję **+ nowa oferta** na pasku menu po lewej stronie. W menu nowa oferta wybierz **IoT Edge modułów** do wyświetlenia **nowa oferta** formularza i rozpocząć proces Definiowanie zasobów dla nowej oferty moduł Edge ioT. 

![Nowy moduł usługi IoT Edge oferują wybór interfejsu użytkownika](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Kolejne kroki

**Nowa oferta** strona Typ oferty modułu usługi IoT Edge zawiera zbiór kart i pola formularza, które będziesz używać do tworzenia nowej oferty. Każda z następujących artykułów wyjaśnia, jak karta służy do definiowania grup zasobów i usługi pomocnicze nowej oferty modułu usługi IoT Edge.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
