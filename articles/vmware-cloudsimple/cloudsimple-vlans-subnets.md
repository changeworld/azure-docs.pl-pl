---
title: Sieci VLAN i podsieci w VMware Solution by CloudSimple - Azure
description: Dowiedz się więcej o sieci VLAN i podsieci, w chmurze prywatnej CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e88977cc4d99df176116e6be7d8e06adb6297782
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209578"
---
# <a name="vlans-and-subnets-overview"></a>Omówienie sieci VLAN i podsieci

CloudSimple zapewnia sieci, na którym wdrożono usługę CloudSimple region.  Sieć jest w jednej przestrzeni adresowej TCP warstwy 3 routingu, domyślnie włączone.  Wszystkich chmur prywatnych i podsieci, utworzone w tym regionie mogą komunikować się ze sobą bez przeprowadzania dodatkowej konfiguracji.  Można utworzyć grupy portów rozproszone na serwer vCenter za pomocą sieci VLAN.

## <a name="vlans"></a>Sieci VLAN

Sieci VLAN (network Layer 2) są tworzone na chmurę prywatną.  Ruch warstwy 2 pozostaje w ramach chmury prywatnej, dzięki czemu możesz izolować ruch lokalny w chmurze prywatnej.  Utworzone w chmurze prywatnej sieci VLAN może służyć do tworzenia grup portów rozproszonych tylko w danej chmurze prywatnej.  Utworzone w chmurze prywatnej sieci VLAN jest automatycznie konfigurowany dla wszystkich przełączników, które są podłączone do hostów chmury prywatnej.

## <a name="subnets"></a>Podsieci

Po utworzeniu sieci VLAN, definiując przestrzeni adresowej podsieci można utworzyć podsieć. Nie przypisano adres IP z przestrzeni adresowej jako podsieci bramy. Pojedynczy prywatnej przestrzeni adresowej warstwy 3 jest przypisywana do klienta i regionu. Wszelkie RFC 1918 bez nakładających się przestrzeni adresowej, można skonfigurować z sieci lokalnej lub sieci wirtualnej platformy Azure w regionie sieci.

Wszystkie podsieci mogą komunikować się ze sobą domyślnie zmniejszenie konfiguracji obciążenie w przypadku routingu między chmur prywatnych. Wschód Zachód danych różnych komputerów, w tym samym regionie pozostaje w tej samej sieci warstwy 3 i przenosi przez infrastrukturę sieci lokalnej w obrębie regionu. Nie ruchu wychodzącego jest niezbędne do komunikacji między chmur prywatnych w regionie. To podejście eliminuje wszelkich spadek wydajności sieci WAN i wychodzących we wdrażaniu różnych obciążeń w różnych chmur prywatnych.

## <a name="vspherevsan-subnets-cidr-range"></a>zakres CIDR podsieci vSphere/sieci vSAN

Chmura prywatna jest tworzona jako izolowanych stos oprogramowania VMware (hosty ESXi, vCenter, sieć vSAN i NSX) środowiska zarządzanego przez serwer vCenter.  Składniki zarządzania są wdrażane w sieć wybrana dla **vSphere/sieć vSAN podsieci CIDR**.  Zakres CIDR sieci odbywa się w różnych podsieciach podczas wdrażania.

Minimalna vSphere/sieć vSAN podsieci CIDR zakresu prefiksu: **/24** maksymalna vSphere/sieć vSAN podsieci CIDR zakresu prefiksu:   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/sieć vSAN podsieci CIDR zakresu

Wybieranie rozmiaru zakresu CIDR podsieci vSphere/sieć vsan, która ma wpływ na rozmiar chmury prywatnej.  W poniższej tabeli przedstawiono maksymalną liczbę węzłów, które może mieć na podstawie rozmiaru vSphere/sieć vSAN podsieci CIDR.

| Długość prefiksu określonego vSphere/sieć vSAN podsieci CIDR | Maksymalna liczba węzłów |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Podsieci zarządzania utworzone w chmurze prywatnej

Podczas tworzenia chmury prywatnej, są tworzone następujące podsieci zarządzania. 

* **Zarządzanie systemem** -sieci VLAN i podsieci dla hostów ESXi zarządzania sieci, serwer DNS, serwer vCenter.
* **Narzędzia VMotion** -sieci VLAN i podsieci dla sieci vMotion hostów ESXi.
* **Sieć VSAN** -sieci VLAN i podsieci dla sieci vSAN hostów ESXi.
* **NsxtEdgeUplink1** -sieci VLAN i podsieci dla pasm sieci VLAN do sieci zewnętrznej.
* **NsxtEdgeUplink2** -sieci VLAN i podsieci dla pasm sieci VLAN do sieci zewnętrznej.
* **NsxtEdgeTransport** -sieci VLAN i podsieci dla stref transportu kontrolowania zasięgu sieci warstwy 2 w NSX T.
* **NsxtHostTransport** -sieci VLAN i podsieci dla hosta transportu strefy.

### <a name="management-network-cidr-range-breakdown"></a>Podział zakresu CIDR sieć zarządzania

określony zakres CIDR podsieci vSphere/sieć vsan, która jest podzielony na wiele podsieci.  W poniższej tabeli przedstawiono przykład podział dla dozwolonych prefiksów.  W przykładzie użyto **192.168.0.0** jako zakres CIDR.

Przykład:

| Określonej podsieci vSphere/sieć vSAN prefiks/CIDR | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Zarządzanie systemem | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| narzędzia vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transport hosta NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transport krawędzi NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| Uplink1 krawędzi NSX-T | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink2 krawędzi NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie i zarządzanie nimi w sieci VLAN i podsieci](https://docs.azure.cloudsimple.com/create-vlan-subnet/)