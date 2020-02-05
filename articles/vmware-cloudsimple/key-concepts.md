---
title: Kluczowe pojęcia związane z administrowaniem rozwiązaniami VMware firmy Azure (Automatyczna synchronizacja)
description: Opisuje kluczowe pojęcia związane z administrowaniem rozwiązaniami VMware platformy Azure (Automatyczna synchronizacja)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025235"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Kluczowe pojęcia związane z administracją rozwiązań VMware platformy Azure (Automatyczna synchronizacja)

Administrowanie rozwiązaniami VMware platformy Azure (Automatyczna synchronizacja) wymaga znajomości następujących pojęć:

* Usługa automatycznej synchronizacji, która jest wyświetlana jako rozwiązanie Azure VMware (Automatyczna synchronizacja) — usługa
* Węzeł automatycznej synchronizacji, który jest wyświetlany jako rozwiązanie Azure VMware (Automatyczna synchronizacja) — węzeł
* Automatyczna synchronizacja chmury prywatnej
* Sieć usług
* Automatyczna synchronizacja maszyn wirtualnych, która jest wyświetlana na platformie Azure VMware Solutions (Automatyczna synchronizacja) — maszyna wirtualna

## <a name="avs-service"></a>Usługa automatycznej synchronizacji

Za pomocą usługi automatycznej synchronizacji można utworzyć wszystkie zasoby skojarzone z rozwiązaniami VMware (Automatyczna synchronizacja) i zarządzać nimi z poziomu Azure Portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi.

Dowiedz się więcej na temat [usługi automatycznej synchronizacji](cloudsimple-service.md).

## <a name="avs-node"></a>Węzeł automatycznej synchronizacji

Węzeł automatycznej synchronizacji jest dedykowanym hostem obliczeniowym i magazynem, na którym jest wdrożona VMware ESXi funkcja hypervisor. Ten węzeł jest następnie dołączany do platform VMware vSphere, vCenter, sieci vSAN i NSX. Są również włączone automatyczna synchronizacja usług sieciowych i usług sieci brzegowej. Każdy węzeł służy jako jednostka pojemności obliczeniowej i magazynu, którą można udostępnić do tworzenia [chmur prywatnych](cloudsimple-private-cloud.md). W regionie, w którym jest dostępna usługa automatycznej synchronizacji, zastrzega się lub rezerwuje węzły.

Dowiedz się więcej o [węzłach automatyczna synchronizacja](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Automatyczna synchronizacja chmury prywatnej

Chmura prywatna w wersji zaszeregowej to izolowane środowisko stosu VMware zarządzane przez serwer vCenter we własnej domenie zarządzania. Stos VMware obejmuje hosty ESXi, vSphere, vCenter, sieci vSAN i NSX. Stos działa na dedykowanych węzłach (dedykowany i izolowany sprzęt bez systemu operacyjnego) i jest używany przez użytkowników za pomocą natywnych narzędzi VMware, które obejmują program vCenter i NSX Manager. Węzły dedykowane są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każda chmura prywatna w wersji zaautomatycznej można podzielić na segmenty i zabezpieczyć przy użyciu usług sieciowych, takich jak sieci VLAN i podsieci oraz tabele zapory. Połączenia ze środowiskiem lokalnym i siecią platformy Azure są tworzone przy użyciu bezpiecznych, prywatnych sieci VPN i połączeń usługi Azure ExpressRoute.

Dowiedz się więcej o [automatycznej synchronizacji chmury prywatnej](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Sieć usług

Usługa automatycznej dostępności zapewnia sieć na region, w którym wdrożono usługę automatycznej synchronizacji. Sieć jest pojedynczą przestrzenią adresową TCP warstwy 3 z włączoną funkcją routingu. Wszystkie prywatne chmury i podsieci utworzone w tym regionie komunikują się ze sobą bez żadnej dodatkowej konfiguracji. Na serwerze vCenter tworzysz rozproszone grupy portów przy użyciu sieci VLAN. Poniższe funkcje sieciowe umożliwiają konfigurowanie i Zabezpieczanie zasobów obciążeń w chmurze prywatnej automatycznej synchronizacji:

* [Sieci VLAN i podsieci](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie sieciowe platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Automatyczna synchronizacja maszyn wirtualnych

Za pomocą usługi automatycznej synchronizacji można zarządzać maszynami wirtualnymi VMware z poziomu Azure Portal. Co najmniej jeden klaster lub pule zasobów ze środowiska vSphere można zamapować na subskrypcję, w której jest tworzona usługa.

Dowiedz się więcej o usługach:

* [Automatyczna synchronizacja maszyn wirtualnych](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
