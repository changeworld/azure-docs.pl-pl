---
title: Korzystanie z bramy aplikacji platformy Azure z maszynami wirtualnymi VMware
description: W tym artykule opisano, jak używać bramy aplikacji platformy Azure do zarządzania przychodzącym ruchem internetowym serwerów sieci Web działających w maszynach wirtualnych VMware, które wygrywają środowisko CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015460"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Korzystanie z bramy aplikacji platformy Azure z maszynami wirtualnymi VMware w środowisku cloudsimple private cloud

Za pomocą bramy aplikacji platformy Azure można zarządzać przychodzącym ruchem sieci Web dla serwerów sieci web działających na maszynach wirtualnych VMware w środowisku cloudsimple private cloud.

Korzystając z usługi Azure Application Gateway we wdrożeniu hybrydowym publiczno-prywatnej, można zarządzać ruchem internetowym do aplikacji, zapewnić bezpieczny front-end i odciążyć przetwarzanie SSL dla swoich usług działających w środowisku VMware. Usługa Azure Application Gateway kieruje przychodzący ruch sieci web do wystąpień puli wewnętrznej bazy danych zamieszkałych w środowiskach VMware zgodnie ze skonfigurowanymi regułami i sondami kondycji.

To rozwiązanie usługi Azure Application Gateway wymaga:

* Mieć subskrypcję platformy Azure.
* Tworzenie i konfigurowanie sieci wirtualnej platformy Azure oraz podsieci w sieci wirtualnej.
* Tworzenie i konfigurowanie reguł sieciowej grupy nsg i równorzędne sieci wirtualnej przy użyciu usługi ExpressRoute do chmury CloudSimple Private Cloud.
* Utwórz & skonfiguruj chmurę prywatną.
* Utwórz & konfigurowanie bramy aplikacji platformy Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Scenariusz wdrażania bramy aplikacji platformy Azure

W tym scenariuszu usługa Azure Application Gateway jest uruchamiana w sieci wirtualnej platformy Azure. Sieć wirtualna jest połączona z chmurą prywatną za pośrednictwem obwodu usługi ExpressRoute. Wszystkie podsieci w chmurze prywatnej są dostępne dla adresów IP z podsieci sieci wirtualnej.

![Moduł równoważenia obciążenia platformy Azure w sieci wirtualnej platformy Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

Proces wdrażania składa się z następujących zadań:

1. [Sprawdź, czy spełnione są wymagania wstępne](#1-verify-prerequisites)
2. [Łączenie wirtualnego połączenia platformy Azure z chmurą prywatną](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Wdrażanie bramy aplikacji platformy Azure](#3-deploy-an-azure-application-gateway)
4. [Tworzenie i konfigurowanie puli maszyn wirtualnych serwera sieci Web w chmurze prywatnej](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Sprawdź wymagania wstępne

Sprawdź, czy spełnione są te wymagania wstępne:

* Usługa Azure Resource Manager i sieć wirtualna jest już utworzona.
* Dedykowana podsieć (dla bramy aplikacji) w sieci wirtualnej platformy Azure jest już utworzona.
* CloudSimple Private Cloud jest już utworzony.
* Nie ma konfliktu ip między podsieciami IP w sieci wirtualnej i podsieci w chmurze prywatnej.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Połącz swoją sieć wirtualną platformy Azure z chmurą prywatną

Aby połączyć sieć wirtualną platformy Azure z usługą Private Cloud, wykonaj ten proces.

1. [W portalu CloudSimple skopiuj informacje o komunikacji równorzędnej usługi ExpressRoute](virtual-network-connection.md).

2. [Skonfiguruj bramę sieci wirtualnej dla swojej sieci wirtualnej platformy Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Połącz swoją sieć wirtualną z obwódem CloudSimple ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Użyj skopiowanych informacji komunikacji równorzędnej, aby połączyć sieć wirtualną z obwodem usługi ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Wdrażanie bramy aplikacji platformy Azure

Szczegółowe instrukcje w tym zakresie są dostępne w [obszarze Tworzenie bramy aplikacji z regułami routingu opartymi na ścieżce przy użyciu portalu Azure.](../application-gateway/create-url-route-portal.md) Oto podsumowanie wymaganych kroków:

1. Utwórz sieć wirtualną w grupie subskrypcji i zasobów.
2. Utwórz podsieć (która ma być używana jako dedykowana podsieć) w sieci wirtualnej.
3. Utwórz standardową bramę aplikacji (opcjonalnie włącz WAF): Na stronie głównej portalu Azure kliknij pozycję > **Brama aplikacji** **sieciowej** **zasobów** > od lewej górnej strony strony. Wybierz standardową jednostkę SKU i rozmiar oraz podaj informacje o subskrypcji, grupie zasobów i lokalizacji platformy Azure. W razie potrzeby utwórz nowy publiczny adres IP dla tej bramy aplikacji i podaj szczegółowe informacje o sieci wirtualnej i dedykowanej podsieci bramy aplikacji.
4. Dodaj pulę wewnętrznej bazy danych z maszynami wirtualnymi i dodaj ją do bramy aplikacji.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Tworzenie i konfigurowanie puli maszyn wirtualnych serwera sieci Web w chmurze prywatnej

W vCenter utwórz maszyny wirtualne z wybranym systemem operacyjnym i serwerem sieci Web (takim jak Windows/IIS lub Linux/Apache). Wybierz podsieć/sieć Wirtualną przeznaczoną dla warstwy sieci web w chmurze prywatnej. Sprawdź, czy co najmniej jedna maszyna wirtualna vNIC serwera sieci Web znajduje się w podsieci warstwy sieci web.
