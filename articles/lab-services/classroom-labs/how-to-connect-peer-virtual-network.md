---
title: Nawiązywanie połączenia z siecią równorzędną w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak połączyć sieć laboratorium z inną siecią jako równorzędną. Na przykład Połącz lokalną sieć szkolną/Uniwersytet z siecią wirtualną laboratorium na platformie Azure.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701665"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Połącz sieć laboratorium z równorzędną siecią wirtualną w Azure Lab Services 
Ten artykuł zawiera informacje o komunikacji równorzędnej w sieci z inną siecią. 

## <a name="overview"></a>Przegląd
Komunikacja równorzędna sieci wirtualnych umożliwia bezproblemowe łączenie sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pomocą prywatnych adresów IP. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).

Może być konieczne połączenie sieci laboratorium z równorzędną siecią wirtualną w niektórych scenariuszach, takich jak następujące:

- Maszyny wirtualne w laboratorium mają oprogramowanie łączące się z lokalnymi serwerami licencji w celu uzyskania licencji
- Maszyny wirtualne w laboratorium muszą mieć dostęp do zestawów danych (lub innych plików) w udziałach sieciowych University. 

Niektóre sieci lokalne są połączone z platformą Azure Virtual Network przy użyciu bramy [ExpressRoute](../../expressroute/expressroute-introduction.md) lub [Virtual Network](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Te usługi należy skonfigurować poza Azure Lab Services. Aby dowiedzieć się więcej na temat łączenia sieci lokalnej z platformą Azure przy użyciu usługi ExpressRoute, zobacz [Omówienie usługi ExpressRoute](../../expressroute/expressroute-introduction.md). W przypadku połączenia lokalnego przy użyciu bramy Virtual Network brama, określona Sieć wirtualna i konto laboratorium muszą znajdować się w tym samym regionie.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfiguruj podczas tworzenia konta laboratorium
Podczas tworzenia nowego konta laboratorium można wybrać istniejącą sieć wirtualną, która zostanie wyświetlona na liście rozwijanej **równorzędnej sieci wirtualnej** . Wybrana Sieć wirtualna jest połączona (równorzędna) do laboratoriów utworzonych w ramach konta laboratorium. Wszystkie maszyny wirtualne w laboratoriach, które są tworzone po wprowadzeniu tej zmiany, będą miały dostęp do zasobów w sieci wirtualnej połączonej za pomocą komunikacji równorzędnej. 

![Wybierz sieć wirtualną do elementu równorzędnego](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia konta laboratorium, zobacz [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfiguruj po utworzeniu laboratorium
Tę samą Właściwość można włączyć na karcie **Konfiguracja laboratoriów** na stronie **konto laboratorium** , jeśli nie skonfigurowano sieci równorzędnej w momencie tworzenia konta laboratorium. Zmiana wprowadzona dla tego ustawienia ma zastosowanie tylko do laboratoriów, które są tworzone po zmianie. Jak widać w obrazie, możesz włączyć lub wyłączyć **równorzędną sieć wirtualną** dla laboratoriów na koncie laboratorium. 

![Włącz lub Wyłącz komunikację równorzędną sieci wirtualnej po utworzeniu laboratorium](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

W przypadku wybrania sieci wirtualnej dla pola **równorzędna Sieć wirtualna** opcja **Zezwól na wybór laboratorium do wyboru lokalizacji laboratorium** jest wyłączona. Wynika to z faktu, że laboratorium na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby można było połączyć się z zasobami w równorzędnej sieci wirtualnej. 

> [!IMPORTANT]
> Ta zmiana ustawienia ma zastosowanie tylko do laboratoriów, które są tworzone po wprowadzeniu zmiany, a nie do istniejących laboratoriów. 


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)

