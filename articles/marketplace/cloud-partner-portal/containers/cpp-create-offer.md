---
title: Tworzenie oferty kontenerów platformy Azure | Azure Marketplace
description: Jak opublikować nową ofertę kontenera dla Marketplace..
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 25d678ad7b8c2b786239b294de873bc1d98fa5cb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270336"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Tworzenie nowej oferty kontenerów za pomocą portalu Cloud Partner Portal

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami usługi Azure Container do Centrum partnerów. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty usługi Azure Container](https://aka.ms/CreateContainerOffer) do zarządzania zmigrowanych ofert.

W tym artykule opisano sposób tworzenia i publikowania wpisu oferty kontenera dla portalu Azure Marketplace. Każda oferta jest wyświetlana jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta kontenera składa się z następujących grup zasobów i usług pomocniczych:

|  **Grupa aktywów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejsza rozmieszczona jednostka oferty. Z ofertą może być skojarzonych pojedyncza oferta (klasa produktu). Jednostek SKU można używać do rozróżniania między obsługiwanymi funkcjami a modelami rozliczeń. |
|  Portal Marketplace       | Zawiera aktywa i specyfikacje marketingu, zarządzania prawnego i ołowiu.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Aktywa prawne obejmują politykę prywatności, warunki użytkowania i inną dokumentację prawną</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z portalu użytkowników końcowych usługi Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach pomocy technicznej |


## <a name="new-offer-form"></a>Formularz Nowa oferta 

Zaloguj się do [portalu cloud partner,](https://cloudpartner.azure.com/)a następnie wybierz **+ Nowa oferta** na pasku menu po lewej stronie. W menu Nowa oferta wybierz **pozycję Kontenery,** aby wyświetlić formularz **Nowa oferta** i rozpocząć proces definiowania zasobów dla nowej oferty kontenera.

![Wybierz opcję kontenerów dla nowej oferty](./media/azure-container-offer.png)

## <a name="next-steps"></a>Następne kroki

Strona **Nowa oferta** dla typu oferty kontenera zawiera zestaw kart i pól formularza, których użyjesz do utworzenia nowej oferty. Każdy z poniższych artykułów wyjaśnia, jak używać karty do definiowania grup zasobów i usług pomocniczych dla nowej oferty kontenera.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
