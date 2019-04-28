---
title: Publikowanie oferty modułu usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak stawiane ofertom moduł usługi IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: dfa512a26334567301812ecefd8c5673b0ea4094
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916160"
---
# <a name="publish-iot-edge-module-offer"></a>Publikowanie oferty modułu usługi IoT Edge

 Po utworzeniu nowej oferty, podając informacje o **nowa oferta** strony, możesz opublikować ofertę. Wybierz **Publikuj** aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania w celu skorzystania z oferty "Przejdź na żywo".

![Kroki publikowania dla modułu usługi IoT Edge oferty](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania, za pomocą szacowany czas (maksimum) do wykonania poszczególnych kroków.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikowania**           | **czas**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Weryfikowanie wymagań wstępnych         | 15 min   | Oferuje informacje i ofertę, ustawienia zostaną zweryfikowane.                        |
| Certyfikacja                  | 2 tygodnie | Oferty są analizowane przez zespół certyfikacji platformy Azure. Ten krok przeprowadzi skanowanie wirusów, złośliwe oprogramowanie, bezpieczeństwa, zgodności i problemy z zabezpieczeniami. Zostanie również sprawdzić, czy ta oferta modułu usługi IoT Edge spełnia wszystkie kryteria kwalifikacyjne (zobacz [wymagania wstępne](./cpp-prerequisites.md) i [przygotowywanie zasobów technicznych](./cpp-create-technical-assets.md)). Ile opinii jest, że problem zostanie znaleziony. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku klienta i systemy potencjalnych klientów są konfigurowane i konfiguracji. |
|  Logowania wydawcy wyłączone             |  -        | Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Sprawdź, czy spełnia wszystkie wymagania dotyczące można wdrożyć swoją ofertę w wybranych subskrypcjach (w krokach informacji oferty).  Wybierz **Go Live** dzięki ofercie przejściem do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Ukończone oferty jest replikowana w regionach i systemów produkcyjnych w portalu marketplace. | 
| Na żywo                           | 4 dni |Oferta jest zwolniony, replikowane do określonych regionów wymagane i udostępnione publicznie. |

Zezwalaj na maksymalnie 10 dni roboczych. proces publikowania zakończyć i udostępnieniu oferty. Po zakończeniu procesu publikowania oferty modułu usługi IoT Edge zostaną wyświetlone w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Kolejne kroki

- [Aktualizowanie istniejącej oferty modułu usługi IoT Edge w witrynie Azure Marketplace](./cpp-update-existing-offer.md)
