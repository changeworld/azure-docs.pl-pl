---
title: Często zadawane pytania dotyczące migracji Azure Migrate serwera
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące migrowania maszyn przy użyciu migracji Azure Migrate serwera
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425837"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migracja serwera Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate: serwerowego narzędzia migracji. Jeśli masz więcej pytań po zapoznaniu się z tym artykułem, zapoznaj się z następującymi artykułami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate.
- [Pytania](common-questions-appliance.md) dotyczące urządzenia Azure Migrateowego.
- [Pytania](common-questions-discovery-assessment.md) dotyczące funkcji odnajdywania, oceny i wizualizacji zależności.
- Publikuj pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Jak działa replikacja VMware bez agenta?

Metoda replikacji bez agenta dla oprogramowania VMware używa migawek VMware i śledzenia zmian bloków programu VMware (CBT).

1. Po uruchomieniu replikacji jest zaplanowana początkowa cykl replikacji. W cyklu początkowym tworzona jest migawka maszyny wirtualnej. Ta migawka służy do replikowania maszyn wirtualnych VMDK (disks). 
2. Po zakończeniu cyklu replikacji początkowej cykle replikacji różnicowej są planowane okresowo.
    - Podczas replikacji różnicowej jest wykonywana migawka, a bloki danych, które uległy zmianie od czasu ostatniego cyklu replikacji, są replikowane.
    - Program VMware CBT służy do określania bloków, które uległy zmianie od ostatniego cyklu.
    - Częstotliwość okresowych cykli replikacji jest automatycznie zarządzana przez Azure Migrate, w zależności od tego, jak wiele innych maszyn wirtualnych/dysków jest jednocześnie replikowana z tego samego magazynu danych. W idealnych warunkach replikacja ostatecznie wywoła jeden cykl na godzinę dla każdej maszyny wirtualnej.

Podczas migrowania cykl replikacji na żądanie jest planowany dla maszyny, aby przechwycić wszystkie pozostałe dane. Możesz zdecydować się na wyłączenie maszyny podczas migracji, aby zapewnić zero utraty danych i spójność aplikacji.

## <a name="why-isnt-resynchronization-exposed"></a>Dlaczego nie jest dostępna ponowna synchronizacja?

Podczas migracji bez wykorzystania agentów w każdym cyklu Delta różnica między bieżącą migawką a wcześniej zrobioną migawką jest zapisywana. Ponieważ zawsze jest to różnica między migawkami, składania danych w. Tak więc, jeśli określony sektor jest zapisywana N razy między migawkami, należy przesłać tylko ostatni zapis, ponieważ interesuje tylko ostatnią synchronizację. Różni się to od replikacji opartej na agentach, gdzie śledzimy i stosujemy każdy zapis. Oznacza to, że każdy cykl różnicowy jest ponowną synchronizacją. W związku z tym nie jest dostępna opcja ponownej synchronizacji. Jeśli kiedykolwiek dyski nie zostaną zsynchronizowane ze względu na awarię, zostanie ona rozwiązana w następnym cyklu. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Jak współczynnik zmian wpływa na replikację bezagentową?

Ze względu na to, że dane dotyczące replikacji bez wykorzystania agentów, wzorzec zmian jest ważniejszy niż współczynnik zmian. Gdy plik zostanie ponownie zapisany i ponownie, szybkość nie ma znacznie wpływu. Jednak wzorzec, w którym każdy inny sektor jest zapisywana, powoduje duże zmiany w następnym cyklu. Ponieważ minimalizujemy ilość przesyłanych danych, zezwolimy, aby dane były składane możliwie jak najwięcej przed zaplanowaniem następnego cyklu.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak często zaplanowano cykl replikacji?

Formuła określająca harmonogram następnego cyklu replikacji: (poprzedni czas cyklu/2) lub 1 godzina, w zależności od tego, która wartość jest większa.

Na przykład jeśli maszyna wirtualna zajmuje cztery godziny cyklu różnicowego, kolejny cykl jest zaplanowany w ciągu dwóch godzin, a nie w ciągu następnej godziny. Jest to różne od razu po replikacji początkowej, gdzie pierwszy cykl różnicowy jest zaplanowany od razu.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>W jaki sposób replikacja bez agentów ma wpływ na serwery VMware?

Na hostach vCenter Server/ESXi występuje pewien wpływ na wydajność. Ponieważ replikacja bezagentowa korzysta z migawek, zużywa ona operacje we/wy na magazyn, a wymagana jest pewna przepustowość magazynu IOPS. Nie zalecamy korzystania z replikacji bez wykorzystania agentów, jeśli istnieją ograniczenia dotyczące magazynu/operacji we/wy na sekundę w danym środowisku.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Czy można przeprowadzić migrację maszyn wirtualnych UEFI do usługi Azure Gen 2 bez wykorzystania agentów?

Nie. Użyj Azure Site Recovery do migrowania tych maszyn wirtualnych do maszyn wirtualnych platformy Azure w generacji 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Czy mogę przypiąć maszyny wirtualne do Strefy dostępności platformy Azure podczas migracji?

Nie, Strefy dostępności platformy Azure nie są obsługiwane.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Który protokół transportu jest używany przez Azure Migrate podczas replikacji?

Azure Migrate używa protokołu urządzenia bloku sieciowego (w przypadku szyfrowania SSL).

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Co to jest minimalna wersja vCenter Server wymagana do migracji?

Musisz mieć co najmniej vCenter Server 5,5 i VMware vSphere Host ESXi w wersji 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Czy klienci mogą migrować maszyny wirtualne do dysków niezarządzanych?

Nie. Azure Migrate obsługuje migrację tylko do dysków zarządzanych (standardowy dysk twardy, dysk SSD Premium).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Ile maszyn wirtualnych można replikować razem z migracją bez agenta?

Obecnie można migrować maszyny wirtualne 100 na vCenter Server jednocześnie. Migrowanie w partiach 10 maszyn wirtualnych.
 



