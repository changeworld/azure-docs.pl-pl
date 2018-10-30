---
title: Konfigurowanie środowiska docelowego dla odzyskiwania po awarii lokalnych serwerów fizycznych do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania docelowego środowiska platformy Azure na potrzeby odzyskiwania po awarii serwerów fizycznych przy użyciu usługi Azure Site Recovery.
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: b89d04a6e2fd11a61de8b56690664f6204c208ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209296"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotowywanie celu (program VMware do platformy Azure)

W tym artykule opisano sposób przygotowania środowiska platformy Azure do uruchamiania replikacji serwerów fizycznych (x 64) systemem Windows lub Linux na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł zakłada:
- Utworzono magazyn usługi Recovery Services można chronić swoje serwery fizyczne. Można utworzyć magazyn usługi Recovery Services z [witryny Azure portal](http://portal.azure.com "witryny Azure portal").
- Masz [konfiguracji środowiska lokalnego](physical-azure-disaster-recovery.md) replikowania serwerów fizycznych do platformy Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po zakończeniu **cel ochrony 1 kroku** i **krok 2: przygotowanie źródłowego**, nastąpi przekierowanie do **krok 3: docelowy**

![Przygotowywanie celu](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subskrypcja:** z menu rozwijanego Wybierz subskrypcję, którą chcesz replikować serwery fizyczne do.
2. **Model wdrażania:** wybierz model wdrażania (Model Klasyczny lub usługi Resource Manager)

Oparte na modelu wdrażania wybranego, uruchomiono aby upewnić się, że masz co najmniej jedno zgodne konto magazynu i sieć wirtualną w subskrypcji docelowej replikacji i trybu failover serwerów fizycznych do weryfikacji.

Po operacje sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz zgodne konto magazynu usługi Resource Manager lub sieć wirtualną można utworzyć jeden, klikając **+ konto magazynu** lub **+ sieć** przyciski w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
