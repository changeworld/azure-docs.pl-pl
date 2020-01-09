---
title: Kluczowe pojęcia związane z administrowaniem rozwiązaniem VMware przez CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje kluczowe pojęcia związane z administrowaniem rozwiązaniem VMware platformy Azure przez CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d4de25f281f9164d5cfe3de64d2ca644593a8dd3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450783"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Kluczowe pojęcia dotyczące administrowania rozwiązaniem VMware platformy Azure przez CloudSimple

Zarządzanie rozwiązaniem VMware platformy Azure przez CloudSimple wymaga poznania następujących koncepcji:

* Usługa CloudSimple, która jest wyświetlana jako rozwiązanie VMware platformy Azure przez CloudSimple-Service
* Węzeł CloudSimple, który jest wyświetlany jako rozwiązanie Azure VMware według CloudSimple-Node
* CloudSimple chmurę prywatną
* Sieć usług
* CloudSimple maszynę wirtualną, która jest wyświetlana jako rozwiązanie VMware platformy Azure przez CloudSimple — maszyna wirtualna

## <a name="cloudsimple-service"></a>Usługa CloudSimple

Usługa CloudSimple umożliwia tworzenie wszystkich zasobów skojarzonych z rozwiązaniami VMware i zarządzanie nimi za pomocą CloudSimple z Azure Portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi.

Dowiedz się więcej o [usłudze CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Węzeł CloudSimple

Węzeł CloudSimple to dedykowany Host obliczeniowy i magazyn, do którego wdrożono funkcję hypervisor VMware ESXi. Ten węzeł jest następnie dołączany do platform VMware vSphere, vCenter, sieci vSAN i NSX. Usługi sieciowe CloudSimple i usługi sieci brzegowej również są włączone. Każdy węzeł służy jako jednostka pojemności obliczeniowej i magazynu, którą można udostępnić do tworzenia [chmur prywatnych CloudSimple](cloudsimple-private-cloud.md). W regionie, w którym jest dostępna usługa CloudSimple, można zainicjować obsługę administracyjną lub zarezerwować węzły.

Dowiedz się więcej o [węzłach CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple chmurę prywatną

Chmura prywatna CloudSimple to izolowane środowisko stosu VMware zarządzane przez serwer vCenter we własnej domenie zarządzania. Stos VMware obejmuje hosty ESXi, vSphere, vCenter, sieci vSAN i NSX. Stos działa na dedykowanych węzłach (dedykowany i izolowany sprzęt bez systemu operacyjnego) i jest używany przez użytkowników za pomocą natywnych narzędzi VMware, które obejmują program vCenter i NSX Manager. Węzły dedykowane są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każdą chmurę prywatną można podzielić na segmenty i zabezpieczyć przy użyciu usług sieciowych, takich jak sieci VLAN i podsieci oraz tabele zapory. Połączenia ze środowiskiem lokalnym i siecią platformy Azure są tworzone przy użyciu bezpiecznych, prywatnych sieci VPN i połączeń usługi Azure ExpressRoute.

Dowiedz się więcej o [chmurze prywatnej CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Sieć usług

Usługa CloudSimple zapewnia sieć na region, w którym wdrożono usługę CloudSimple. Sieć jest pojedynczą przestrzenią adresową TCP warstwy 3 z włączoną funkcją routingu. Wszystkie chmury prywatne i podsieci utworzone w tym regionie komunikują się ze sobą bez żadnej dodatkowej konfiguracji. Na serwerze vCenter tworzysz rozproszone grupy portów przy użyciu sieci VLAN. Aby skonfigurować i zabezpieczyć zasoby obciążeń w chmurze prywatnej, można użyć następujących funkcji sieciowych:

* [Sieci VLAN i podsieci](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie sieciowe platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple maszynę wirtualną

Za pomocą usługi CloudSimple można zarządzać maszynami wirtualnymi VMware z poziomu Azure Portal. Co najmniej jeden klaster lub pule zasobów ze środowiska vSphere można zamapować na subskrypcję, w której jest tworzona usługa.

Dowiedz się więcej o usługach:

* [CloudSimple maszyn wirtualnych](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
