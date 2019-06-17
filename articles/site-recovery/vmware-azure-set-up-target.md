---
title: Przygotowanie środowiska docelowego dla replikacji VMware na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania urządzenie docelowe środowisko platformy Azure do replikacji maszyn wirtualnych programu VMware na platformę Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723815"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Przygotowywanie środowiska docelowego dla odzyskiwania po awarii maszyn wirtualnych programu VMware lub serwery fizyczne do platformy Azure

W tym artykule opisano sposób przygotowania urządzenie docelowe środowisko platformy Azure, aby rozpocząć replikowanie maszyn wirtualnych VMware lub serwery fizyczne do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł zakłada:
- Utworzono magazyn usługi Recovery Services na [witryny Azure portal](https://portal.azure.com "witryny Azure portal") chronić maszyny źródłowej
- Skonfigurowano w lokalnym środowisku replikacji źródła [maszyn wirtualnych VMware](vmware-azure-set-up-source.md) lub [serwerów fizycznych](physical-azure-set-up-source.md) na platformie Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po zakończeniu **krok 1: Wybierz cel ochrony** i **krok 2: Przygotowywanie źródła**, nastąpi przekierowanie do **krok 3: Docelowy**

![Przygotowywanie celu](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego Wybierz subskrypcję, dla której chcesz wykonać replikację maszyn wirtualnych ani serwery fizyczne do.
2. **Model wdrażania:** Wybierz model wdrażania (Model Klasyczny lub usługi Resource Manager)

Oparte na modelu wdrażania wybranego, sprawdzania poprawności jest wykonywane do upewnij się, że co najmniej jedną sieć wirtualną w subskrypcji docelowej replikacji i trybu failover z maszyny wirtualnej lub serwer fizyczny z platformą.

Po operacje sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz sieć wirtualną można utworzyć jeden, klikając **+ sieć** znajdujący się u góry strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
