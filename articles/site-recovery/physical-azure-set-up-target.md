---
title: Przygotowanie docelowego (fizycznych do platformy Azure) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania środowiska Azure, aby uruchomić replikowania serwerów fizycznych z systemem Windows lub Linux na platformie Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: a2465bb3397a175b6ad8b8be0de933dfae1dee5b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812628"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotowanie docelowego (VMware do platformy Azure)

W tym artykule opisano sposób przygotowania środowiska Azure do rozpoczęcia replikacji serwery fizyczne (x 64) z systemu Windows lub Linux na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Założono artykułu:
- Utworzono magazyn usług odzyskiwania, aby chronić serwery fizyczne. Można utworzyć magazyn usług odzyskiwania z [portalu Azure](http://portal.azure.com "portalu Azure").
- Masz [konfiguracji środowiska lokalnego](physical-azure-disaster-recovery.md) do replikowania serwerów fizycznych do platformy Azure.

## <a name="prepare-target"></a>Przygotowanie docelowego

Po zakończeniu **cel ochrony krok 1** i **krok 2: przygotowanie źródła**, zostają przeniesieni do **krok 3: docelowy**

![Przygotowanie docelowego](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subskrypcja:** z menu rozwijanego Wybierz subskrypcję, która ma zostać zreplikowana na serwerach fizycznych.
2. **Model wdrażania:** wybierz model wdrażania (Classic lub Resource Manager)

Oparte na modelu wdrażania wybranego, uruchomieniu aby upewnić się, że użytkownik ma co najmniej jedno konto magazynu zgodny i sieć wirtualną w docelowej subskrypcji replikacji i trybu failover serwerów fizycznych do weryfikacji.

Po operacji sprawdzania poprawności zakończy się pomyślnie, kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz konta magazynu usługi Resource Manager zgodny lub sieci wirtualnej, można utworzyć klikając **+ konto magazynu** lub **+ sieć** przycisków w górnej części strony.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie ustawień replikacji](vmware-azure-set-up-replication.md).
