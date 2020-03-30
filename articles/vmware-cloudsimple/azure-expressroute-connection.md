---
title: Rozwiązanie Azure VMware by CloudSimple — łączenie chmury prywatnej z siecią Azure przy użyciu usługi ExpressRoute
description: W tym artykule opisano sposób łączenia środowiska cloudsimple private cloud z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015239"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Łączenie środowiska cloudsimple private cloud z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute

Chmura prywatna CloudSimple może być połączona z siecią wirtualną platformy Azure przy użyciu usługi Azure ExpressRoute.  To połączenie o wysokiej przepustowości i małych opóźnieniach umożliwia dostęp do usług uruchomionych w ramach subskrypcji platformy Azure ze środowiska private cloud.

Połączenie z siecią wirtualną umożliwia:

* Użyj platformy Azure jako docelowego tworzenia kopii zapasowych dla maszyn wirtualnych w chmurze prywatnej.
* Wdrażaj serwery usługi zarządzania kluczami w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych private cloud vSAN.
* Użyj aplikacji hybrydowych, w których warstwa sieci web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych są uruchamiane w chmurze prywatnej.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Konfigurowanie połączenia z siecią wirtualną

Aby skonfigurować połączenie z siecią wirtualną z usługą Private Cloud, potrzebny jest klucz autoryzacji, identyfikator URI obwodu równorzędnego i dostęp do subskrypcji platformy Azure. Te informacje są dostępne na stronie Połączenie sieci wirtualnej w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Uzyskiwanie informacji dotyczących komunikacji równorzędnej dla sieci wirtualnej platformy Azure do połączenia CloudSimple](virtual-network-connection.md). Jeśli masz jakiekolwiek problemy z uzyskaniem informacji, prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">prośbę o pomoc techniczną</a>.

> [!TIP]
> Jeśli masz już sieć wirtualną platformy Azure, podsieć bramy i bramę sieci wirtualnej, możesz przejść do kroku 4.

1. Utwórz sieć wirtualną w ramach subskrypcji platformy Azure i sprawdź, czy wybrana przestrzeń adresowa różni się od przestrzeni adresowej chmury prywatnej.  Jeśli masz już sieć wirtualną platformy Azure, możesz użyć istniejącej.  Aby uzyskać szczegółowe informacje, zobacz [Tworzenie sieci wirtualnej przy użyciu portalu Azure](../virtual-network/quick-create-portal.md).
2. Utwórz podsieć bramy w sieci wirtualnej platformy Azure.  Jeśli masz już podsieć bramy w sieci wirtualnej platformy Azure, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie podsieci bramy](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Utwórz bramę sieci wirtualnej w sieci wirtualnej.  Jeśli masz istniejącą bramę sieci wirtualnej, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie bramy sieci wirtualnej](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Utwórz połączenie między siecią wirtualną a chmurą prywatną, realizując klucz autoryzacji zgodnie z opisem w [sekcji Połącz sieć wirtualną z obwodem — inną subskrypcją.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

> [!WARNING]
> Jeśli używasz istniejącej bramy sieci wirtualnej i ma połączenie usługi ExpressRoute z tą samą lokalizacją co obwód Usługi CloudSimple ExpressRoute, połączenie nie zostanie nawiązane.  Utwórz nową sieć wirtualną i wykonaj poprzednie kroki.

## <a name="test-the-virtual-network-connection"></a>Testowanie połączenia z siecią wirtualną

Po utworzeniu połączenia można sprawdzić stan połączenia, wybierając **pozycję Właściwości** w obszarze **Ustawienia**.  Stan i stan inicjowania obsługi administracyjnej powinny być wyświetlane **powodzenie**.

![Stan połączenia](media/azure-expressroute-connection.png)

Aby przetestować połączenie z siecią wirtualną:

1. Utwórz maszynę wirtualną w ramach subskrypcji platformy Azure.
2. Znajdź adres IP swojego centrum vCenter private cloud (patrz powitalna wiadomość e-mail).
3. Pinguj urządzenie vCenter w chmurze z maszyny wirtualnej utworzonej w sieci wirtualnej platformy Azure.
4. Ping maszyny wirtualnej platformy Azure z maszyny wirtualnej uruchomionej w centrum wirtualnym private cloud.

Jeśli masz jakiekolwiek problemy z nawiązaniem połączenia, prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">żądanie pomocy technicznej</a>.
