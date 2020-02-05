---
title: Sieci VLAN i podsieci w rozwiązaniach VMware platformy Azure (Automatyczna synchronizacja)
description: Informacje o sieciach VLAN i podsieciach w usłudze automatycznej synchronizacji w chmurze prywatnej
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d0ce15c782ae70e16f55a28ec8c4b70f3b080f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024895"
---
# <a name="vlans-and-subnets-overview"></a>Sieci VLAN i podsieci — Omówienie

Automatyczna synchronizacja zapewnia sieć na region, w którym wdrożono usługę automatycznej synchronizacji. Sieć jest pojedynczą przestrzenią adresową TCP warstwy 3 z włączoną funkcją routingu. Wszystkie chmury prywatne i podsieci utworzone w tym regionie mogą komunikować się ze sobą bez żadnej dodatkowej konfiguracji. Rozproszone grupy portów można utworzyć na serwerze vCenter przy użyciu sieci VLAN.

![Automatyczna synchronizacja topologii sieci](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Sieci VLAN

Dla każdej chmury prywatnej automatycznej synchronizacji są tworzone sieci VLAN (sieć warstwy 2). Ruch warstwy 2 znajduje się w granicach chmury prywatnej automatycznej synchronizacji, co umożliwia izolowanie ruchu lokalnego w ramach chmury prywatnej automatycznej synchronizacji. Sieć VLAN utworzona w chmurze prywatnej automatycznej synchronizacji może służyć do tworzenia grup portów rozproszonych tylko w tej chmurze prywatnej synchronizacji. Sieć VLAN utworzona w chmurze prywatnej automatycznej synchronizacji jest konfigurowana automatycznie na wszystkich przełącznikach podłączonych do hostów chmury prywatnej.

## <a name="subnets"></a>Podsieci

Można utworzyć podsieć podczas tworzenia sieci VLAN przez zdefiniowanie przestrzeni adresowej podsieci. Adres IP z przestrzeni adresowej jest przypisywany jako brama podsieci. Do każdego klienta i regionu jest przypisywany pojedynczy prywatny obszar adresów warstwy 3. W Twoim regionie sieciowym można skonfigurować dowolną nienakładające się przestrzenie adresowe RFC 1918 z siecią lokalną lub siecią wirtualną platformy Azure.

Wszystkie podsieci mogą komunikować się ze sobą domyślnie, co zmniejsza obciążenie związane z konfiguracją routingu między chmurami prywatnymi automatycznej synchronizacji. Dane wschodnie i zachodnie między komputerami w tym samym regionie pozostają w tej samej sieci warstwy 3 i transferuje się za pośrednictwem infrastruktury sieci lokalnej w regionie. W przypadku komunikacji między chmurami prywatnymi automatycznej synchronizacji w regionie nie są wymagane żadne dane wyjściowe. Takie podejście eliminuje spadek wydajności sieci WAN/ruchu wychodzącego w przypadku wdrażania różnych obciążeń w różnych chmurach prywatnych automatycznej synchronizacji.

## <a name="vspherevsan-subnets-cidr-range"></a>zakres CIDR podsieci vSphere/sieci vSAN

Chmura prywatna do automatycznej synchronizacji jest tworzona jako izolowany środowisko VMware (hosty ESXi, vCenter, sieci vSAN i NSX) zarządzane przez serwer vCenter. Składniki zarządzania są wdrażane w sieci wybranej dla podsieci vSphere/sieci vSAN CIDR. Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania.

* Minimalna prefiks zakresu CIDR vSphere/sieci vSAN: **/24**
* Maksymalna vSphere/sieci vSAN prefiks zakresu CIDR: **/21**

> [!IMPORTANT]
> Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej do automatycznej synchronizacji. Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limity zakresu CIDR podsieci vSphere/sieci vSAN

Wybranie rozmiaru zakresu CIDR podsieci vSphere/sieci vSAN ma wpływ na rozmiar chmury prywatnej automatycznej synchronizacji. W poniższej tabeli przedstawiono maksymalną liczbę węzłów na podstawie rozmiaru vSphere/sieci vSAN podsieci CIDR.

| Określona długość prefiksu CIDR vSphere/sieci vSAN | Maksymalna liczba węzłów |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-an-avs-private-cloud"></a>Podsieci zarządzania utworzone w chmurze prywatnej automatycznej synchronizacji

Podczas tworzenia chmury prywatnej do automatycznej synchronizacji są tworzone następujące podsieci zarządzania.

* **Zarządzanie systemem**. Sieć VLAN i podsieć dla sieci zarządzania ESXi hosty, serwer DNS, serwer vCenter.
* **VMotion**. Sieć VLAN i podsieć dla sieci vMotion hostów ESXi.
* **Sieci vSAN**. Sieć VLAN i podsieć dla sieci sieci vSAN hostów ESXi.
* **NsxtEdgeUplink1**. Sieci VLAN i podsieci dla połączeń sieci VLAN łączących się z siecią zewnętrzną.
* **NsxtEdgeUplink2**. Sieci VLAN i podsieci dla połączeń sieci VLAN łączących się z siecią zewnętrzną.
* **NsxtEdgeTransport**. Sieci VLAN i podsieci dla stref transportu sterują zasięgiem sieci warstwy 2 w NSX-T.
* **NsxtHostTransport**. Sieci VLAN i podsieci dla strefy transportu hosta.

### <a name="management-network-cidr-range-breakdown"></a>Podział zakresu CIDR sieci zarządzania

określony zakres CIDR podsieci vSphere/sieci vSAN jest podzielony na wiele podsieci. W poniższej tabeli przedstawiono przykład podziału dla dozwolonych prefiksów. W przykładzie używa się 192.168.0.0 jako zakresu CIDR.

Przykład:

| Określony vSphere/sieci vSAN podsieci CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Zarządzanie systemem | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| Sieci vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transport hosta NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transport NSX-T Edge | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX — T Uplink1 Edge | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX — T uplink2 Edge | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md)
