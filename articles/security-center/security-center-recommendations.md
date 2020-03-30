---
title: Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure
description: W tym dokumencie znajdziesz informacje o tym, jak zalecenia w usłudze Azure Security Center pomagają chronić zasoby platformy Azure i przestrzeganie zasad zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245306"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlić i zrozumieć zalecenia w usłudze Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnikiem krok po kroku.
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?

Zalecenia to działania, które należy podjąć w celu zabezpieczenia zasobów.

Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich usuwania.

Każde zalecenie zawiera:

- Krótki opis tego, co jest zalecane.
- Kroki naprawcze do przeprowadzenia w celu wdrożenia zalecenia. <!-- In some cases, Quick Fix remediation is available. -->
- Jakie zasoby są w potrzebie wykonywania zalecanych działań na nich.
- **Wpływ Secure Score**, czyli kwota, która zostanie wydrzesza, jeśli zaimplementujesz to zalecenie.

## <a name="monitor-recommendations"></a>Monitorowanie zaleceń<a name="monitor-recommendations"></a>

Usługa Security Center analizuje stan zabezpieczeń zasobów w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. **Kafelek Zalecenia** w obszarze **Przegląd** zawiera całkowitą liczbę zaleceń zidentyfikowanych przez centrum zabezpieczeń.

![Omówienie centrum zabezpieczeń](./media/security-center-recommendations/asc-overview.png)

1. Wybierz **kafelek Rekomendacje** w obszarze **Przegląd**. Zostanie otwarta lista **Rekomendacje.**

      ![Wyświetlanie zaleceń](./media/security-center-recommendations/view-recommendations.png)

    Można filtrować zalecenia. Aby filtrować zalecenia, wybierz **filtr** na **bloku Zalecenia.** Otwiera się **blok Filtruj** i wybierasz wartości ważności i stanu, które mają być widoczne.

   * **ZALECENIA**: Zalecenie.
   * **SECURE SCORE IMPACT:** Wynik generowany przez centrum zabezpieczeń przy użyciu zaleceń dotyczących zabezpieczeń i stosowanie zaawansowanych algorytmów w celu określenia, jak ważne jest każde zalecenie. Aby uzyskać więcej informacji, zobacz [Bezpieczne obliczanie wyniku](security-center-secure-score.md#secure-score-calculation).
   * **ZASÓB**: Wyświetla listę zasobów, do których stosuje się to zalecenie.
   * **PASEK STANU**: Opisuje ważność tego konkretnego zalecenia:
       * **Wysoki (czerwony)**: Luka w zabezpieczeniach występuje w znaczącym zasobie (takim jak aplikacja, maszyna wirtualna lub grupa zabezpieczeń sieci) i wymaga uwagi.
       * **Średni (Pomarańczowy)**: Istnieje luka i niekrytyczne lub dodatkowe kroki są wymagane, aby ją wyeliminować lub zakończyć proces.
       * **Niski (niebieski)**: Istnieje luka, która powinna zostać rozwiązana, ale nie wymaga natychmiastowej uwagi. (Domyślnie niskie rekomendacje nie są prezentowane, ale możesz filtrować niskie rekomendacje, jeśli chcesz je zobaczyć). 
       * **Zdrowy (Zielony)**:
       * **Niedostępne (szary)**:

1. Aby wyświetlić szczegóły każdej rekomendacji, kliknij zalecenie.

    ![Szczegóły zalecenia](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Zobacz [klasyczne i resource manager modeli wdrażania](../azure-classic-rm.md) zasobów platformy Azure.
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w u centrum zabezpieczeń. Aby dowiedzieć się, jak zaradzić zaleceniu:

* [Korygowanie zaleceń](security-center-remediate-recommendations.md) — dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
