---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Używanie chmury prywatnej do automatycznej obsługi obciążeń lokalnych
description: Opisuje sposób konfigurowania chmury prywatnej automatycznej synchronizacji jako lokacji odzyskiwania po awarii dla lokalnych obciążeń programu VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5ee43af97e79f1e835787d61bd79cfb256ef445
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083134"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Skonfiguruj chmurę prywatną do automatycznej synchronizacji jako lokację odzyskiwania po awarii dla lokalnych obciążeń programu VMware

Chmurę prywatną do automatycznej synchronizacji można skonfigurować jako lokację odzyskiwania dla aplikacji lokalnych, aby zapewnić ciągłość działania w przypadku awarii. Rozwiązanie odzyskiwania bazuje na replikacji wirtualnej Zerto jako platforma replikacji i aranżacji. Krytyczne infrastruktury i maszyny wirtualne aplikacji mogą być replikowane w sposób ciągły z lokalnego programu vCenter do swojej chmury prywatnej. Do testowania pracy w trybie failover można użyć chmury prywatnej do automatycznej synchronizacji oraz zapewnić dostępność aplikacji podczas awarii. Podobne podejście można wykonać, aby skonfigurować chmurę prywatną do automatycznej synchronizacji jako lokację główną, która jest chroniona przez lokację odzyskiwania w innej lokalizacji.

> [!NOTE]
> Zapoznaj się z [zagadnieniami dotyczącymi ustalania rozmiarów dokumentu Zerto w przypadku replikacji wirtualnej Zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) w celu uzyskania wskazówek dotyczących określania rozmiarów środowiska odzyskiwania po awarii.

Rozwiązanie do automatycznej synchronizacji:

* Eliminuje konieczność skonfigurowania centrum danych przeznaczonego do odzyskiwania po awarii (DR).
* Umożliwia korzystanie z lokalizacji platformy Azure, w której jest wdrażana automatyczna elastyczność geograficzna.
* Zapewnia możliwość zmniejszenia kosztów wdrożenia i łącznego kosztu posiadania odzyskiwania po awarii.

Rozwiązanie wymaga:

* Instaluj i Konfiguruj Zerto w chmurze prywatnej automatycznej synchronizacji, a następnie Zarządzaj nią.
* Podaj własne licencje na Zerto, gdy Chmura prywatna jest chroniona. Można sparować Zerto uruchomione w witrynie automatycznej synchronizacji z lokacją lokalną w celu licencjonowania.

Na poniższej ilustracji przedstawiono architekturę rozwiązania Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

W poniższych sekcjach opisano, jak wdrożyć rozwiązanie DR przy użyciu replikacji wirtualnej Zerto w chmurze prywatnej automatycznej synchronizacji.

1. [Wymagania wstępne](#prerequisites)
2. [Opcjonalna konfiguracja w chmurze prywatnej automatycznej synchronizacji](#optional-configuration-on-your-avs-private-cloud)
3. [Skonfiguruj ZVM i VRA w chmurze prywatnej automatycznej synchronizacji](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Konfigurowanie wirtualnej grupy ochrony Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć replikację wirtualną Zerto z poziomu środowiska lokalnego do chmury prywatnej automatycznej synchronizacji, należy spełnić następujące wymagania wstępne.

1. [Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między siecią lokalną a chmurą prywatną do automatycznej synchronizacji](set-up-vpn.md).
2. [Skonfiguruj wyszukiwanie DNS w taki sposób, aby składniki zarządzania chmurą prywatną do automatycznej synchronizacji zostały przekazane do wersji załączonej do serwerów DNS chmury prywatnej](on-premises-dns-setup.md). Aby włączyć przekazywanie wyszukiwania DNS, Utwórz wpis strefy przekazywania na lokalnym serwerze DNS, aby uzyskać `*.cloudsimple.io` na potrzeby automatycznej synchronizacji serwerów DNS.
3. Skonfiguruj wyszukiwanie DNS w taki sposób, że lokalne składniki programu vCenter są przekazywane do lokalnych serwerów DNS. Serwery DNS muszą być dostępne z chmury prywatnej automatycznej synchronizacji przy użyciu sieci VPN typu lokacja-lokacja. Aby uzyskać pomoc, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), dostarczając poniższe informacje. 

    * Lokalna nazwa domeny DNS
    * Adresy IP lokalnego serwera DNS

4. Zainstaluj system Windows Server w chmurze prywatnej automatycznej. Serwer służy do instalowania Zerto Virtual Manager.
5. [Eskalacja uprawnień do automatycznej synchronizacji](escalate-private-cloud-privileges.md).
6. Utwórz nowego użytkownika w ramach automatycznej synchronizacji w chmurze prywatnej programu vCenter z rolą administracyjną, która będzie używana jako konto usługi dla Zerto Virtual Manager.

### <a name="optional-configuration-on-your-avs-private-cloud"></a>Opcjonalna konfiguracja w chmurze prywatnej automatycznej synchronizacji

1. Utwórz co najmniej jedną pulę zasobów w chmurze prywatnej chmury programu vCenter, która będzie używana jako docelowe pule zasobów dla maszyn wirtualnych w środowisku lokalnym.
2. Utwórz co najmniej jeden folder w chmurze prywatnej chmury programu vCenter, który będzie używany jako foldery docelowe dla maszyn wirtualnych w środowisku lokalnym.
3. Utwórz sieć VLAN dla sieci trybu failover i skonfiguruj reguły zapory. Otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) , aby uzyskać pomoc.
4. Tworzenie rozproszonych grup portów dla sieci trybu failover i sieci testowej w celu testowania pracy w trybie failover maszyn wirtualnych.
5. Zainstaluj [serwery DHCP i DNS](dns-dhcp-setup.md) lub użyj Active Directory kontroler domeny w środowisku chmury prywatnej do automatycznej synchronizacji.

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>Skonfiguruj ZVM i VRA w chmurze prywatnej automatycznej synchronizacji

1. Zainstaluj program Zerto Virtual Manager (ZVM) na serwerze z systemem Windows w chmurze prywatnej "Automatyczna synchronizacja".
2. Zaloguj się do ZVM przy użyciu konta usługi utworzonego w poprzednich krokach.
3. Konfigurowanie licencjonowania dla Zerto Virtual Manager.
4. Zainstaluj urządzenie Zerto Virtual Replication (VRA) na hostach ESXi w chmurze prywatnej automatycznej synchronizacji.
5. Poparz swoją ZVMą chmurę prywatną z lokalną usługą ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Konfigurowanie wirtualnej grupy ochrony Zerto

1. Utwórz nową wirtualną grupę ochrony (VPG) i określ priorytet dla VPG.
2. Wybierz Maszyny wirtualne wymagające ochrony ciągłości działania i w razie potrzeby dostosuj kolejność rozruchu.
3. Wybierz lokację odzyskiwania jako chmurę prywatną do automatycznej synchronizacji i domyślny serwer odzyskiwania jako klaster chmury prywatnej do automatycznej synchronizacji lub utworzoną grupę zasobów. Wybierz pozycję **vsanDatastore** dla magazynu danych odzyskiwania w chmurze prywatnej automatycznej synchronizacji.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Można dostosować opcję hosta dla poszczególnych maszyn wirtualnych w opcji ustawienia maszyny wirtualnej.

4. Dostosuj opcje magazynu zgodnie z potrzebami.
5. Określ sieci odzyskiwania, które mają być używane dla sieci trybu failover i sieci testowej trybu failover jako rozproszone grupy portów utworzone wcześniej, i Dostosuj skrypty odzyskiwania zgodnie z wymaganiami.
6. W razie potrzeby dostosuj ustawienia sieci dla poszczególnych maszyn wirtualnych i Utwórz VPG.
7. Test pracy w trybie failover po zakończeniu replikacji.

## <a name="reference"></a>Dokumentacja

[Dokumentacja Zerto](https://www.zerto.com/myzerto/technical-documentation/)
