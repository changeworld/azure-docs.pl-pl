---
title: Rozwiązanie Azure VMware by CloudSimple — używanie chmury prywatnej jako lokacji awarii dla obciążeń lokalnych
description: W tym artykule opisano, jak skonfigurować chmurę CloudSimple Private Cloud jako lokację odzyskiwania po awarii dla lokalnych obciążeń VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083134"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Konfigurowanie chmury CloudSimple Private Cloud jako lokacji odzyskiwania po awarii dla lokalnych obciążeń VMware

CloudSimple Private Cloud można skonfigurować jako lokację odzyskiwania dla aplikacji lokalnych, aby zapewnić ciągłość działania w przypadku awarii. Rozwiązanie do odzyskiwania jest oparte na replikacji wirtualnej Zerto jako platformie replikacji i aranżacji. Infrastruktury krytycznej i aplikacji maszyn wirtualnych mogą być replikowane w sposób ciągły z lokalnego vCenter do chmury prywatnej. Usługi Private Cloud można używać do testowania trybu failover i zapewnienia dostępności aplikacji podczas awarii. Podobne podejście można stosować w celu skonfigurowania chmury prywatnej jako lokacji głównej, która jest chroniona przez lokację odzyskiwania w innej lokalizacji.

> [!NOTE]
> Zapoznaj się z dokumentem Zerto [Zagadnienia rozmiaru dla replikacji wirtualnej Zerto,](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) aby uzyskać wskazówki dotyczące rozmiaru środowiska odzyskiwania po awarii.

Rozwiązanie CloudSimple:

* Eliminuje konieczność konfigurowania centrum danych specjalnie do odzyskiwania po awarii (DR).
* Umożliwia korzystanie z lokalizacji platformy Azure, w których cloudsimple jest wdrażany dla odporności geograficznej na całym świecie.
* Umożliwia obniżenie kosztów wdrożenia i całkowity koszt posiadania odzyskiwania po awarii.

Rozwiązanie wymaga:

* Zainstaluj, skonfiguruj i zarządzaj Zerto w chmurze prywatnej.
* Zapewnij własne licencje dla Zerto, gdy private cloud jest witryną chronioną. Zerto działającego w witrynie CloudSimple można sparować z witryną lokalną w celu uzyskania licencji.

Na poniższej ilustracji przedstawiono architekturę rozwiązania Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

W poniższych sekcjach opisano sposób wdrażania rozwiązania odzyskiwania po awarii przy użyciu replikacji wirtualnej Zerto w chmurze prywatnej.

1. [Wymagania wstępne](#prerequisites)
2. [Opcjonalna konfiguracja w chmurze prywatnej CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Konfigurowanie ZVM i VRA w chmurze prywatnej CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Konfigurowanie wirtualnej grupy ochrony Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć replikację wirtualną Zerto ze środowiska lokalnego do chmury prywatnej, wykonaj następujące wymagania wstępne.

1. [Skonfiguruj połączenie sieci VPN między lokacją a chmurą cloudsimple private cloud.](set-up-vpn.md)
2. [Skonfiguruj wyszukiwanie DNS tak, aby składniki zarządzania private cloud były przekazywane do serwerów DNS w chmurze prywatnej](on-premises-dns-setup.md).  Aby włączyć przekazywanie wyszukiwania DNS, utwórz wpis strefy przekazywania na `*.cloudsimple.io` lokalnym serwerze DNS dla serwerów DNS CloudSimple.
3. Skonfiguruj wyszukiwanie DNS tak, aby lokalne składniki vCenter były przekazywane do lokalnych serwerów DNS.  Serwery DNS muszą być dostępne z chmury CloudSimple Private Cloud za pośrednictwem sieci VPN typu lokacja lokacja. Aby uzyskać pomoc, prześlij [wniosek o wsparcie,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)podając następujące informacje.  

    * Lokalna nazwa domeny DNS
    * Lokalne adresy IP serwera DNS

4. Zainstaluj serwer Windows w chmurze prywatnej. Serwer służy do instalowania Zerto Virtual Manager.
5. [Eskalacja uprawnień CloudSimple](escalate-private-cloud-privileges.md).
6. Utwórz nowego użytkownika w centrum vCenter private cloud z rolą administracyjną, która będzie używana jako konto usługi dla menedżera wirtualnego Zerto.

### <a name="optional-configuration-on-your-private-cloud"></a>Opcjonalna konfiguracja w chmurze prywatnej

1. Utwórz co najmniej jedną pulę zasobów w centrum vCenter private cloud, aby używać ich jako pul zasobów docelowych dla maszyn wirtualnych ze środowiska lokalnego.
2. Utwórz co najmniej jeden folder w centrum vCenter private cloud, aby używać ich jako folderów docelowych dla maszyn wirtualnych ze środowiska lokalnego.
3. Utwórz sieci VLAN dla sieci trybu failover i skonfiguruj reguły zapory. Otwórz [prośbę o pomoc do pomocy.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
4. Tworzenie rozproszonych grup portów dla sieci trybu failover i sieci testowej do testowania pracy awaryjnej maszyn wirtualnych.
5. Zainstaluj [serwery DHCP i DNS](dns-dhcp-setup.md) lub użyj kontrolera domeny usługi Active Directory w środowisku private cloud.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Konfigurowanie ZVM i VRA w chmurze prywatnej

1. Zainstaluj Zerto Virtual Manager (ZVM) na serwerze Windows w chmurze prywatnej.
2. Zaloguj się do ZVM przy użyciu konta usługi utworzonego w poprzednich krokach.
3. Skonfiguruj licencjonowanie dla Zerto Virtual Manager.
4. Zainstaluj urządzenie Verto Virtual Replication Appliance (VRA) na hostach ESXi w chmurze prywatnej.
5. Sparuj złącze Private Cloud ZVM z lokalnym ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Konfigurowanie wirtualnej grupy ochrony Zerto

1. Utwórz nową wirtualną grupę ochrony (VPG) i określ priorytet dla VPG.
2. Wybierz maszyny wirtualne, które wymagają ochrony dla ciągłości biznesowej i dostosować kolejność rozruchu w razie potrzeby.
3. Wybierz lokację odzyskiwania jako chmurę prywatną i domyślny serwer odzyskiwania jako klaster private cloud lub utworzoną grupę zasobów. Wybierz **vsanDatastore** dla magazynu danych odzyskiwania w chmurze prywatnej.

    ![Vpg](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Można dostosować opcję hosta dla poszczególnych maszyn wirtualnych w ramach opcji Ustawienia maszyny wirtualnej.

4. W razie potrzeby dostosuj opcje pamięci masowej.
5. Określ sieci odzyskiwania używane dla sieci trybu failover i sieci testowej trybu failover jako rozproszone grupy portów utworzone wcześniej i dostosuj skrypty odzyskiwania zgodnie z wymaganiami.
6. W razie potrzeby dostosuj ustawienia sieciowe dla poszczególnych maszyn wirtualnych i utwórz vpg.
7. Przetestuj przewija po awarii po zakończeniu replikacji.

## <a name="reference"></a>Tematy pomocy

[Dokumentacja Zerto](https://www.zerto.com/myzerto/technical-documentation/)
