---
title: Publikowanie oferty obrazów kontenerów platformy Azure | Azure Marketplace
description: Jak opublikować ofertę kontenera platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: c612d2234e6c3c4e93c3c984242bb283ed85c27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279984"
---
# <a name="publish-container-offer"></a>Publikowanie oferty kontenera

 Po utworzeniu nowej oferty na stronie **Nowa oferta** można ją opublikować. Wybierz **pozycję Publikuj,** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go live".

![Kroki publikowania dla oferty kontenerów](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania. Podano również szacowany czas zakończenia każdego kroku.


|  **Krok publikowania**           | **Czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie poprawności wymagań wstępnych         | 15 min.   | Informacje o ofercie i ustawienia oferty są sprawdzane.                        |
| Certyfikacja                  | 1 tydzień | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Oferta jest skanowana pod kątem wirusów, złośliwego oprogramowania, zgodności z zasadami bezpieczeństwa i problemów z zabezpieczeniami. Oferta jest sprawdzana, czy spełnia wszystkie kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md) i przygotowanie [zasobów technicznych](./cpp-create-technical-assets.md). Informacje zwrotne są dostarczane w przypadku znalezienia problemu. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta, a systemy ołowiane są konfigurowane i konfigurowane. |
|  Rejestracja wydawcy             |  -        | Ostateczna recenzja wydawcy i potwierdzenie przed rozpoczęciem oferty. Możesz wdrożyć ofertę w wybranych subskrypcjach (w krokach informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie twoje wymagania.  Wybierz **opcję Przejdź na żywo,** aby Twoja oferta mogła przejść do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Gotowa oferta jest replikowana w systemach produkcyjnych i regionach marketplace. | 
| Na żywo                           | 4 dni |Oferta jest zwalniana, replikowana do wymaganych regionów i udostępniana publicznie. |

Poczekaj do 10 dni roboczych na zakończenie procesu publikowania i wydanie oferty. Po zakończeniu procesu publikowania oferta kontenera zostanie wyświetlona w portalu [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Następne kroki

[Aktualizowanie istniejącej oferty kontenerów w portalu Azure Marketplace](./cpp-update-existing-offer.md)
