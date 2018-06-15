---
title: Przygotowanie środowiska docelowego dla replikacji maszyn wirtualnych VMware do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania urządzenie docelowe środowiska platformy Azure dla replikacji maszyny Wirtualnej VMware do platformy Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812635"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Przygotowanie środowiska docelowego dla replikacji maszyn wirtualnych VMware do platformy Azure

W tym artykule opisano sposób przygotowania urządzenie docelowe środowiska platformy Azure, aby rozpocząć replikację maszyn wirtualnych VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Założono artykułu:
- Utworzono magazyn usług odzyskiwania, aby chronić maszyny wirtualne VMware. Można utworzyć magazyn usług odzyskiwania z [portalu Azure](http://portal.azure.com "portalu Azure").
- Masz [konfiguracji środowiska lokalnego](vmware-azure-set-up-source.md) do replikowania maszyn wirtualnych VMware do platformy Azure.

## <a name="prepare-target"></a>Przygotowanie docelowego

Po zakończeniu **cel ochrony krok 1** i **krok 2: przygotowanie źródła**, zostają przeniesieni do **krok 3: docelowy**

![Przygotowanie docelowego](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** z menu rozwijanego Wybierz subskrypcję, którą chcesz zreplikować maszyny wirtualnej.
2. **Model wdrażania:** wybierz model wdrażania (Classic lub Resource Manager)

Oparte na modelu wdrażania wybranego, uruchomieniu aby upewnić się, że masz co najmniej jedno konto magazynu zgodny i siecią wirtualną w docelowej subskrypcji replikacji i trybu failover maszyny wirtualnej do weryfikacji.

Po operacji sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz konta magazynu usługi Resource Manager zgodny lub sieci wirtualnej, można utworzyć na, klikając **+ konto magazynu** lub **+ sieć** przycisków w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
