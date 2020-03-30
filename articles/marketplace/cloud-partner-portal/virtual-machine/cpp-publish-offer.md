---
title: Publikowanie oferty maszyny wirtualnej w portalu Azure Marketplace
description: Wyświetla listę kroków wymaganych do opublikowania istniejącej oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277348"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikowanie oferty maszyny wirtualnej

 Ostatnim krokiem po zdefiniowaniu oferty w portalu i utworzeniu skojarzonych zasobów technicznych jest przesłanie oferty do publikacji. Na poniższym diagramie przedstawiono główne kroki procesu publikowania do "go live":

![Kroki publikowania dla oferty maszyny wirtualnej](./media/publishvm_013.png)

W poniższej tabeli opisano te kroki i przedstawiono maksymalny czas szacowany czas ich ukończenia:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikowania**           | **Czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie poprawności wymagań wstępnych         | 15 min.   | Informacje o ofercie i ustawienia oferty są sprawdzane.                        |
| Sprawdzanie poprawności dysku testowego (opcjonalnie) | 2 godziny | Jeśli wybrano opcję włączania dysku testowego, firma Microsoft sprawdza poprawność konfiguracji dysku testowego, jej wdrożenia i replikacji w wybranych regionach. |
| Certyfikacja                  | 3 dni | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z przepisami bezpieczeństwa i problemów z zabezpieczeniami. Informacja zwrotna jest dostarczana w przypadku znalezienia problemu. |
| Inicjowanie obsługi                   | 4 dni   | Oferta maszyn wirtualnych jest replikowana w systemach produkcyjnych marketplace.               |
| Rejestracja opakowań i promiliacji ołowiu | \<1 godz.  | Zasoby techniczne oferty są pakowane do użytku klienta, a systemy ołowiane są konfigurowane i konfigurowane. |
|  Znakowanie wydawcy             |  -        | Ostateczna recenzja wydawcy i potwierdzenie przed rozpoczęciem oferty. Możesz wdrożyć ofertę w wybranych subskrypcjach (w krokach informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie twoje wymagania.  |
| Inicjowanie obsługi                   | 4 dni | Sfinalizowana oferta maszyny Wirtualnej jest replikowana w systemach produkcyjnych i regionach marketplace. | 
| Na żywo                           | 4 dni | Oferta maszyny Wirtualnej jest zwalniana, replikowana do wymaganych regionów i udostępniana publicznie. |
|  |  |

Poczekaj do 16 dni na zakończenie tego procesu.  Po przejściu przez te kroki publikowania oferta maszyny Wirtualnej zostanie wyświetlona w portalu [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

