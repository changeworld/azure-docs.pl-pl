---
title: Łączenie się z siecią równorzędną w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć sieć laboratoryjną z inną siecią jako element równorzędny. Na przykład połączyć lokalną sieć szkolną/akademija z siecią wirtualną laboratorium na platformie Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 56c31e03eeec0c81207dc402e864eadec2d768bd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474060"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Łączenie sieci laboratorium z siecią wirtualną równorzędną w usługach Azure Lab Services 
Ten artykuł zawiera informacje dotyczące komunikacji równorzędnej sieci laboratoriów z inną siecią. 

## <a name="overview"></a>Omówienie
Komunikacja równorzędna sieci wirtualnej umożliwia bezproblemowe łączenie sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w sieciach wirtualnych równorzędnych jest kierowany za pośrednictwem infrastruktury szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej, tylko za pośrednictwem prywatnych adresów IP. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md).

W niektórych scenariuszach, w tym w następujących scenariuszach, może być konieczne połączenie sieci laboratorium z siecią wirtualną równorzędną:

- Maszyny wirtualne w laboratorium mają oprogramowanie, które łączy się z lokalnymi serwerami licencji w celu uzyskania licencji
- Maszyny wirtualne w laboratorium potrzebują dostępu do zestawów danych (lub innych plików) w udziałach sieciowych uniwersytetu. 

Niektóre sieci lokalne są połączone z usługą Azure Virtual Network za pośrednictwem [usługi ExpressRoute](../../expressroute/expressroute-introduction.md) lub [bramy sieci wirtualnej.](../../vpn-gateway/vpn-gateway-about-vpngateways.md) Te usługi muszą być skonfigurowane poza usługami Azure Lab Services. Aby dowiedzieć się więcej na temat łączenia sieci lokalnej z platformą Azure przy użyciu usługi ExpressRoute, zobacz [Omówienie usługi ExpressRoute](../../expressroute/expressroute-introduction.md). W przypadku łączności lokalnej przy użyciu bramy sieci wirtualnej brama, określona sieć wirtualna i konto laboratorium muszą znajdować się w tym samym regionie.

> [!NOTE]
> Podczas tworzenia sieci wirtualnej platformy Azure, która będzie równorzędna z kontem laboratoryjnym, ważne jest, aby zrozumieć, jak region sieci wirtualnej wpływa na środowisko, w którym tworzone są laboratoria w klasie.  Aby uzyskać więcej informacji, zapoznaj się z sekcją przewodnika administratora na temat [regionów\lokalizacji](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurowanie w momencie tworzenia konta w laboratorium
Podczas tworzenia nowego konta laboratorium można wybrać istniejącą sieć wirtualną, która jest wyświetlana na liście rozwijanej **sieci wirtualnej równorzędnej** na karcie **Zaawansowane.** Wybrana sieć wirtualna jest połączona (równorzędna) z laboratoriami utworzonymi w ramach konta laboratorium. Wszystkie maszyny wirtualne w laboratoriach, które są tworzone po wykonywując tę zmianę będzie miał dostęp do zasobów w sieci wirtualnej równorzędnej. 

Istnieje również przepis, aby zapewnić **zakres adresów** maszyn wirtualnych dla laboratoriów. Jeśli zakres adresów jest podany, wszystkie maszyny wirtualne w laboratoriach w ramach konta laboratorium zostaną utworzone w tym zakresie adresów. Zakres adresów powinien znajdować się w notacji CIDR (np. 10.20.0.0/20) i nie pokrywać się z istniejącymi zakresami adresów. Podczas podawania zakresu adresów, ważne jest, aby myśleć o liczbie maszyn wirtualnych, które zostaną utworzone w laboratoriach i zapewnić zakres adresów, aby pomieścić. Dla danego zakresu zostanie wyświetlona liczba laboratoriów, które może pomieścić.

![Wybierz sieć wirtualną do elementu równorzędnego](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia konta w laboratorium, zobacz [Konfigurowanie konta w laboratorium](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurowanie po utworzeniu laboratorium
Tę samą właściwość można włączyć na karcie **Konfiguracja laboratoriów** na stronie **Konto laboratorium,** jeśli sieć równorzędna nie została skonfigurowana w momencie tworzenia konta w laboratorium. Zmiany wprowadzone w tym ustawieniu dotyczą tylko laboratoriów, które są tworzone po zmianie. Jak widać na obrazie, można włączyć lub wyłączyć **sieci wirtualnej równorzędnej** dla laboratoriów na koncie laboratorium. 

![Włączanie lub wyłączanie komunikacji równorzędnej sieci wirtualnej po utworzeniu laboratorium](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Po wybraniu sieci wirtualnej dla pola **Sieci wirtualnej równorzędnej** opcja **Zezwalaj twórcy laboratorium na wybranie lokalizacji laboratorium** jest wyłączona. To dlatego, że laboratoria na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium dla nich do łączenia się z zasobami w sieci wirtualnej równorzędnej. 

> [!IMPORTANT]
> Ta zmiana ustawienia dotyczy tylko laboratoriów, które są tworzone po wniesieniu zmiany, a nie do istniejących laboratoriów. 


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Dołączanie udostępnionej galerii obrazów do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Konfigurowanie innych ustawień dla laboratorium](how-to-configure-lab-accounts.md)
