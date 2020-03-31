---
title: Włączanie replikacji dla dodanego dysku maszyny Wirtualnej platformy Azure w usłudze Azure Site Recovery
description: W tym artykule opisano sposób włączania replikacji dysku dodanego do maszyny Wirtualnej platformy Azure, która jest włączona do odzyskiwania po awarii za pomocą usługi Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973788"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Włączanie replikacji dysku dodanego do maszyny Wirtualnej platformy Azure


W tym artykule opisano sposób włączania replikacji dysków danych, które są dodawane do maszyny Wirtualnej platformy Azure, która jest już włączona do odzyskiwania po awarii do innego regionu platformy Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

Włączanie replikacji dysku dodanego do maszyny wirtualnej jest obsługiwane dla maszyn wirtualnych platformy Azure z dyskami zarządzanymi.

Po dodaniu nowego dysku do maszyny Wirtualnej platformy Azure, która replikuje do innego regionu platformy Azure, występuje następujące czynności:

-   Kondycja replikacji maszyny Wirtualnej wyświetla ostrzeżenie, a notatka w portalu informuje, że jeden lub więcej dysków jest dostępnych do ochrony.
-   Jeśli włączysz ochronę dodanych dysków, ostrzeżenie zniknie po początkowej replikacji dysku.
-   Jeśli nie chcesz włączać replikacji dysku, możesz wybrać, aby odrzucić ostrzeżenie.

![Dodano nowy dysk](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że zostało już skonfigurowane odzyskiwanie po awarii dla maszyny Wirtualnej, do której dodajesz dysk. Jeśli nie, wykonaj [samouczek odzyskiwania po awarii platformy Azure na platformie Azure.](azure-to-azure-tutorial-enable-replication.md)

## <a name="enable-replication-for-an-added-disk"></a>Włączanie replikacji dodanego dysku

Aby włączyć replikację dodanego dysku, wykonaj następujące czynności:

1. W przechowalni > **elementy replikowane**kliknij maszynę wirtualną, do której dodano dysk.
2. Kliknij pozycję **Dyski**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (dyski te mają stan **Niechroniony).**
3.  W **opcji Szczegóły dysku**kliknij pozycję Włącz **replikację**.

    ![Włączanie replikacji dla dodanego dysku](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po uruchomieniu zadania replikacji włączania i zakończeniu replikacji początkowej ostrzeżenie o kondycji replikacji dla problemu z dyskiem zostanie usunięte.



## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu pracy awaryjnej testu.
