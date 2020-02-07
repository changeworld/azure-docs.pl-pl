---
title: Często zadawane pytania dotyczące migracji Azure Migrate serwera
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące migracji serwera Azure Migrate
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067385"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migracja serwera Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate: migracji serwera. Jeśli po przeczytaniu tego artykułu masz dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Jeśli masz inne pytania, zapoznaj się z następującymi artykułami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate.
- [Pytania](common-questions-appliance.md) dotyczące urządzenia Azure Migrateowego.
- [Pytania](common-questions-discovery-assessment.md) dotyczące funkcji odnajdywania, oceny i wizualizacji zależności.


## <a name="how-does-agentless-vmware-replication-work"></a>Jak działa replikacja VMware bez agenta?

Metoda replikacji bez agenta dla oprogramowania VMware używa migawek VMware i śledzenia zmian bloków programu VMware (CBT). Cykl replikacji początkowej jest planowany, gdy użytkownik rozpocznie replikację. W cyklu replikacji początkowej tworzona jest migawka maszyny wirtualnej, a ta migawka jest używana do replikowania maszyn wirtualnych VMDK (disks). Po zakończeniu cyklu replikacji początkowej cykle replikacji różnicowej są planowane okresowo. W cyklu replikacji różnicowej jest wykonywana migawka, a bloki danych, które uległy zmianie od czasu ostatniego cyklu replikacji, są replikowane. Śledzenie blokowe zmiany programu VMware służy do określania bloków, które uległy zmianie od ostatniego cyklu.
Częstotliwość okresowych cykli replikacji jest automatycznie zarządzana przez usługę w zależności od tego, jak wiele innych maszyn wirtualnych/dysków jest jednocześnie replikowana z tego samego magazynu danych, a w idealnych warunkach będzie ostatecznie zbieżna z 1 cyklem na maszynę wirtualną.

Podczas migrowania cykl replikacji na żądanie jest zaplanowany do przechwycenia przez maszynę wirtualną wszelkich pozostałych danych. W ramach migracji można wyłączyć maszynę wirtualną w celu zapewnienia zerowej utraty danych i spójności aplikacji.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Dlaczego opcja ponownej synchronizacji nie jest ujawniona w stosie bez agentów?

W przypadku stosu bezagentowego w każdym cyklu różnicowym przesyłamy różnice między bieżącą migawką a poprzednią migawką, którą zrobiono. Ponieważ ta funkcja jest zawsze różnicą między migawkami, daje to zaletę składania danych (tj. Jeśli określony sektor ma postać "n" razy między migawkami, wystarczy przesłać Ostatni zapis, ponieważ interesuje się tylko ostatnią synchronizacją). Różni się to od stosu opartego na agentach, w którym śledzimy każdy zapis i zastosuje je. Oznacza to, że każdy cykl różnicowy jest ponowną synchronizacją. W związku z tym nie jest dostępna opcja ponownej synchronizacji. 

Jeśli kiedykolwiek dysk nie jest zsynchronizowany z powodu błędu, zostanie on rozwiązany w następnym cyklu. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Jaki jest wpływ współczynnika zmian, jeśli używam replikacji bez wykorzystania agentów?

Ze względu na to, że stos jest zależny od danych, które są składane, więcej niż szybkość zmiany, wzorzec zmian to zagadnienia w tym stosie. Wzorzec, w którym plik jest zapisywany ponownie i nie ma znacznie wpływu. Jednak wzorzec, w którym każdy inny sektor jest zapisywana, spowoduje wysoki poziom zmian w następnym cyklu. Ponieważ minimalizujemy ilość przesyłanych danych, zezwolimy, aby dane były składane możliwie jak najwięcej przed zaplanowaniem następnego cyklu.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak często zaplanowano cykl replikacji?

Formułą do zaplanowania następnego cyklu replikacji jest: (poprzedni czas cyklu/2) lub 1 godzina, w zależności od tego, gdzie jest wyższy. Na przykład jeśli maszyna wirtualna zajęła cztery godziny dla cyklu różnicowego, zaplanujemy kolejny cykl w 2 godziny, a nie w ciągu następnej godziny. Jest to inne miejsce, gdy cykl jest natychmiast po środowisku IR, w którym natychmiast zaplanujemy Pierwszy cykl Delta.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Jaki jest wpływ na wydajność vCenter Server lub hosta ESXi podczas korzystania z replikacji bez wykorzystania agentów?

Ponieważ replikacja bezagentowa korzysta z migawek, użycie będzie wymagało użycia operacji we/wy na sekundę, a klienci będą potrzebować pewnej pojemności dla magazynu. Nie zalecamy korzystania z tego stosu w ograniczonym środowisku magazynu/operacji we/wy.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Czy stos migracji bez agenta obsługuje migrację maszyn wirtualnych UEFI do maszyn wirtualnych Azure Gen 2?

Nie, aby przeprowadzić migrację maszyn wirtualnych do maszyn wirtualnych generacji 2 na platformie Azure, musisz użyć Azure Site Recovery. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Czy mogę przypiąć maszyny wirtualne do Strefy dostępności platformy Azure podczas migracji?

Nie, nie ma pomocy technicznej dla Strefy dostępności platformy Azure.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Który protokół transportu jest używany przez Azure Migrate podczas replikacji?

Azure Migrate używa protokołu urządzenia bloku sieciowego (NBD) z szyfrowaniem SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Co to jest minimalna wersja vCenter Server wymagana do migracji?

Musisz mieć co najmniej vCenter Server 5,5 i VMware vSphere Host ESXi w wersji 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Czy klienci mogą migrować maszyny wirtualne do dysków niezarządzanych?

Nie. Azure Migrate obsługuje migrację tylko do dysków zarządzanych (standardowy dysk twardy, dysk SSD Premium).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Ile maszyn wirtualnych może replikować jednocześnie przy użyciu stosu VMware bez agentów?

Obecnie klienci mogą migrować 100 maszyn wirtualnych na vCenter Server jednocześnie. Można to zrobić w partiach 10 maszyn wirtualnych.




