---
title: Sieci wirtualnej do usług platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zalet wdrażania zasobów w sieci wirtualnej. Zasobów w sieci wirtualne mogą komunikować się ze sobą i zasobów lokalnych, bez ruch przechodzi przez Internet.
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
ms.openlocfilehash: eeb5fd765a6cad10be5706162566605feaa8af4c
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802565"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integracji sieci wirtualnej do usług platformy Azure

Integrowanie usług platformy Azure do sieci wirtualnej platformy Azure umożliwia prywatny dostęp z wystąpień usługi wdrożony w sieci wirtualnej.

Usługi platformy Azure można zintegrować z sieci wirtualnej przy użyciu następujących opcji:
- Bezpośrednie wdrażanie dedykowanego wystąpienia usługi w sieci wirtualnej. Dedykowany wystąpień tych usług są prywatnie dostępne w sieci wirtualnej i z sieci lokalnej.
- Rozszerzając sieci wirtualnej z usługą za pomocą punktów końcowych usługi. Punkty końcowe usługi Zezwalaj pojedynczej usługi zasobów być zabezpieczone do sieci wirtualnej.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Wdrażanie usług platformy Azure w sieci wirtualnych

Może komunikować się z zasobami najbardziej Azure za pośrednictwem Internetu za pośrednictwem publicznych adresów IP. Podczas wdrażania usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md), można komunikować się z zasobami usługi prywatnie, przy użyciu prywatnych adresów IP.

![Usługi wdrożony w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnych zapewnia następujące możliwości:

- Zasoby w sieci wirtualnej mogą komunikować się ze sobą prywatnie, przy użyciu prywatnych adresów IP. Przykład bezpośrednio przesyłania danych między HDInsight i programu SQL Server uruchomionego na maszynie wirtualnej w sieci wirtualnej.
- Lokalnych zasobów mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem [VPN lokacja-lokacja (bramy sieci VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być [połączyć za pomocą](virtual-network-peering-overview.md) umożliwiające zasobów w sieci wirtualne do komunikowania się ze sobą używania prywatnych adresów IP.
- Wystąpienie usługi w sieci wirtualnej pełni są zarządzane przez usługę Azure w celu monitorowania kondycji wystąpień i podaj wymagane skali, oparte na obciążenia.
- Wystąpienie usługi są wdrażane do dedykowanych podsieci w sieci wirtualnej. Ruchu przychodzącego i wychodzącego dostępu muszą być otwarte przez [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups) podsieci, na wskazówki udostępniane przez usługi.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które można wdrożyć w sieci wirtualnej

Każda usługa bezpośrednio wdrożony w sieci wirtualnej ma szczególne wymagania dotyczące routingu i typów ruchu, który może do i z podsieci. Aby uzyskać więcej informacji, zobacz: 
 
- Maszyny wirtualne: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [systemu Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sieci szkieletowej usług](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Brama aplikacji (wewnętrzny)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usługa Azure Kubernetes (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Aparat usługi kontenera platformy Azure](https://github.com/Azure/acs-engine) z CNI sieci wirtualnych Azure [wtyczki](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): sieć wirtualna (klasyczna) tylko
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Usługi w chmurze](https://msdn.microsoft.com/library/azure/jj156091): sieć wirtualna (klasyczna) tylko

Można wdrożyć [usługi równoważenia obciążenia Azure wewnętrznego](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) załadować wiele zasobów na poprzedniej liście zrównoważenia. W niektórych przypadkach usługa automatycznie tworzy i wdraża moduł równoważenia obciążenia, podczas tworzenia zasobu.

## <a name="service-endpoints-for-azure-services"></a>Punkty końcowe usługi dla usług Azure

Nie można wdrożyć niektórych usług platformy Azure w sieciach wirtualnych. Jeśli wybierzesz, włączając punkt końcowy usługi sieci wirtualnej, można ograniczyć dostęp do niektórych zasobów usługi do podsieci tylko określonych sieci wirtualnej. Dowiedz się więcej o [punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)i usług, które można włączyć dla punktów końcowych.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integracji sieci wirtualnej w wielu usługach Azure

Usługa Azure można wdrożyć w podsieci sieci wirtualnej i Usługa bezpiecznego krytyczne zasoby do tej podsieci. Na przykład można wdrożyć usługi HDInsight w sieci wirtualnej i zabezpieczyć konto magazynu do podsieci usługi HDInsight.





