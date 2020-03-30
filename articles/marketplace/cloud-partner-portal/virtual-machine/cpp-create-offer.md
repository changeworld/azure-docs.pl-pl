---
title: Tworzenie oferty maszyny wirtualnej w portalu Azure Marketplace
description: Wyświetla listę kroków wymaganych do utworzenia nowej oferty maszyny wirtualnej dla portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278045"
---
# <a name="create-virtual-machine-offer"></a>Tworzenie oferty maszyny wirtualnej

W tej sekcji wymieniono kroki wymagane do utworzenia nowego żądania oferty maszyny wirtualnej dla portalu Azure Marketplace.  Każda oferta jest wyświetlana jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta maszyny Wirtualnej składa się z następujących grup zasobów i usług pomocniczych: 

![Zasoby oferty maszyn wirtualnych](./media/publishvm_002.png)

gdzie:

|  **Grupa aktywów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejsza jednostka do kupienia oferty. Z jedną ofertą (klasą produktów) może być skojarzonych wiele jednostek SKU, aby odróżnić obsługiwane funkcje, typy obrazów maszyn wirtualnych i modele rozliczeń. |
|  Portal Marketplace       | Zawiera aktywa i specyfikacje marketingu, zarządzania prawnego i ołowiu.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Aktywa prawne obejmują politykę prywatności, warunki użytkowania i inną dokumentację prawną</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z portalu użytkowników końcowych portalu usługi Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach pomocy technicznej |
| Wersja testowa         | Definiuje zasoby, które umożliwiają użytkownikom końcowym testowanie oferty przed jej zakupem |
|  |  |


## <a name="new-offer-form"></a>Formularz Nowa oferta

Po zalogowaniu się do [portalu cloud partnerów](https://cloudpartner.azure.com/)kliknij pozycję **+ Nowa oferta** na lewym pasku menu. W menu wynikowym kliknij na **maszyny wirtualne,** aby wyświetlić formularz **Nowa oferta** i rozpocząć proces definiowania zasobów dla nowej oferty maszyny wirtualnej. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nowa maszyna wirtualna oferuje wybór interfejsu użytkownika](./media/publishvm_003.png)

> [!WARNING]
> Jeśli opcja **Maszyny wirtualne** nie jest wyświetlana lub nie jest włączona, twoje konto nie ma uprawnień do tworzenia tego typu oferty.  Sprawdź, czy spełniasz wszystkie [wymagania wstępne](./cpp-prerequisites.md) dla tego typu oferty, w tym rejestrację konta dewelopera.


## <a name="next-steps"></a>Następne kroki

Kolejne tematy w tej sekcji odzwierciedlają karty na stronie **Nowa oferta** (dla typu oferty maszyny Wirtualnej).  Każdy artykuł wyjaśnia, jak używać skojarzonej karty do definiowania grup zasobów i usług pomocniczych dla nowej oferty maszyny Wirtualnej.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta Wersja testowa](./cpp-test-drive-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
