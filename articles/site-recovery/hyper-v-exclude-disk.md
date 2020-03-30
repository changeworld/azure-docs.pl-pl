---
title: Wykluczanie dysków maszyn wirtualnych funkcji Hyper V z odzyskiwania po awarii na platformę Azure za pomocą usługi Azure Site Recovery
description: Jak wykluczyć dyski maszyn wirtualnych funkcji Hyper-V z replikacji na platformę Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498139"
---
# <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych z programem Hyper V na platformie Azure. Można wykluczyć dyski z replikacji z kilku powodów:

- Upewnij się, że nieistotne dane ubijane na wykluczonym dysku nie są replikowane.
- Zoptymalizuj przepustowość używanej replikacji lub zasoby po stronie docelowej, wykluczając dyski, których nie trzeba replikować.
- Oszczędzaj zasoby magazynu i sieci, nie replikując danych, które nie są potrzebne.

Przed wykluczeniem dysków z replikacji:

- [Dowiedz się więcej](exclude-disks-replication.md) o wykluczaniu dysków.
- Przejrzyj [typowe scenariusze wykluczeń](exclude-disks-replication.md#typical-scenarios) i [przykłady,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) które pokazują, jak wykluczenie dysku wpływa na replikację, tryb failover i powrót po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem należy zwrócić uwagę na następujące kwestie:

- **Replikacja:** Domyślnie wszystkie dyski na komputerze są replikowane.
- **Typ dysku:**
    - Dyski podstawowe można wykluczyć z replikacji.
    - Nie możesz wykluczać dysków systemu operacyjnego.
    - Nie zalecamy wykluczania dysków dynamicznych. Usługa Site Recovery nie może zidentyfikować, który dysk VHD jest podstawowy lub dynamiczny w maszynie wirtualnej gościa.  Jeśli nie wykluczysz wszystkich zależnych dysków woluminów dynamicznych, chroniony dysk dynamiczny stanie się dyskiem awaryjnym na nieudanej maszynie wirtualnej, a dane na tym dysku nie są dostępne.
- **Dodawanie/usuwanie/wykluczanie dysków:** Po włączeniu replikacji nie można dodawać/usuwać/wykluczać dysków do replikacji. Jeśli chcesz dodać/usunąć lub wykluczyć dysk, musisz wyłączyć ochronę maszyny Wirtualnej, a następnie włączyć ją ponownie.
- **Praca awaryjna:** Po przełączeniu w tryb failover, jeśli aplikacje awaryjne muszą wykluczyć dyski, aby działać, należy utworzyć te dyski ręcznie. Alternatywnie można zintegrować automatyzacji platformy Azure do planu odzyskiwania, aby utworzyć dysk podczas pracy awaryjnej komputera.
- **Powrót po awarii:** Po awarii po powrocie do lokacji lokalnej po przełączeniu w tryb failover dyski utworzone ręcznie na platformie Azure nie są po awarii. Na przykład jeśli w pracy awaryjnej na trzech dyskach i utworzyć dwa dyski bezpośrednio na maszynie Wirtualnej platformy Azure, tylko trzy dyski, które zostały przejęte awaryjnie są następnie po awarii z powrotem. Nie można dołączyć dysków, które zostały utworzone ręcznie w powiększenie zwrotnym lub w odwrotnej replikacji maszyn wirtualnych.

## <a name="exclude-disks"></a>Wykluczanie dysków

1. Aby wykluczyć dyski po [włączeniu replikacji](site-recovery-hyper-v-site-to-azure.md) maszyny wirtualnej funkcji Hyper-V, po wybraniu maszyn wirtualnych, które chcesz replikować, na stronie Włącz**właściwości** >  **replikacji** > **Konfiguruj** przejrzyj kolumnę **Dyski do replikacji.** Domyślnie wszystkie dyski są wybierane do replikacji.
2. Jeśli nie chcesz replikować określonego dysku, w **folderze Dyski, aby replikować wyczyść** zaznaczenie dla wszystkich dysków, które chcesz wykluczyć. 

    ![Wykluczanie dysków z replikacji](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.
