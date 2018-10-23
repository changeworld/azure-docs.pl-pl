---
title: Tworzenie oferty maszyny wirtualnej w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Listy czynności wymagane do utworzenia nowej maszyny wirtualnej (VM) oferują usługi w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 0a875b69a10f6305b4ffe32b63d3db5b508faf01
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49640096"
---
# <a name="create-virtual-machine-offer"></a>Tworzenie oferty maszyny wirtualnej

W tej sekcji przedstawiono kroki wymagane do utworzenia nowego żądania oferty maszyny wirtualnej (VM) w portalu Azure Marketplace.  Każdy oferta pojawia się jako własny element w witrynie Azure Marketplace i jest skojarzony z przynajmniej jednej jednostki SKU.  Oferty maszyny Wirtualnej składa się z następujących grup zasobów i usługi pomocnicze: 

![Zasoby dla oferty maszyny Wirtualnej](./media/publishvm_002.png)

Gdzie:

|  **Grupy zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Od najmniejszych możliwych do zakupienia jednostka oferty. Jednej oferty (produktu klasy) może mieć wielu jednostek SKU skojarzony z nim, aby odróżnić obsługiwanych funkcji, typy obrazów maszyn wirtualnych i modelami rozliczeń. |
|  Marketplace       | Zawiera marketingu, prawne i prowadzić zarządzania zasobami i specyfikacji.  <ul><li> Marketing zasoby obejmują oferty nazwę, opis i logo</li> <li> Zasoby prawne zawierają zasady zachowania poufności informacji, warunki użytkowania i inne dokumenty prawne</li>  <li> Włącza zasady zarządzania prowadzić można określić sposób obsługi potencjalnych klientów z poziomu portalu Azure Marketplace przez użytkownika końcowego.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje dotyczące kontaktu i zasady pomocy technicznej |
| Wersja testowa         | Definiuje zasoby, które umożliwiają użytkownikom końcowym przetestować oferty przed ich do zakupu |
|  |  |


## <a name="new-offer-form"></a>Nowy formularz oferty

Raz swojego konta w [portalu Cloud Partner](http://cloudpartner.azure.com/), kliknij przycisk **+ nowa oferta** element na pasku menu po lewej stronie. Wynikowy menu, kliknij pozycję **maszyn wirtualnych** do wyświetlenia **nowa oferta** formularza i rozpocząć proces Definiowanie zasobów dla nowej oferty maszyny Wirtualnej. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Wybór interfejsu użytkownika oferty nowej maszyny wirtualnej](./media/publishvm_003.png)

> [!WARNING]
> Jeśli **maszyn wirtualnych** opcja nie jest wyświetlany lub nie jest włączona, a następnie Twoje konto nie ma uprawnień do utworzenia tego typu oferty.  Sprawdź, czy zostały spełnione wszystkie [wymagania wstępne](./cpp-prerequisites.md) dla tego typu oferty, w tym rejestrowanie dla konta dewelopera.


## <a name="next-steps"></a>Kolejne kroki

Kolejnych tematach w tej sekcji duplikatów kart w **nowa oferta** strony (w przypadku typu oferty maszyny Wirtualnej).  Każdy artykuł wyjaśnia, jak karta skojarzony służy do definiowania grup zasobów i usługi pomocnicze dla Twojej nowej oferty maszyny Wirtualnej.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta dysku testu](./cpp-test-drive-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
