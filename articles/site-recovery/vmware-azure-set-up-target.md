---
title: Przygotowywanie obiektu docelowego replikacji maszyny Wirtualnej VMware w usłudze Azure Site Recovery
description: W tym artykule opisano sposób przygotowania docelowego środowiska platformy Azure dla replikacji maszyn wirtualnych VMware na platformie Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693170"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Przygotowanie środowiska docelowego do odzyskiwania po awarii maszyn wirtualnych vmware lub serwerów fizycznych na platformie Azure

W tym artykule opisano sposób przygotowania docelowego środowiska platformy Azure do rozpoczęcia replikowania maszyn wirtualnych VMware lub serwerów fizycznych na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W artykule przyjęto założenie:
- Utworzono magazyn usług odzyskiwania w witrynie [Azure portal,](https://portal.azure.com "Portal Azure") aby chronić swoje maszyny źródłowe
- Skonfigurowano środowisko lokalne w celu replikowania [źródłowych maszyn wirtualnych VMware](vmware-azure-set-up-source.md) lub [serwerów fizycznych](physical-azure-set-up-source.md) na platformie Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po ukończeniu **kroku 1: Wybierz cel ochrony** i krok **2: Przygotuj źródło,** zostaniesz zabrany do kroku **3: Cel**

![Przygotowywanie celu](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego wybierz subskrypcję, do której chcesz replikować maszyny wirtualne lub serwery fizyczne.
2. **Model wdrażania:** Wybierz model wdrażania (klasyczny lub Menedżer zasobów)

Na podstawie wybranego modelu wdrażania jest uruchamiana weryfikacja, aby upewnić się, że masz co najmniej jedną sieć wirtualną w subskrypcji docelowej do replikacji i pracy awaryjnej maszyny wirtualnej lub serwera fizycznego.

Po pomyślnym zakończeniu sprawdzania poprawności kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, klikając przycisk **+ Sieć** u góry strony.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
