---
title: Uaktualnianie wirtualnej sieci WAN platformy Azure z podstawowej do standardowej — witryna Azure | Dokumenty firmy Microsoft
description: Można uaktualnić wirtualny typ sieci WAN, aby uzyskać większą funkcjonalność.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515813"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Uaktualnianie wirtualnej sieci WAN z podstawowej do standardowej

Ten artykuł pomaga uaktualnić podstawową wan do standardowej wan. Typ WAN "Podstawowe" tworzy wszystkie koncentratory wewnątrz niego jako podstawowe koncentratory SKU. W centrum Basic masz ograniczone tylko funkcje sieci VPN typu lokacja lokacja. Typ WAN "Standard" tworzy wszystkie koncentratory wewnątrz niego jako standardowe koncentratory SKU. Korzystając z koncentratorów standardowych, można włączyć usługi ExpressRoute, sieć VPN dla użytkowników (point-to-site), koncentrator pełnej siatki i przesyłanie sieci wirtualnej do sieci wirtualnej za pośrednictwem centrów platformy Azure.

W poniższej tabeli przedstawiono konfiguracje dostępne dla każdego typu sieci WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Aby zmienić typ wirtualnej sieci WAN

1. Na stronie wirtualnej sieci WAN wybierz **pozycję Konfiguracja,** aby otworzyć stronę Konfiguracja.

   ![Diagram usługi Virtual WAN](./media/upgrade-virtual-wan/1.png)
2. Dla typu wirtualnej sieci WAN wybierz pozycję **Standardowy** z listy rozwijanej.

   ![Diagram usługi Virtual WAN](./media/upgrade-virtual-wan/2.png)
3. Należy zrozumieć, że w przypadku uaktualnienia do standardowej wirtualnej sieci WAN nie można przywrócić wirtualnej sieci WAN podstawowej. Wybierz **pozycję Potwierdź,** jeśli chcesz uaktualnić.

   ![Diagram usługi Virtual WAN](./media/upgrade-virtual-wan/4.png)
4. Po zapisaniu zmiany wirtualna strona sieci WAN wygląda podobnie do tego przykładu.

   ![Diagram usługi Virtual WAN](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).