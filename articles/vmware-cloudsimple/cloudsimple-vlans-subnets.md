---
title: Sieci VLAN i podsieci w rozwiązaniu VMware według CloudSimple — Azure
description: Informacje o sieciach VLAN i podsieciach w chmurze prywatnej CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6659c50b79237907cf596d65e0ba9fb72113246
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812476"
---
# <a name="vlans-and-subnets-overview"></a>Sieci VLAN i podsieci — Omówienie

CloudSimple zapewnia sieć na region, w którym wdrożono usługę CloudSimple.  Sieć jest pojedynczą przestrzenią adresową TCP warstwy 3 z włączoną funkcją routingu.  Wszystkie chmury prywatne i podsieci utworzone w tym regionie mogą komunikować się ze sobą bez żadnej dodatkowej konfiguracji.  Rozproszone grupy portów można utworzyć na serwerze vCenter przy użyciu sieci VLAN.

![Topologia sieci CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Sieci VLAN

Sieci VLAN (sieć warstwy 2) są tworzone na chmurę prywatną.  Ruch warstwy 2 znajduje się w granicach chmury prywatnej, co umożliwia odizolowanie ruchu lokalnego w chmurze prywatnej.  Sieć VLAN utworzona w chmurze prywatnej może służyć do tworzenia grup portów rozproszonych tylko w tej chmurze prywatnej.  Sieć VLAN utworzona w chmurze prywatnej jest automatycznie konfigurowana na wszystkich przełącznikach połączonych z hostami w chmurze prywatnej.

## <a name="subnets"></a>Podsieci

Można utworzyć podsieć podczas tworzenia sieci VLAN, definiując przestrzeń adresową podsieci. Adres IP z przestrzeni adresowej jest przypisywany jako brama podsieci. Do każdego klienta i regionu jest przypisywany pojedynczy prywatny obszar adresów warstwy 3. W Twoim regionie sieciowym można skonfigurować dowolną nienakładające się przestrzenie adresowe RFC 1918 z siecią lokalną lub siecią wirtualną platformy Azure.

Wszystkie podsieci mogą komunikować się ze sobą domyślnie, co zmniejsza obciążenie związane z konfiguracją routingu między chmurami prywatnymi. Dane wschodnie i zachodnie między komputerami w tym samym regionie pozostają w tej samej sieci warstwy 3 i transferuje się za pośrednictwem infrastruktury sieci lokalnej w regionie. W przypadku komunikacji między chmurami prywatnymi w regionie nie są wymagane żadne dane wyjściowe. Takie podejście eliminuje spadek wydajności sieci WAN/ruchu wychodzącego w przypadku wdrażania różnych obciążeń w różnych chmurach prywatnych.

## <a name="vspherevsan-subnets-cidr-range"></a>zakres CIDR podsieci vSphere/sieci vSAN

Chmura prywatna jest tworzona jako izolowany środowisko VMware (hosty ESXi, vCenter, sieci vSAN i NSX) zarządzane przez serwer vCenter.  Składniki zarządzania są wdrażane w sieci wybranej dla **podsieci vSphere/sieci vSAN CIDR**.  Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania.

Minimalny prefiks zakresu CIDR vSphere/sieci vSAN: **/24** Maksymalna długość podsieci VSphere/sieci vSAN: **/21**

> [!CAUTION]
> Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej. Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.


### <a name="vspherevsan-subnets-cidr-range-limits"></a>limity zakresu CIDR podsieci vSphere/sieci vSAN

Wybranie rozmiaru zakresu CIDR podsieci vSphere/sieci vSAN ma wpływ na rozmiar chmury prywatnej.  W poniższej tabeli przedstawiono maksymalną liczbę węzłów na podstawie rozmiaru vSphere/sieci vSAN podsieci CIDR.

| Określona długość prefiksu CIDR vSphere/sieci vSAN | Maksymalna liczba węzłów |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Podsieci zarządzania utworzone w chmurze prywatnej

Podczas tworzenia chmury prywatnej tworzone są następujące podsieci zarządzania. 

* **Zarządzanie systemem** — sieć VLAN i podsieć dla sieci zarządzania ESXi hosty, serwer DNS, serwer vCenter.
* **VMotion** -VLAN i podsieć dla sieci vMotion Hosts.
* **Sieci vSAN** -VLAN i podsieć dla sieci sieci vSAN Hosts.
* **NsxtEdgeUplink1** -VLAN i podsieć dla sieci VLAN łączących się z siecią zewnętrzną.
* **NsxtEdgeUplink2** -VLAN i podsieć dla sieci VLAN łączących się z siecią zewnętrzną.
* **NsxtEdgeTransport** -VLAN i podsieć dla stref transportu sterują zasięgiem sieci warstwy 2 w NSX-T.
* **NsxtHostTransport** -VLAN i Subnet dla strefy transportu hosta.

### <a name="management-network-cidr-range-breakdown"></a>Podział zakresu CIDR sieci zarządzania

określony zakres CIDR podsieci vSphere/sieci vSAN jest podzielony na wiele podsieci.  W poniższej tabeli przedstawiono przykład podziału dla dozwolonych prefiksów.  W przykładzie używa się **192.168.0.0** jako zakresu CIDR.

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

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
