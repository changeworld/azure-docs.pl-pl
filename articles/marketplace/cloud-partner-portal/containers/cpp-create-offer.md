---
title: Utwórz ofertę Azure Containers | Dokumentacja firmy Microsoft
description: Jak opublikować nową ofertę kontenerów w portalu Marketplace...
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b2a34a5ed2c6b57bfce0b9a4cf35cdf14cff9c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473051"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Tworzenie nowej oferty kontenera za pomocą portalu Cloud Partner

W tym artykule opisano, jak tworzyć i publikować wpis oferty kontenera dla witryny Azure Marketplace. Każdy oferta pojawia się jako własny element w witrynie Azure Marketplace i jest skojarzony z przynajmniej jednej jednostki SKU.  Z oferty opartej na kontenerze składa się z następujących grup zasobów i usługi pomocnicze:

|  **Grupy zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejszy możliwy do wdrożenia jednostka oferty. Jednej oferty (produktu klasy) może mieć wielu jednostek SKU, skojarzone z ofertą. Jednostki SKU służy do rozróżnienia między obsługiwane funkcje i modelami rozliczeń. |
|  Portal Marketplace       | Zawiera marketingu, prawne i prowadzić zarządzania zasobami i specyfikacji.  <ul><li> Marketing zasoby obejmują oferty nazwę, opis i logo</li> <li> Zasoby prawne zawierają zasady zachowania poufności informacji, warunki użytkowania i inne dokumenty prawne</li>  <li> Włącza zasady zarządzania prowadzić można określić sposób obsługi potencjalnych klientów z poziomu portalu Azure Marketplace przez użytkownika końcowego.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje dotyczące kontaktu i zasady pomocy technicznej |


## <a name="new-offer-form"></a>Nowy formularz oferty 

Zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com/), a następnie wybierz pozycję **+ nowa oferta** na pasku menu po lewej stronie. Na nowe oferty menu, wybierz **kontenery** do wyświetlenia **nowa oferta** formularza i rozpocząć proces Definiowanie zasobów dla nowej oferty kontenera.

![Wybierz opcję kontenerów na potrzeby nowej oferty](./media/azure-container-offer.png)

## <a name="next-steps"></a>Kolejne kroki

**Nowa oferta** strona Typ oferty kontener zawiera zbiór kart i pola formularza, które będziesz używać do tworzenia nowej oferty. Każda z następujących artykułów wyjaśnia, jak karta służy do definiowania grup zasobów i usługi pomocnicze nowej oferty kontenera.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
