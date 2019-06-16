---
title: Konfigurowanie środowiska docelowego dla odzyskiwania po awarii lokalnych serwerów fizycznych do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania docelowego środowiska platformy Azure na potrzeby odzyskiwania po awarii serwerów fizycznych przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 41220ccdca945610d7d8ca87af0857114e2cef85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60949081"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotowywanie celu (program VMware do platformy Azure)

W tym artykule opisano sposób przygotowania środowiska platformy Azure do uruchamiania replikacji serwerów fizycznych (x 64) systemem Windows lub Linux na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł zakłada:
- Utworzono magazyn usługi Recovery Services można chronić swoje serwery fizyczne. Można utworzyć magazyn usługi Recovery Services z [witryny Azure portal](https://portal.azure.com "witryny Azure portal").
- Masz [konfiguracji środowiska lokalnego](physical-azure-disaster-recovery.md) replikowania serwerów fizycznych do platformy Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po zakończeniu **cel ochrony 1 kroku** i **krok 2: przygotowanie źródłowego**, nastąpi przekierowanie do **krok 3: Docelowy**

![Przygotowywanie celu](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego Wybierz subskrypcję, którą chcesz replikować serwery fizyczne do.
2. **Model wdrażania:** Wybierz model wdrażania (Model Klasyczny lub usługi Resource Manager)

Oparte na modelu wdrażania wybranego, uruchomiono aby upewnić się, że masz co najmniej jedno zgodne konto magazynu i sieć wirtualną w subskrypcji docelowej replikacji i trybu failover serwerów fizycznych do weryfikacji.

Po operacje sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz zgodne konto magazynu usługi Resource Manager lub sieć wirtualną można utworzyć jeden, klikając **+ konto magazynu** lub **+ sieć** przyciski w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
