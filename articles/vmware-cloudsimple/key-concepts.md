---
title: Kluczowe pojęcia związane z administrowanie usługą Azure VMware Solution by CloudSimple
description: W tym artykule opisano kluczowe pojęcia związane z administrowanie usługą Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d87871fe8faaaab2e56d4a0426cd5e5f0899c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595620"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Kluczowe pojęcia związane z administracyjnej Azure VMware Solution by CloudSimple

Administrowanie usługą Azure VMware Solution by CloudSimple wymaga zrozumienia następujące pojęcia:

* Usługa CloudSimple, która jest wyświetlana jako rozwiązania programu VMware na platformie Azure przez CloudSimple - usługi
* CloudSimple węzła, który jest wyświetlany jako rozwiązania programu VMware na platformie Azure przez CloudSimple - Node
* Chmura prywatna CloudSimple
* Usługi sieciowe
* CloudSimple maszynę wirtualną, która jest wyświetlana jako rozwiązania programu VMware na platformie Azure przez CloudSimple - maszyny wirtualnej

## <a name="cloudsimple-service"></a>Usługa CloudSimple

W usłudze CloudSimple można tworzyć i zarządzanie wszystkimi zasobami skojarzonymi z rozwiązaniami VMware przez CloudSimple w witrynie Azure portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi.

Dowiedz się więcej o [usługi CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Węzeł CloudSimple

Węzeł CloudSimple to hosta zasobów obliczeniowych i magazynowych hiperzbieżnego dedykowaną, bez systemu operacyjnego, w której jest wdrożony funkcja hypervisor VMware ESXi. Ten węzeł jest następnie włączona do oprogramowania VMware vSphere, vCenter, sieć vSAN i NSX platform. Usługi sieciowe CloudSimple i edge usługi sieciowe również są włączone. Każdy węzeł służy jako jednostki pojemności obliczeniowej i magazynowej, który można udostępnić, aby utworzyć [chmur prywatnych CloudSimple](cloudsimple-private-cloud.md). Aprowizowanie lub rezerwa węzły w regionie, w których usługa CloudSimple jest dostępna.


Dowiedz się więcej o [węzłów CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Chmura prywatna CloudSimple

CloudSimple Chmura prywatna jest izolowanym środowisku stos oprogramowania VMware zarządzane przez serwer vCenter w jego własnej domeny zarządzania. Stos oprogramowania VMware zawiera hosty, vSphere ESXi, vCenter, sieć vSAN i NSX. Uruchamiany stosu na dedykowanych węzłów (dedykowanej i odizolowanej sprzętu bez systemu operacyjnego) i jest używane przez użytkowników za pośrednictwem natywnych narzędzi VMware vCenter i NSX menedżera. Węzły dedykowane są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każdej chmury prywatnej można segmentowanych i zabezpieczone za pomocą usług sieciowych, takich jak sieci VLAN i podsieci oraz tabele zapory. Połączenia usługi w środowisku lokalnym i sieci platformy Azure są tworzone przez przy użyciu bezpiecznej, prywatne sieci VPN i usługi Azure ExpressRoute połączeń.

Dowiedz się więcej o [chmury prywatnej CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Usługi sieciowe

Usługa CloudSimple zapewnia sieci, na którym wdrożono usługę CloudSimple region. Sieć jest w jednej przestrzeni adresowej TCP warstwy 3 routingu, domyślnie włączone. Wszystkich chmur prywatnych i podsieci, utworzone w tym regionie komunikują się ze sobą bez przeprowadzania dodatkowej konfiguracji. Tworzenie grup portów rozproszonych na serwer vCenter odbywa się przy użyciu sieci VLAN. Aby skonfigurować i zabezpieczyć zasoby obciążenia w chmurze prywatnej, można użyć następujących funkcji sieci:

* [Sieci VLAN i podsieci](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie sieci platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Maszyna wirtualna CloudSimple

Usługa CloudSimple maszyny wirtualne VMware można zarządzać w witrynie Azure portal. Klastry lub pule zasobów ze środowiska vSphere mogą być mapowane do subskrypcji, w którym usługa została utworzona.

Dowiedz się więcej o usługach:

* [Maszyny wirtualne CloudSimple](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
