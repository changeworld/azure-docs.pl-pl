---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Migrowanie maszyn wirtualnych obciążeń do automatycznej synchronizacji chmury prywatnej
description: Zawiera opis sposobu migrowania maszyn wirtualnych z lokalnego programu vCenter do wersji zabudowanej programu vCenter chmury prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019999"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>Migrowanie maszyn wirtualnych obciążeń z lokalnego programu vCenter do automatycznej synchronizacji środowiska usługi vCenter w chmurze prywatnej

Aby przeprowadzić migrację maszyn wirtualnych z lokalnego centrum danych do chmury prywatnej automatycznej synchronizacji, dostępne są różne opcje. Chmura prywatna automatycznej synchronizacji zapewnia natywny dostęp do programu VMware vCenter, a narzędzia obsługiwane przez program VMware mogą służyć do migracji obciążeń. W tym artykule opisano niektóre opcje migracji programu vCenter.

## <a name="prerequisites"></a>Wymagania wstępne

Migracja maszyn wirtualnych i danych ze środowiska lokalnego do systemu www wymaga łączności sieciowej z centrum dane w środowisku chmury prywatnej. Aby nawiązać połączenie sieciowe, użyj jednej z następujących metod:

* [Połączenie sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między środowiskiem lokalnym a chmurą prywatną do automatycznej synchronizacji.
* ExpressRoute Global Reach połączenie między lokalnym obwodem usługi ExpressRoute i obwodem ExpressRoutea o automatycznej synchronizacji.

Ścieżka sieciowa z lokalnego środowiska vCenter do chmury prywatnej automatycznej synchronizacji musi być dostępna do migracji maszyn wirtualnych przy użyciu vMotion. Sieć vMotion na lokalnym serwerze vCenter musi mieć możliwości routingu. Sprawdź, czy zapora zezwala na cały ruch vMotion między lokalnym programem vCenter i automatycznej synchronizacji chmury prywatnej. (W chmurze prywatnej automatycznej synchronizacji usługa Routing w sieci vMotion jest domyślnie konfigurowana).

## <a name="migrate-isos-and-templates"></a>Migrowanie obrazów ISO i szablonów

Aby tworzyć nowe maszyny wirtualne w chmurze prywatnej automatycznej synchronizacji, użyj szablonów obrazów ISO i maszyn wirtualnych. Aby przekazać obrazów ISO i szablony do programu vCenter-chmurze prywatnej chmury i udostępnić je, użyj następującej metody.

1. Przekaż plik ISO do narzędzia do automatycznej synchronizacji z chmurą prywatną vCenter z interfejsu użytkownika vCenter.
2. [Opublikowanie biblioteki zawartości](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) w chmurze prywatnej chmury programu vCenter:

    1. Opublikuj lokalną bibliotekę zawartości.
    2. Utwórz nową bibliotekę zawartości w chmurze prywatnej chmury automatycznej.
    3. Zasubskrybuj opublikowaną lokalną bibliotekę zawartości.
    4. Zsynchronizuj bibliotekę zawartości, aby uzyskać dostęp do subskrybowanej zawartości.

## <a name="migrate-vms-using-powercli"></a>Migrowanie maszyn wirtualnych przy użyciu PowerCLI

Aby przeprowadzić migrację maszyn wirtualnych z lokalnego programu vCenter do narzędzia do automatycznej synchronizacji w chmurze prywatnej, użyj oprogramowania VMware PowerCLI lub oprogramowania do migracji obciążeń Cross vCenter dostępnych w oprogramowaniu VMware Labs. Poniższy przykładowy skrypt przedstawia polecenia migracji PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Aby korzystać z nazw docelowych hostów programu vCenter Server i ESXi, skonfiguruj przekazywanie DNS z miejsca lokalnego do chmury prywatnej automatycznej synchronizacji.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrowanie maszyn wirtualnych przy użyciu sieci VPN NSX warstwy 2

Ta opcja umożliwia migrację na żywo obciążeń z lokalnego środowiska VMware do chmury prywatnej do automatycznej synchronizacji na platformie Azure. W tej rozciągniętej sieci warstwy 2, podsieć z lokalnego programu będzie dostępna w chmurze prywatnej automatycznej synchronizacji. Po migracji nowe przypisanie adresu IP nie jest wymagane dla maszyn wirtualnych.

[Migrowanie obciążeń przy użyciu sieci rozciągniętych warstwy 2](migration-layer-2-vpn.md) zawiera opis sposobu użycia sieci VPN warstwy 2 do rozciągnięcia sieci warstwy 2 ze środowiska lokalnego do chmury prywatnej do automatycznej synchronizacji.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrowanie maszyn wirtualnych przy użyciu narzędzi kopii zapasowej i odzyskiwania po awarii

Migracja maszyn wirtualnych do chmury prywatnej można wykonać przy użyciu narzędzi do tworzenia kopii zapasowych i przywracania danych. Użyj chmury prywatnej do automatycznej synchronizacji jako docelowej do przywracania kopii zapasowych utworzonych za pomocą narzędzia innej firmy. Chmurę prywatną do automatycznej synchronizacji można także użyć jako celu do odzyskiwania po awarii przy użyciu oprogramowania VMware SRM lub narzędzia innej firmy.

Aby uzyskać więcej informacji na temat korzystania z tych narzędzi, zobacz następujące tematy:

* [Tworzenie kopii zapasowych maszyn wirtualnych obciążeń w chmurze prywatnej o automatycznej synchronizacji przy użyciu Veeam B & R](backup-workloads-veeam.md)
* [Skonfiguruj chmurę prywatną do automatycznej synchronizacji jako lokację odzyskiwania po awarii dla lokalnych obciążeń programu VMware](disaster-recovery-zerto.md)
