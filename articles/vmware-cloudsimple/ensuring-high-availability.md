---
title: Zapewnij wysoką dostępność aplikacji podczas uruchamiania w vmware na platformie Azure
description: W tym artykule opisano funkcje cloudsimple o wysokiej dostępności w celu rozwiązania typowych scenariuszy awarii aplikacji dla aplikacji uruchomionych w chmurze prywatnej CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025354"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Zapewnij wysoką dostępność aplikacji podczas uruchamiania w vmware na platformie Azure

Rozwiązanie CloudSimple zapewnia wysoką dostępność aplikacji działających na VMware w środowisku platformy Azure. W poniższej tabeli wymieniono scenariusze awarii i skojarzone funkcje wysokiej dostępności.

| Scenariusz awarii | Aplikacja zabezpieczona? | Funkcja platformy HA | Funkcja VMware HA | Funkcja usługi Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Awaria dysku | TAK | Szybka wymiana nieudanego węzła | [Informacje o domyślnych zasadach magazynu vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Awaria wentylatora | TAK | Nadmiarowe wentylatory, szybka wymiana nieudanego węzła |  |  |
| Awaria karty sieciowej | TAK | Nadmiarowa karta sieciowa, szybka wymiana nieudanego węzła
| Awaria zasilania hosta | TAK | Nadmiarowe zasilanie |  |  |
| Błąd hosta ESXi | TAK | szybka wymiana nieudanego węzła | [Wysoka dostępność VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Błąd maszyny Wirtualnej | TAK | [Moduły równoważenia obciążenia](load-balancers.md)  | [Wysoka dostępność VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) | Moduł równoważenia obciążenia platformy Azure dla bezstanowych maszyn wirtualnych VMware |
| Błąd portu przełącznika liścia | TAK | Nadmiarowa karta sieciowa |  |  |
| Błąd przełącznika liścia | TAK | Nadmiarowe przełączniki liściowe |  |  |
| Awaria stelaża | TAK | Grupy umieszczania |  |  |
| Łączność sieciowa z lokalnym kontrolerem domeny | TAK  | Nadmiarowe usługi sieciowe |  | Nadmiarowe obwody ER |
| Łączność sieciowa z platformą Azure | TAK | |  | Nadmiarowe obwody ER |
| Błąd centrum danych | TAK |  |  | Strefy dostępności |
| Awaria regionalna | TAK  |  |  | Regiony świadczenia usługi Azure |

Rozwiązanie Azure VMware firmy CloudSimple zapewnia następujące funkcje wysokiej dostępności.

## <a name="fast-replacement-of-failed-node"></a>Szybka wymiana nieudanego węzła

Oprogramowanie płaszczyzny sterowania CloudSimple stale monitoruje kondycję klastrów VMware i wykrywa awarię węzła ESXi. Następnie automatycznie dodaje nowy host ESXi do klastra VMware, którego dotyczy problem, z puli łatwo dostępnych węzłów i usuwa węzeł, którego nie powiodło się z klastra. Ta funkcja zapewnia szybkie przywrócenie wolnych mocy produkcyjnych w klastrze VMware, dzięki czemu zostanie przywrócona odporność klastra zapewniana przez usługi vSAN i VMware HA.

## <a name="placement-groups"></a>Grupy umieszczania

Użytkownik, który tworzy chmurę prywatną, może wybrać region platformy Azure i grupę miejsc docelowych w wybranym regionie. Grupa miejsc docelowych to zestaw węzłów rozmieszczonych w wielu stojakach, ale w tym samym segmencie sieci kręgosłupa. Węzły w tej samej grupie miejsc docelowych mogą dotrzeć do siebie z maksymalnie dwoma dodatkowymi przeskokami przełączników. Grupa miejsc docelowych znajduje się zawsze w jednej strefie dostępności platformy Azure i obejmuje wiele stojaków. Płaszczyzna sterowania CloudSimple dystrybuuje węzły chmury prywatnej w wielu stojakach na podstawie najlepszych starań. Węzły w różnych grupach miejsc docelowych są gwarantowane do umieszczenia w różnych stojakach.

## <a name="availability-zones"></a>Strefy dostępności

Strefy dostępności to oferta o wysokiej dostępności, która chroni aplikacje i dane przed awariami centrów danych. Strefy dostępności to specjalne lokalizacje fizyczne w regionie platformy Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Każdy region ma jedną strefę dostępności. Aby uzyskać więcej informacji, zobacz [Co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Nadmiarowe obwody usługi Azure ExpressRoute

Łączność centrum danych z witryną wirtualną platformy Azure przy użyciu usługi ExpressRoute ma nadmiarowe obwody, aby zapewnić łącze łączności sieciowej o wysokiej dostępności.

## <a name="redundant-networking-services"></a>Nadmiarowe usługi sieciowe

Wszystkie usługi sieciowe CloudSimple dla chmury prywatnej (w tym sieć VLAN, zapora sieciowa, publiczne adresy IP, Internet i sieć VPN) są zaprojektowane tak, aby były wysoce dostępne i mogły obsługiwać usługę SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Moduł równoważenia obciążenia warstwy Azure 7 dla bezstanowych maszyn wirtualnych VMware

Użytkownicy mogą umieścić moduł równoważenia obciążenia warstwy Azure 7 przed bezstanowymi maszynami wirtualnymi warstwy sieci web uruchomionymi w środowisku VMware, aby osiągnąć wysoką dostępność dla warstwy sieci web.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Region platformy Azure to zestaw centrów danych wdrożonych w obwodzie zdefiniowanym przez opóźnienie i połączonych za pośrednictwem dedykowanej regionalnej sieci o małych opóźnieniach. Aby uzyskać szczegółowe informacje, zobacz [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions).
