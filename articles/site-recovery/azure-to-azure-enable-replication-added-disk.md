---
title: Włącz replikację dla dodanego dysku maszyny wirtualnej platformy Azure w Azure Site Recovery
description: W tym artykule opisano sposób włączania replikacji dysku dodanego do maszyny wirtualnej platformy Azure, która umożliwia odzyskiwanie po awarii za pomocą Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: fd020755ec579b2553bca42db5d050bd442bf6fc
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942301"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Włącz replikację dla dysku dodanego do maszyny wirtualnej platformy Azure


W tym artykule opisano sposób włączania replikacji dysków z danymi, które są dodawane do maszyny wirtualnej platformy Azure, która jest już włączona na potrzeby odzyskiwania po awarii w innym regionie świadczenia usługi Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Włączenie replikacji dysku dodawanego do maszyny wirtualnej jest obsługiwane dla maszyn wirtualnych platformy Azure z dyskami zarządzanymi.

Po dodaniu nowego dysku do maszyny wirtualnej platformy Azure, która jest replikowana do innego regionu platformy Azure, występują następujące sytuacje:

-   Kondycja replikacji maszyny wirtualnej zawiera ostrzeżenie i Uwaga w portalu informuje o tym, że co najmniej jeden dysk jest dostępny do ochrony.
-   Jeśli włączysz ochronę dla dodanych dysków, ostrzeżenie zniknie po początkowej replikacji dysku.
-   Jeśli nie zdecydujesz się na włączenie replikacji dla dysku, możesz wybrać opcję odrzucania ostrzeżenia.

![Dodano nowy dysk](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że już skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej, do której jest dodawany dysk. Jeśli nie, postępuj zgodnie z [samouczkiem odzyskiwania po awarii z platformy Azure do platformy Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Włączanie replikacji dodanego dysku 

Aby włączyć replikację dla dodanego dysku, wykonaj następujące czynności:

1. W magazynie > **zreplikowane elementy**kliknij maszynę wirtualną, do której dodano dysk.
2. Kliknij pozycję **dyski**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (te dyski mają stan **niechroniony** ).
3.  W obszarze **szczegóły dysku**kliknij pozycję **Włącz replikację**.

    ![Włącz replikację dla dodanego dysku](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po uruchomieniu zadania włączania replikacji i zakończeniu początkowej replikacji zostanie usunięte ostrzeżenie o kondycji replikacji dla problemu z dyskiem.



## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowej pracy w trybie failover.
