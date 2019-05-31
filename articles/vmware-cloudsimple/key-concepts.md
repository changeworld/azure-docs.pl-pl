---
title: Kluczowe pojęcia związane z administrowanie usługą Azure VMware Solution by CloudSimple
description: W tym artykule opisano kluczowe pojęcia związane z administrowanie usługą Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358171"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Kluczowe pojęcia związane z administracyjnej Azure VMware Solution by CloudSimple

Administrowanie usługą Azure VMware Solution by CloudSimple wymaga zrozumienia następujące pojęcia.

* Usługa CloudSimple (wyświetlane jako rozwiązania programu VMware na platformie Azure przez CloudSimple - Service)
* Węzeł CloudSimple (wyświetlane jako rozwiązania programu VMware na platformie Azure przez CloudSimple - Node)
* Chmura prywatna CloudSimple
* Service Network
* Maszyna wirtualna CloudSimple (wyświetlane jako rozwiązania programu VMware na platformie Azure przez CloudSimple - maszyny wirtualnej)

## <a name="cloudsimple-service"></a>Usługa CloudSimple

Usługa CloudSimple umożliwia tworzenie i zarządzanie wszystkimi zasobami skojarzonymi z rozwiązaniami VMware przez CloudSimple w witrynie Azure portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi. 

Dowiedz się więcej o [CloudSimple usługi](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Węzeł CloudSimple

Węzeł CloudSimple jest dedykowanym, komputerach bez systemu operacyjnego, hiperzbieżnego zasobów obliczeniowych i magazynowych hosta, w której funkcja hypervisor VMware ESXi została wdrożona. Ten węzeł jest następnie włączona do oprogramowania VMware vSphere, vCenter, sieć vSAN i NSX platform. Usługi sieciowe CloudSimple i edge usługi sieciowe również są włączone. Każdy węzeł służy jako jednostki pojemności obliczeniowej i magazynowej, który można kupić, aby utworzyć [Chmurami prywatnymi CloudSimple](cloudsimple-private-cloud.md). Kup lub rezerwa węzły w regionie, w których usługa CloudSimple jest dostępna.


Dowiedz się więcej o [CloudSimple węzłów](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>Chmura prywatna CloudSimple

Chmurę prywatną CloudSimple jest izolowanym środowisku stos oprogramowania VMware zarządzane przez serwer vCenter w jego własnej domeny zarządzania. Stos oprogramowania VMware zawiera hosty, vSphere ESXi, vCenter, sieć vSAN i NSX.  Uruchamiany stosu na dedykowanych węzłów (dedykowanej i odizolowanej bez systemu operacyjnego sprzętu) i jest używane przez użytkowników za pośrednictwem natywnych narzędzi VMware vCenter i NSX menedżera. Węzły dedykowane są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każda Chmura prywatna może być segmentowanych i chronione przy użyciu sieci usługi, takie jak podsieci/sieci VLAN i zapory tabel.  Połączenia usługi w środowisku lokalnym i sieci platformy Azure są tworzone przy użyciu bezpiecznego, prywatnych sieci VPN i połączeń usługi ExpressRoute platformy Azure.

Dowiedz się więcej o [Chmura prywatna CloudSimple](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Usługi sieciowe

Usługa CloudSimple zapewnia sieci, na którym wdrożono usługę CloudSimple region. Sieć jest w jednej przestrzeni adresowej TCP warstwy 3 routingu, domyślnie włączone. Wszystkich chmur prywatnych i podsieci, utworzone w tym regionie komunikują się ze sobą bez przeprowadzania dodatkowej konfiguracji. Utworzysz grupy portów rozproszonych w programie vcenter za pomocą sieci VLAN.  Następujące funkcje sieci służy do konfigurowania i zabezpieczanie zasobów obciążenia w chmurze prywatnej.

* [Podsieci/sieci VLAN](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie sieci platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Maszyna wirtualna CloudSimple

Usługa CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware w witrynie Azure portal. Klastry lub pule zasobów ze środowiska vSphere mogą być mapowane do subskrypcji, w którym usługa została utworzona.

Dowiedz się więcej o usługach:

* [Maszyny wirtualne CloudSimple](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
