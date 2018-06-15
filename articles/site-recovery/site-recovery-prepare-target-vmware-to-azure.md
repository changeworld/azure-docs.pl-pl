---
title: Przygotowanie docelowego (VMware do platformy Azure) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania środowiska platformy Azure, aby rozpocząć replikację maszyn wirtualnych VMware do platformy Azure.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: ec1322e95431d5fd38d8e05a66322239d3184ac0
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768424"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotowanie docelowego (VMware do platformy Azure)
> [!div class="op_single_selector"]
> * [Z programu VMware do platformy Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fizycznych do platformy Azure](./site-recovery-prepare-target-physical-to-azure.md)

W tym artykule opisano sposób przygotowania środowiska platformy Azure, aby rozpocząć replikację maszyn wirtualnych VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Założono artykułu:
- Utworzono magazyn usług odzyskiwania, aby chronić maszyny wirtualne VMware. Można utworzyć magazyn usług odzyskiwania z [portalu Azure](http://portal.azure.com "portalu Azure").
- Masz [konfiguracji środowiska lokalnego](./site-recovery-set-up-vmware-to-azure.md) do replikowania maszyn wirtualnych VMware do platformy Azure.

## <a name="prepare-target"></a>Przygotowanie docelowego

Po zakończeniu **cel ochrony krok 1** i **krok 2: przygotowanie źródła**, zostają przeniesieni do **krok 3: docelowy**

![Przygotowanie docelowego](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** z menu rozwijanego Wybierz subskrypcję, którą chcesz zreplikować maszyny wirtualnej.
2. **Model wdrażania:** wybierz model wdrażania (Classic lub Resource Manager)

Oparte na modelu wdrażania wybranego, uruchomieniu aby upewnić się, że masz co najmniej jedno konto magazynu zgodny i siecią wirtualną w docelowej subskrypcji replikacji i trybu failover maszyny wirtualnej do weryfikacji.

Po operacji sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz konta magazynu usługi Resource Manager zgodny lub sieci wirtualnej, można utworzyć na, klikając **+ konto magazynu** lub **+ sieć** przycisków w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](./site-recovery-setup-replication-settings-vmware.md).
