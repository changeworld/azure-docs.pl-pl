---
title: Publikowanie oferty maszyny wirtualnej w witrynie Azure Marketplace | Dokumentacja firmy Microsoft
description: Listy czynności wymagane do publikowania istniejącej maszyny wirtualnej oferują na portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 3cf6a3d9bcb9470fd3a6bd4fef964c1966adfa1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844265"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikowanie oferty maszyny wirtualnej

 Ostatnim krokiem po zdefiniowane oferty w portalu i utworzyć skojarzone zasoby techniczne, jest przesyłać oferty do opublikowania. Poniższy diagram przedstawia podstawowe kroki w procesie publikowania "Przejdź na żywo":

![Kroki publikowania dla maszyny wirtualnej oferty](./media/publishvm_013.png)

Poniższej tabeli opisano te kroki i udostępnia Szacowanie maksymalny czas na ich zakończenie:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikowania**           | **czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Weryfikowanie wymagań wstępnych         | 15 min   | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                        |
| Testowanie weryfikacji dysku (opcjonalnie) | 2 godziny | Jeśli wybrano, aby włączyć wersję testową, Microsoft sprawdza poprawność konfiguracji testowej, jego wdrożenia i replikacji za pośrednictwem wybranych regionach. |
| Certyfikacja                  | 3 dni | Oferty są analizowane przez zespół certyfikacji platformy Azure. Ten krok przeprowadzi skanowanie wirusów, złośliwe oprogramowanie, bezpieczeństwa, zgodności i problemy z zabezpieczeniami. Ile opinii jest, że problem zostanie znaleziony. |
| Inicjowanie obsługi                   | 4 dni   | Oferty maszyny Wirtualnej jest replikowany w systemach produkcyjnych w portalu marketplace.               |
| Tworzenie pakietów i rejestracji generowania potencjalnych klientów | \< 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta i systemy potencjalnych klientów są konfigurowane i konfiguracji. |
|  Przygotowania wydawcy             |  -        | Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Sprawdź, czy spełnia wszystkie wymagania dotyczące można wdrożyć swoją ofertę w wybranych subskrypcjach (w krokach informacji oferty).  |
| Inicjowanie obsługi                   | 4 dni | Ukończone oferty maszyny Wirtualnej jest replikowana w regionach i systemów produkcyjnych w portalu marketplace. | 
| Na żywo                           | 4 dni | Oferty maszyny Wirtualnej jest zwolniony, replikowane do określonych regionów wymagane i udostępnione publicznie. |
|  |  |

Zezwala na maksymalnie 16 dni na ukończenie tego procesu.  Po przejściu przez następujące kroki publikowania oferty maszyny Wirtualnej zostaną wyświetlone w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

