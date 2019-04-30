---
title: Publikowanie oferty obrazów kontenerów platformy Azure | Dokumentacja firmy Microsoft
description: Jak opublikować ofertę kontenerów platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472898"
---
# <a name="publish-container-offer"></a>Publikowanie oferty kontenera

 Po utworzeniu nowej oferty, używając **nowa oferta** strony, możesz opublikować ofertę. Wybierz **Publikuj** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania w celu skorzystania z oferty "Przejdź na żywo".

![Kroki publikowania dla oferty kontenera](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania. Szacowany czas do zakończenia każdego kroku jest również podana.


|  **Krok publikowania**           | **czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Weryfikowanie wymagań wstępnych         | 15 min   | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                        |
| Certyfikacja                  | 1 tydzień | Oferty są analizowane przez zespół certyfikacji platformy Azure. Oferta jest skanowany w poszukiwaniu wirusów, złośliwego oprogramowania, bezpieczeństwo, zgodność i problemy z zabezpieczeniami. Oferta jest sprawdzenie spełnia kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md) i [przygotowywanie zasobów technicznych](./cpp-create-technical-assets.md). Opinia firmy pod warunkiem, jeśli problem zostanie znaleziony. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta i systemy potencjalnych klientów są konfigurowane i konfiguracji. |
|  Podpisywania wydawcy             |  -        | Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Sprawdź, czy spełnia wszystkie wymagania dotyczące można wdrożyć swoją ofertę w wybranych subskrypcjach (w krokach informacji oferty).  Wybierz **Go Live** dzięki ofercie przejściem do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Zakończono oferty jest replikowana w regionach i systemów produkcyjnych w portalu marketplace. | 
| Na żywo                           | 4 dni |Oferta jest zwolniony, replikowane do określonych regionów wymagane i udostępnione publicznie. |

Zezwalaj na maksymalnie 10 dni roboczych. proces publikowania zakończyć i udostępnieniu oferty. Po zakończeniu procesu publikowania oferty kontenera zostaną wyświetlone w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Kolejne kroki

[Aktualizowanie istniejącej oferty kontenera w witrynie Azure Marketplace](./cpp-update-existing-offer.md)
