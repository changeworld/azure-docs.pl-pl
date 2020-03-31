---
title: Konfigurowanie środowiska docelowego dla serwerów fizycznych w usłudze Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania docelowego środowiska platformy Azure do odzyskiwania po awarii serwerów fizycznych przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953900"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotuj obiekt docelowy (VMware na platformie Azure)

W tym artykule opisano sposób przygotowania środowiska platformy Azure do rozpoczęcia replikowania serwerów fizycznych (x64) z systemem Windows lub Linux na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W artykule przyjęto założenie:
- Utworzono magazyn usług odzyskiwania w celu ochrony serwerów fizycznych. Magazyn usług odzyskiwania można utworzyć z [witryny Azure portal](https://portal.azure.com "Portal Azure").
- Skonfigurowanie [środowiska lokalnego w](physical-azure-disaster-recovery.md) celu replikowania serwerów fizycznych na platformie Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po ukończeniu **kroku 1:Wybierz cel ochrony** i krok **2:Przygotuj źródło,** zostaniesz przesuń do kroku **3: Cel**

![Przygotowywanie celu](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego wybierz subskrypcję, do której chcesz replikować serwery fizyczne.
2. **Model wdrażania:** Wybierz model wdrażania (klasyczny lub Menedżer zasobów)

Na podstawie wybranego modelu wdrażania jest uruchamiana weryfikacja, aby upewnić się, że masz co najmniej jedno zgodne konto magazynu i sieć wirtualną w subskrypcji docelowej do replikacji i pracy awaryjnej serwerów fizycznych.

Po pomyślnym zakończeniu sprawdzania poprawności kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz zgodnego konta magazynu Menedżera zasobów lub sieci wirtualnej, możesz je utworzyć, klikając przycisk **+ Konto magazynu** lub + **Sieć** u góry strony.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
