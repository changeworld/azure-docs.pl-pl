---
title: Połączenie z siecią elementu równorzędnego w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć sieć laboratorium z inną siecią jako element równorzędny. Na przykład połączyć sieć Szkoła/university lokalnych z siecią wirtualną laboratorium na platformie Azure.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653224"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Łączenie sieci środowiska laboratoryjnego z równorzędnej sieci wirtualnej w usłudze Azure Lab Services 
Ten artykuł zawiera informacje dotyczące komunikacji równorzędnej sieci laboratoria z inną siecią. 

## <a name="overview"></a>Przegląd
Wirtualne sieci równorzędne umożliwiają bezproblemowe łączenie sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej przy użyciu prywatnych adresów IP. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md).

Może być konieczne łączenie sieci środowiska laboratoryjnego z równorzędnej sieci wirtualnej, w niektórych scenariuszach, w tym poniższych:

- Maszyny wirtualne w laboratorium ma oprogramowania, który nawiązuje połączenie z serwerów licencji w środowisku lokalnym w celu uzyskania licencji
- Maszyny wirtualne w laboratorium wymagany jest dostęp do zestawów danych (lub innych plików) uniwersyteckim udziałów sieciowych. 

Niektórych sieci lokalnej są połączone z siecią wirtualną platformy Azure albo za pośrednictwem [ExpressRoute](../../expressroute/expressroute-introduction.md) lub [bramy sieci wirtualnej](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Te usługi należy skonfigurować poza usługi Azure Lab Services. Aby dowiedzieć się więcej na temat łączenia sieci lokalnej na platformę Azure przy użyciu usługi ExpressRoute, zobacz [Omówienie usługi ExpressRoute]) (.. /expressroute/expressroute-Introduction.MD). Dla połączeń lokalnych za pomocą bramy sieci wirtualnej, bramę, określonych sieci wirtualnej, a konto laboratorium musi być w tym samym regionie.


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurowanie w czasie tworzenia konta laboratorium
Podczas tworzenia nowego konta laboratorium, możesz wybrać istniejącą sieć wirtualną, który przedstawia w **równorzędna sieć wirtualna** listy rozwijanej. Wybranej sieci wirtualnej jest connected(peered) do laboratoriów utworzonych w ramach konta laboratorium. Wszystkie maszyny wirtualne laboratoria, które są tworzone po udostępnianie tej zmiany, musi dostęp do zasobów w równorzędnej sieci wirtualnej. 

![Wybierz sieć wirtualną do komunikacji równorzędnej](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia konta laboratorium, zobacz [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Skonfigurować po utworzeniu laboratorium
Tę samą właściwość można włączyć w **konfiguracji Labs** karcie **konta laboratorium** strony, jeśli nie skonfigurowano sieci elementów równorzędnych w czasie tworzenia konta w laboratorium. Zmiany wprowadzone przez to ustawienie dotyczy tylko laboratoria, które są tworzone po zmianie.

![Włączanie lub wyłączanie wirtualne sieci równorzędne — po utworzeniu laboratorium](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Jak widać na ilustracji, można włączać lub wyłączać **równorzędna sieć wirtualna** do laboratoriów w ramach konta laboratorium. 

> [!IMPORTANT]
> Zmień to ustawienie dotyczy tylko laboratoria, które są tworzone po wprowadzeniu zmian do istniejących labs. 



## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)

