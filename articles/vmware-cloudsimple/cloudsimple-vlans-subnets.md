---
title: Sieci VLAN i podsieci w usłudze Azure VMware Solution by CloudSimple
description: Dowiedz się więcej o sieciach VLAN i podsieciach w chmurze cloudsimple private cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024895"
---
# <a name="vlans-and-subnets-overview"></a>Omówienie sieci WIRTUALNYCH i podsieci

CloudSimple zapewnia sieć dla regionu, w którym wdrożono usługę CloudSimple.  Sieć jest pojedynczą przestrzenią adresową warstwy TCP 3 z domyślnie włączoną obsługą routingu.  Wszystkie chmury prywatne i podsieci utworzone w tym regionie mogą komunikować się ze sobą bez dodatkowej konfiguracji.  Rozproszone grupy portów można tworzyć w centrum vCenter przy użyciu sieci VLAN.

![Topologia sieci CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Sieci vlan

Sieć VLAN (sieć warstwy 2) jest tworzona dla każdej chmury prywatnej.  Ruch warstwy 2 pozostaje w granicach chmury prywatnej, co pozwala na izolowanie ruchu lokalnego w chmurze prywatnej.  Sieć VLAN utworzona w chmurze prywatnej może służyć do tworzenia rozproszonych grup portów tylko w tej chmurze prywatnej.  Sieć VLAN utworzona w chmurze prywatnej jest automatycznie konfigurowana na wszystkich przełącznikach połączonych z hostami chmury prywatnej.

## <a name="subnets"></a>Podsieci

Podsieci można utworzyć sieć Sieci VLAN, definiując przestrzeń adresową podsieci. Adres IP z przestrzeni adresowej jest przypisywany jako brama podsieci. Jedna prywatna przestrzeń adresowa warstwy 3 jest przypisywana dla każdego klienta i regionu. W regionie sieci można skonfigurować dowolną nienakładającą się przestrzeń adresową RFC 1918 z siecią lokalną lub siecią wirtualną platformy Azure.

Wszystkie podsieci mogą komunikować się ze sobą domyślnie, zmniejszając obciążenie konfiguracji routingu między chmurami prywatnymi. Dane wschód-zachód na komputerach w tym samym regionie pozostają w tej samej sieci warstwy 3 i są przesyłane przez lokalną infrastrukturę sieciową w regionie. Nie jest wymagany ruch wychodzący do komunikacji między chmurami prywatnymi w regionie. Takie podejście eliminuje wszelkie wyładowywania wydajności usługi WAN/wychodzące podczas wdrażania różnych obciążeń w różnych chmurach prywatnych.

## <a name="vspherevsan-subnets-cidr-range"></a>Zakres podsieci vSphere/vSAN CIDR

Private Cloud jest tworzony jako izolowany stos VMware (hosty ESXi, vCenter, vSAN i NSX) środowisko zarządzane przez serwer vCenter.  Składniki zarządzania są wdrażane w sieci wybranej dla podsieci CIDR vSphere/vSAN.  Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania.

* Minimalny prefiks zakresu podsieci vSphere/vSAN: **/24**
* Maksymalny prefiks zakresu podsieci vSphere/vSAN: **/21**

> [!CAUTION]
> Adresy IP w zakresie cidr vSphere/vSAN są zarezerwowane do użytku przez infrastrukturę private cloud.  Nie używaj adresu IP w tym zakresie na żadnej maszynie wirtualnej.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSfery/vSAN podsieci limity zakresu CIDR

Wybranie podsieci vSphere/vSAN o rozmiarze zakresu CIDR ma wpływ na rozmiar chmury prywatnej.  W poniższej tabeli przedstawiono maksymalną liczbę węzłów, które można mieć na podstawie rozmiaru podsieci CIDR vSphere/vSAN.

| Określone podsieci vSphere/vSAN długość prefiksu CIDR | Maksymalna liczba węzłów |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Podsieci zarządzania utworzone w chmurze prywatnej

Następujące podsieci zarządzania są tworzone podczas tworzenia chmury prywatnej.

* **Zarządzanie systemem**. Sieć VLAN i podsieć dla sieci zarządzania hostów ESXi, serwera DNS, serwera vCenter.
* **VMotion**. Sieć VLAN i podsieć sieci vMotion hostów ESXi.
* **VSAN**. Sieć VLAN i podsieć sieci vSAN hostów ESXi.
* **NsxtEdgeUplink1**. Sieć VLAN i podsieć dla łączy nadrzędnych sieci VLAN z siecią zewnętrzną.
* **NsxtEdgeUplink2**. Sieć VLAN i podsieć dla łączy nadrzędnych sieci VLAN z siecią zewnętrzną.
* **NsxtEdgeTransport**. Sieć VLAN i podsieć stref transportu kontrolują zasięg sieci warstwy 2 w sieci NSX-T.
* **NsxtHostTransport**. Sieć VLAN i podsieć dla strefy transportu hosta.

### <a name="management-network-cidr-range-breakdown"></a>Podział zakresu cidr sieci zarządzania

Określone podsieci vSphere/vSAN są podzielone na wiele podsieci.  W poniższej tabeli przedstawiono przykład podziału dozwolonych prefiksów.  W przykładzie użyto 192.168.0.0 jako zakres CIDR.

Przykład:

| Określone podsieci vSphere/vSAN CIDR/prefiks | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Zarządzanie systemem | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion (Rozwiązanie) | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| Vsan | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transport hosta NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transport krawędziowy NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md)
