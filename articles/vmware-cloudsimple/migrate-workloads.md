---
title: Rozwiązanie VMware platformy Azure według cloudSimple — migrowanie maszyn wirtualnych z obciążeniem do chmury prywatnej
description: W tym artykule opisano sposób migracji maszyn wirtualnych z lokalnego centrum vCenter do centrum vCenter cloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019999"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrowanie maszyn wirtualnych obciążenia z lokalnego środowiska vCenter do prywatnego centrum wirtualnego cloud

Aby przeprowadzić migrację maszyn wirtualnych z lokalnego centrum danych do usługi CloudSimple Private Cloud, dostępnych jest kilka opcji.  Private Cloud zapewnia natywny dostęp do VMware vCenter, a narzędzia obsługiwane przez VMware mogą być używane do migracji obciążenia. W tym artykule opisano niektóre opcje migracji vCenter.

## <a name="prerequisites"></a>Wymagania wstępne

Migracja maszyn wirtualnych i danych z lokalnego centrum danych wymaga łączności sieciowej z centrum danych do środowiska private cloud.  Aby ustanowić łączność sieciową, użyj jednej z następujących metod:

* [Połączenie sieci VPN między lokacją](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a środowiskiem lokalnym a chmurą prywatną.
* Połączenie Global Reach usługi ExpressRoute między lokalnym obwodem usługi ExpressRoute a obwodem CloudSimple ExpressRoute.

Ścieżka sieciowa z lokalnego środowiska vCenter do chmury prywatnej musi być dostępna do migracji maszyn wirtualnych przy użyciu vMotion.  Sieć vMotion w lokalnym centrum vCenter musi mieć możliwości routingu.  Sprawdź, czy zapora zezwala na cały ruch vMotion między lokalnym vCenter i Private Cloud vCenter. (W chmurze prywatnej routing w sieci vMotion jest domyślnie skonfigurowany).

## <a name="migrate-isos-and-templates"></a>Migrowanie iso i szablonów

Aby utworzyć nowe maszyny wirtualne w chmurze prywatnej, użyj iso i szablonów maszyn wirtualnych.  Aby przekazać iso i szablony do usługi Private Cloud vCenter i udostępnić je, należy użyć następującej metody.

1. Przekaż iso do usługi Private Cloud vCenter z interfejsu użytkownika vCenter.
2. [Opublikuj bibliotekę zawartości](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) w centrum vCenter private cloud:

    1. Publikuj lokalną bibliotekę zawartości.
    2. Utwórz nową bibliotekę zawartości w centrum vCenter private cloud.
    3. Subskrybuj opublikowaną lokalną bibliotekę zawartości.
    4. Synchronizuj bibliotekę zawartości w celu uzyskania dostępu do subskrybowanych zawartości.

## <a name="migrate-vms-using-powercli"></a>Migrowanie maszyn wirtualnych przy użyciu funkcji PowerCLI

Aby przeprowadzić migrację maszyn wirtualnych z lokalnego centrum vCenter do centrum wirtualnego private cloud, należy użyć narzędzia VMware PowerCLI lub Cross vCenter Workload Migration Utility dostępnego w programie VMware Labs.  Poniższy przykładowy skrypt przedstawia polecenia migracji PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Aby użyć nazw docelowego serwera vCenter i hostów ESXi, skonfiguruj przekazywanie dns z lokalnego do chmury prywatnej.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrowanie maszyn wirtualnych przy użyciu sieci VPN w warstwie 2 NSX

Ta opcja umożliwia migrację na żywo obciążeń z lokalnego środowiska VMware do chmury prywatnej na platformie Azure.  Dzięki tej rozciągniętej sieci warstwy 2 podsieć lokalnie będzie dostępna w chmurze prywatnej.  Po migracji przypisanie nowego adresu IP nie jest wymagane dla maszyn wirtualnych.

[Migrowanie obciążeń przy użyciu rozciągniętych sieci warstwy 2](migration-layer-2-vpn.md) opisuje sposób wykorzystania sieci VPN warstwy 2 do rozciągnięcia sieci warstwy 2 ze środowiska lokalnego do chmury prywatnej.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrowanie maszyn wirtualnych przy użyciu narzędzi do tworzenia kopii zapasowych i odzyskiwania po awarii

Migrację maszyn wirtualnych do chmury prywatnej można przeprowadzić za pomocą narzędzi do tworzenia kopii zapasowych/przywracania oraz narzędzi do odzyskiwania po awarii.  Użyj chmury prywatnej jako obiektu docelowego do przywracania z kopii zapasowych, które są tworzone przy użyciu narzędzia innej firmy.  Private Cloud może być również używany jako obiekt docelowy odzyskiwania po awarii przy użyciu usługi VMware SRM lub narzędzia innej firmy.

Aby uzyskać więcej informacji za pomocą tych narzędzi, zobacz następujące tematy:

* [Tworzenie kopii zapasowych maszyn wirtualnych obciążenia w chmurze CloudSimple Private Cloud przy użyciu rozwiązania Veeam B&R](backup-workloads-veeam.md)
* [Konfigurowanie chmury CloudSimple Private Cloud jako lokacji odzyskiwania po awarii dla lokalnych obciążeń VMware](disaster-recovery-zerto.md)
