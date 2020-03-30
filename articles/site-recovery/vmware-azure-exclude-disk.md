---
title: Wykluczanie dysków maszyn wirtualnych VMware z odzyskiwania po awarii na platformę Azure za pomocą usługi Azure Site Recovery
description: Jak wykluczyć dyski VMware VM Z replikacji na platformę Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495357"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Wykluczanie dysków z replikacji maszyn wirtualnych VMware na platformę Azure

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych vmware na platformie Azure w celu odzyskiwania po awarii. Można wykluczyć dyski z replikacji z kilku powodów:

- Upewnij się, że nieistotne dane ubijane na wykluczonym dysku nie są replikowane.
- Zoptymalizuj przepustowość używanej replikacji lub zasoby po stronie docelowej, wykluczając dyski, których nie trzeba replikować.
- Oszczędzaj zasoby magazynu i sieci, nie replikując danych, które nie są potrzebne.

Przed wykluczeniem dysków z replikacji:

- [Dowiedz się więcej](exclude-disks-replication.md) o wykluczaniu dysków.
- Przejrzyj [typowe scenariusze wykluczeń](exclude-disks-replication.md#typical-scenarios) i [przykłady,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) które pokazują, jak wykluczenie dysku wpływa na replikację, tryb failover i powrót po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

 Przed rozpoczęciem należy zwrócić uwagę na następujące kwestie:

- **Replikacja:** Domyślnie wszystkie dyski na komputerze są replikowane.
- **Typ dysku:** Z replikacji można wykluczyć tylko dyski podstawowe. Nie możesz wykluczać dysków systemu operacyjnego ani dysków dynamicznych.
- **Usługa mobilności:** Aby wykluczyć dysk z replikacji, należy ręcznie zainstalować usługę mobilności na komputerze przed włączeniem replikacji. Nie można użyć instalacji wypychanej, ponieważ ta metoda instaluje usługę mobilności na maszynie Wirtualnej tylko po włączeniu replikacji.  
- **Dodawanie/usuwanie/wykluczanie dysków:** Po włączeniu replikacji nie można dodawać/usuwać/wykluczać dysków do replikacji. Jeśli chcesz dodać/usunąć lub wykluczyć dyski, musisz wyłączyć ochronę komputera, a następnie włączyć ją ponownie.
- **Praca awaryjna:** Po przełączeniu w tryb failover, jeśli aplikacje awaryjne potrzebują wykluczonych dysków, aby działać, należy utworzyć te dyski ręcznie. Alternatywnie można zintegrować automatyzacji platformy Azure do planu odzyskiwania, aby utworzyć dysk podczas pracy awaryjnej komputera.
- **Powrót po awarii systemu Windows:** Po awarii po awarii do lokacji lokalnej po przełączeniu w tryb failover dyski systemu Windows, które można utworzyć ręcznie na platformie Azure, nie są po awarii. Na przykład jeśli po awarii trzy dyski i utworzyć dwa dyski bezpośrednio na maszynach wirtualnych platformy Azure, tylko trzy dyski, które zostały przejęte awaryjnie zostaną po awarii z powrotem.
- **Failback-Linux:** W przypadku powrotu po awarii komputerów z systemem Linux dyski utworzone ręcznie na platformie Azure są przywracane po awarii. Na przykład jeśli po awarii trzy dyski i utworzyć dwa dyski bezpośrednio na maszynach wirtualnych platformy Azure, wszystkie pięć zostanie po awarii z powrotem. Nie można wykluczyć dysków, które zostały utworzone ręcznie w powiększeniu po awarii lub w reprotection maszyn wirtualnych.



## <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

1. Po [włączeniu replikacji](site-recovery-hyper-v-site-to-azure.md) maszyny Wirtualnej VMware po wybraniu maszyn wirtualnych, które mają być replikowane, na stronie Włącz**właściwości** >  **replikacji** > **Konfiguruj** przejrzyj kolumnę **Dyski do replikacji.** Domyślnie wszystkie dyski są wybierane do replikacji.
2. Jeśli nie chcesz replikować określonego dysku, w **folderze Dyski, aby replikować wyczyść** zaznaczenie dla wszystkich dysków, które chcesz wykluczyć. 

    ![Wykluczanie dysków z replikacji](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.
