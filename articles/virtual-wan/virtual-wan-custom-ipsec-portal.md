---
title: 'Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN platformy Azure: portal | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować niestandardowe zasady IPsec dla wirtualnej sieci WAN platformy Azure za pomocą portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515748"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN przy użyciu portalu

Niestandardowe zasady protokołu IPsec dla wirtualnej sieci WAN można skonfigurować w witrynie Azure portal. Zasady niestandardowe są przydatne, gdy obie strony (lokalne i brama sieci VPN platformy Azure) mają używać tych samych ustawień dla fazy 1 i IKE Fazy 2.

## <a name="working-with-custom-policies"></a>Praca z zasadami niestandardowymi

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurowanie zasad

1. **Znajdź koncentrator wirtualny**. Przejdź w przeglądarce do witryny [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) i zaloguj się przy użyciu konta platformy Azure. Znajdź wirtualne centrum witryny.
2. **Wybierz witrynę sieci VPN**. Na stronie centrum wybierz witrynę sieci VPN, dla której chcesz skonfigurować zasady niestandardowe.

   ![Wybierz](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edytuj połączenie sieci VPN**. Z **menu Kontekst ...** **...** wybierz pozycję Edytuj połączenie **SIECI VPN**.

   ![edytuj](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Skonfiguruj ustawienia**. Na stronie **Edytowanie połączenia sieci VPN** skonfiguruj ustawienia. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

   ![konfigurowanie i zapisywanie](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).