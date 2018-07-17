---
title: Sieć wirtualna dla usług platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzyściach związanych z wdrażaniem zasobów w sieci wirtualnej. Zasoby w sieciach wirtualnych mogą komunikować się ze sobą i zasobów lokalnych, bez ruch przechodzący przez Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c92a986d06deb9f7de10f0682fe46804e6ebb6e7
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069880"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integracja sieci wirtualnej dla usług platformy Azure

Integrowanie usług platformy Azure z siecią wirtualną platformy Azure umożliwia dostęp prywatny z wystąpień usługi wdrożone w sieci wirtualnej.

Usługi platformy Azure można zintegrować z sieci wirtualnej przy użyciu następujących opcji:
- Bezpośrednie wdrażanie dedykowanych wystąpień usługi w sieci wirtualnej. Dedykowanych wystąpień tych usług jest prywatnie dostępna w ramach sieci wirtualnej i z sieciami lokalnymi.
- Rozszerzając sieci wirtualnej do usługi za pośrednictwem punktów końcowych usługi. Punkty końcowe usługi umożliwiają zasobom konkretną usługę być chronione w sieci wirtualnej.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Wdrażaj usługi platformy Azure w sieciach wirtualnych

Mogą komunikować się z zasobami najbardziej platformy Azure za pośrednictwem Internetu przy użyciu publicznych adresów IP. Podczas wdrażania usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md), można komunikować się z zasobami usługi prywatnie, przy użyciu prywatnych adresów IP.

![Usług wdrożonych w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w sieci wirtualnej mogą komunikować się ze sobą prywatnie, przy użyciu prywatnych adresów IP. Przykład bezpośrednio przesyłania danych między HDInsight i programu SQL Server uruchomionego na maszynie wirtualnej w sieci wirtualnej.
- Zasoby mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem lokalnej [Site-to-Site VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być [skomunikowane równorzędnie](virtual-network-peering-overview.md) umożliwiające z zasobami w sieciach wirtualnych do komunikowania się ze sobą przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są w pełni zarządzane przez usługę Azure w celu monitorowania kondycji wystąpień i podaj wymagane skali, na podstawie obciążenia.
- Wystąpienia usługi są wdrażane w dedykowanej podsieci w sieci wirtualnej. Dla ruchu przychodzącego i dostępu sieciowego ruchu wychodzącego, należy otworzyć za pomocą [sieciowe grupy zabezpieczeń](security-overview.md#network-security-groups) dla podsieci, na wskazówki udostępniane przez usługi.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które mogą być wdrażane w sieci wirtualnej

Każda usługa wdrażać bezpośrednio w sieci wirtualnej ma określone wymagania dotyczące routingu i typów ruchu, które muszą być dozwolone, do i z podsieci. Aby uzyskać więcej informacji, zobacz: 
 
- Maszyny wirtualne: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usługa Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usługa Application Gateway (wewnętrzny)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usługa Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Aparat usługi w usłudze Azure kontenera](https://github.com/Azure/acs-engine) za pomocą wtyczki Azure wirtualnych sieci CNI [wtyczki](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Wystąpienie zarządzane usługi Azure SQL Database](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usługi w chmurze](https://msdn.microsoft.com/library/azure/jj156091): sieć wirtualna (klasyczna) tylko

Możesz wdrożyć [wewnętrznych, usługa Azure load balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) załadować saldo wielu zasobów na poprzedniej liście. W niektórych przypadkach usługa automatycznie tworzy i wdraża moduł równoważenia obciążenia, podczas tworzenia zasobu.

## <a name="service-endpoints-for-azure-services"></a>Punkty końcowe usługi dla usług platformy Azure

Nie można wdrożyć niektórych usług platformy Azure w sieciach wirtualnych. Jeśli wybierzesz, po włączeniu punktu końcowego usługi sieci wirtualnej, możesz ograniczyć dostęp do niektórych zasobów usługi do podsieci sieci wirtualnej tylko określone. Dowiedz się więcej o [punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)i usług, które można włączyć dla punktów końcowych.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integracja sieci wirtualnej w wielu usługach platformy Azure

Usługi platformy Azure można wdrożyć w podsieci sieci wirtualnej i bezpieczne krytycznych zasobów usługi do tej podsieci. Na przykład można wdrożyć HDInsight w sieci wirtualnej i zabezpieczenia konta magazynu do podsieci HDInsight.





