---
title: Publikowanie oferty maszyny wirtualnej w portalu Azure Marketplace
description: Zawiera listę kroków wymaganych do opublikowania istniejącej oferty maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824413"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikowanie oferty maszyny wirtualnej

 Ostatnim krokiem po zdefiniowaniu oferty w portalu i utworzeniu skojarzonych zasobów technicznych jest przesłanie oferty do opublikowania. Na poniższym diagramie przedstawiono główne kroki w procesie publikowania do "go Live":

![Kroki publikowania dla oferty maszyny wirtualnej](./media/publishvm_013.png)

W poniższej tabeli opisano te kroki i zawarto maksymalne oszacowanie czasu dla ich ukończenia:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | 15 minut   | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Sprawdzanie poprawności dysku testowego (opcjonalnie) | 2 godziny | Jeśli wybrano opcję włączenia dysku testowego, firma Microsoft zweryfikuje konfigurację dysku testowego, jego wdrożenie i replikację za pomocą wybranych regionów. |
| Certyfikacja                  | 3 dni | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z bezpieczeństwem i problemów z zabezpieczeniami. Opinie są podawane w przypadku znalezienia problemu. |
| Inicjowanie obsługi                   | 4 dni   | Oferta maszyny wirtualnej jest replikowana w systemach produkcyjnych w portalu Marketplace.               |
| Pakowanie i rejestracja generacji potencjalnego klienta | \< 1 godz.  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów są skonfigurowane i konfigurowane. |
|  Wydawca przygotowania             |  -        | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  |
| Inicjowanie obsługi                   | 4 dni | Zagotowana oferta maszyny wirtualnej jest replikowana w systemach i regionach produkcyjnych w portalu Marketplace. | 
| Na żywo                           | 4 dni | Oferta maszyny wirtualnej jest wydawana, replikowana do wymaganych regionów i udostępniona publicznie. |
|  |  |

Poczekaj maksymalnie 16 dni na ukończenie tego procesu.  Po wykonaniu tych kroków publikacji w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)zostanie wyświetlona oferta maszyny wirtualnej. 

