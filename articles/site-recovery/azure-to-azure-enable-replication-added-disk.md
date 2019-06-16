---
title: Włącz replikację dla dysku, który został dodany do maszyny Wirtualnej platformy Azure replikowane za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania replikacji dysk, który został dodany do maszyny Wirtualnej platformy Azure, który jest włączony dla odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928065"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Włącz replikację dla dysku, który został dodany do maszyny Wirtualnej platformy Azure


W tym artykule opisano sposób włączania replikacji dysków z danymi, które są dodawane do maszyny Wirtualnej platformy Azure, która jest już włączona dla odzyskiwania po awarii w innym regionie platformy Azure, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

Włączanie replikacji dla dysku, dodane do maszyny Wirtualnej jest obsługiwana dla maszyn wirtualnych platformy Azure z dyskami zarządzanymi.

Po dodaniu nowego dysku do maszyny Wirtualnej platformy Azure, który jest replikowany do innego regionu platformy Azure, są następujące operacje:

-   Kondycja replikacji maszyny wirtualnej, wyświetla ostrzeżenie i notatki w portalu informuje, że jeden lub więcej dysków są dostępne do ochrony.
-   Jeśli włączysz ochronę dodane dyski, ostrzeżenie znikną po początkowej replikacji dysku.
-   Jeśli wybierzesz nie włączyć replikację dla dysku, możesz wybrać, aby zignorować to ostrzeżenie.

![Nowy dysk, dodane](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule założono, że już skonfigurowano odzyskiwanie po awarii dla maszyny Wirtualnej, do którego dodajesz dysku. Jeśli jeszcze tego nie, postępuj zgodnie z [samouczek odzyskiwania po awarii Azure – Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Włączanie replikacji dodanego dysku 

Aby włączyć replikację dla dodany dysk, wykonaj następujące czynności:

1. W magazynie > **zreplikowane elementy**, kliknij maszynę Wirtualną, do którego został dodany dysk.
2. Kliknij przycisk **dysków**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (dyski te mają **nie są chronione** stanu).
3.  W **szczegóły dysku**, kliknij przycisk **włączyć replikację**.

    ![Włączanie replikacji na potrzeby dodany dysk](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po zakończeniu replikacji początkowej, uruchamia zadanie włączania replikacji ostrzeżenie kondycji replikacji dla problemu z dyskiem zostaną usunięte.



# <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowy tryb failover.
