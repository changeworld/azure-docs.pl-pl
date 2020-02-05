---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Połącz się z chmurą prywatną do sieci platformy Azure przy użyciu ExpressRoute
description: Zawiera opis sposobu łączenia środowiska chmury prywatnej do automatycznej synchronizacji z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015239"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Połącz środowisko chmury prywatnej automatycznej synchronizacji z siecią wirtualną platformy Azure za pomocą ExpressRoute

Chmurę prywatną do automatycznej synchronizacji można połączyć z siecią wirtualną platformy Azure przy użyciu usługi Azure ExpressRoute. Ta wysoka przepustowość, połączenie o małym opóźnieniu pozwala uzyskiwać dostęp do usług uruchomionych w ramach subskrypcji platformy Azure w środowisku chmury prywatnej.

Połączenie sieci wirtualnej umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej automatycznej synchronizacji.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować swoją kopię zapasową sieci vSAN w chmurze prywatnej chmury.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej automatycznej synchronizacji.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Konfigurowanie połączenia sieci wirtualnej

Aby skonfigurować połączenie sieci wirtualnej z chmurą prywatną do automatycznej synchronizacji, musisz mieć klucz autoryzacji, identyfikator URI obwodu równorzędnego i dostęp do subskrypcji platformy Azure. Te informacje są dostępne na stronie połączenie Virtual Network w portalu automatycznej synchronizacji. Aby uzyskać instrukcje, zobacz temat [Uzyskiwanie informacji o komunikacji równorzędnej dla usługi Azure Virtual Network w celu nawiązania połączenia automatycznej](virtual-network-connection.md)wersji. Jeśli masz problemy z uzyskaniem informacji, Prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">żądanie pomocy technicznej</a>.

> [!TIP]
> Jeśli masz już sieć wirtualną platformy Azure, podsieć bramy i bramę sieci wirtualnej, możesz przejść do kroku 4.

1. Utwórz sieć wirtualną w ramach subskrypcji platformy Azure i sprawdź, czy wybrana przestrzeń adresowa różni się od przestrzeni adresowej chmury prywatnej automatycznej synchronizacji. Jeśli masz już sieć wirtualną platformy Azure, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).
2. Utwórz podsieć bramy w sieci wirtualnej platformy Azure. Jeśli masz już podsieć bramy w sieci wirtualnej platformy Azure, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie podsieci bramy](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Utwórz bramę sieci wirtualnej w sieci wirtualnej. Jeśli masz istniejącą bramę sieci wirtualnej, możesz użyć istniejącej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie bramy sieci wirtualnej](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Utwórz połączenie między siecią wirtualną a chmurą prywatną do automatycznej synchronizacji, wykonując klucz autoryzacji zgodnie z opisem w temacie [łączenie sieci wirtualnej z subskrypcją inną niż inna](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Jeśli używasz istniejącej bramy sieci wirtualnej i ma połączenie ExpressRoute z tą samą lokalizacją co obwód ExpressRoute, połączenie nie zostanie nawiązane. Utwórz nową sieć wirtualną i wykonaj kroki opisane w poprzednich krokach.

## <a name="test-the-virtual-network-connection"></a>Testowanie połączenia sieci wirtualnej

Po utworzeniu połączenia można sprawdzić stan połączenia, wybierając pozycję **Właściwości** w obszarze **Ustawienia**. Stan stanu aprowizacji powinien wskazywać na **powodzenie**.

![Stan połączenia](media/azure-expressroute-connection.png)

Aby przetestować połączenie sieci wirtualnej:

1. Utwórz maszynę wirtualną w ramach subskrypcji platformy Azure.
2. Znajdź adres IP programu do automatycznej synchronizacji w chmurze prywatnej (Zobacz Twój powitalny adres e-mail).
3. Wyślij polecenie ping do usługi Cloud vCenter z maszyny wirtualnej utworzonej w sieci wirtualnej platformy Azure.
4. Wyślij polecenie ping do maszyny wirtualnej platformy Azure z maszyny wirtualnej uruchomionej w programie vCenter Private Cloud chmury.

Jeśli masz problemy z nawiązywaniem połączenia, Prześlij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">żądanie pomocy technicznej</a>.
