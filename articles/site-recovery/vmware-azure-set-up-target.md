---
title: Przygotowanie środowiska docelowego dla replikacji VMware na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania urządzenie docelowe środowisko platformy Azure do replikacji maszyn wirtualnych programu VMware na platformę Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837515"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Przygotowywanie środowiska docelowego dla odzyskiwania po awarii maszyn wirtualnych programu VMware lub serwery fizyczne do platformy Azure

W tym artykule opisano sposób przygotowania urządzenie docelowe środowisko platformy Azure, aby rozpocząć replikowanie maszyn wirtualnych VMware lub serwery fizyczne do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł zakłada:
- Utworzono magazyn usługi Recovery Services na [witryny Azure portal](http://portal.azure.com "witryny Azure portal") chronić maszyny źródłowej
- Skonfigurowano w lokalnym środowisku replikacji źródła [maszyn wirtualnych VMware](vmware-azure-set-up-source.md) lub [serwerów fizycznych](physical-azure-set-up-source.md) na platformie Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po zakończeniu **krok 1: cel ochrony wybierz** i **krok 2: przygotowanie źródłowego**, nastąpi przekierowanie do **krok 3: docelowy**

![Przygotowywanie celu](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** z menu rozwijanego Wybierz subskrypcję, dla której chcesz wykonać replikację maszyn wirtualnych ani serwery fizyczne do.
2. **Model wdrażania:** wybierz model wdrażania (Model Klasyczny lub usługi Resource Manager)

Oparte na modelu wdrażania wybranego, sprawdzanie poprawności jest wykonywane aby upewnić się, że masz co najmniej jedno zgodne konto magazynu i sieć wirtualną w subskrypcji docelowej replikacji i trybu failover Twojej maszyny wirtualnej lub serwer fizyczny z platformą.

Po operacje sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz zgodne konto magazynu usługi Resource Manager lub sieć wirtualną można utworzyć jeden, klikając **+ konto magazynu** lub **+ sieć** przyciski w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
