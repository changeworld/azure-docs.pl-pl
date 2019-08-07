---
title: Zalecenia dotyczące zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób, w jaki zalecenia w Azure Security Center pomagają chronić zasoby platformy Azure i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 229b8949facae34a809c0789154a3b56264ee2c5
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779018"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlać i zrozumieć zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnikiem krok po kroku.
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?

Zalecenia to akcje, które należy wykonać, aby zabezpieczyć zasoby.

Security Center okresowe Analizowanie stanu zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich usuwania.

Każde zalecenie oferuje następujące informacje:

- Krótki opis tego, co jest zalecane.
- Kroki korygowania, które należy wykonać w celu wdrożenia zalecenia. <!-- In some cases, one-click remediation is available. -->
- Które zasoby są wymagane do wykonania zalecanej akcji.
- Istotny **wpływ na ocenę**, czyli ilość, która będzie obowiązywać w przypadku zaimplementowania tego zalecenia.

## Zalecenia dotyczące monitorowania<a name="monitor-recommendations"></a>

Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. Kafelek **zalecenia** w obszarze **Przegląd** przedstawia łączną liczbę zaleceń identyfikowanych przez Security Center.

![Omówienie usługi Security Center](./media/security-center-recommendations/asc-overview.png)

1. Wybierz **kafelek rekomendacje** w obszarze **Przegląd**. Zostanie otwarta lista rekomendacje.

      ![Wyświetl rekomendacje](./media/security-center-recommendations/view-recommendations.png)

    Możesz filtrować zalecenia. Aby filtrować zalecenia, wybierz pozycję **Filtruj** w bloku **zalecenia** . Zostanie otwarty blok **filtru** i zostanie wybrana wartość ważność i stan, które mają być wyświetlane.

   * **ZALECENIA**: Zalecenie.
   * **BEZPIECZNY WPŁYW OCENY**: Wynik wygenerowany przez Security Center przy użyciu zaleceń dotyczących zabezpieczeń oraz stosowanie zaawansowanych algorytmów w celu określenia, jak kluczowe są poszczególne zalecenia. Aby uzyskać więcej informacji, zobacz temat [bezpieczne Obliczanie wyniku](security-center-secure-score.md#secure-score-calculation).
   * **ZASÓB**: Wyświetla listę zasobów, do których stosuje się to zalecenie.
   * **PASKI STANU**:  Opisuje ważność danego zalecenia:
       * **Wysoki (czerwony)** : Istnieje luka w zabezpieczeniach, która ma zrozumiały zasób (na przykład aplikację, maszynę wirtualną lub sieciową grupę zabezpieczeń) i wymaga uwagi.
       * **Średni (pomarańczowy)** : Istnieje luka w zabezpieczeniach, która nie jest krytyczna lub wymagane jest wykonanie dodatkowych czynności w celu wyeliminowania tego stanu lub ukończenia procesu.
       * **Niska (niebieska)** : Istnieje usterka, którą należy rozwiązać, ale nie wymaga natychmiastowej uwagi. (Domyślnie małe rekomendacje nie są wyświetlane, ale można filtrować według niskich zaleceń, jeśli chcesz je zobaczyć). 
       * **Dobra kondycja (zielony)** :
       * **Niedostępne (szare)** :

1. Aby wyświetlić szczegóły każdego zalecenia, kliknij zalecenie.

    ![Szczegóły rekomendacji](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Zobacz [klasyczne i Menedżer zasobów modele wdrażania](../azure-classic-rm.md) dla zasobów platformy Azure.
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w Security Center. Aby dowiedzieć się, jak skorygować zalecenia:

* [Skoryguj zalecenia](security-center-remediate-recommendations.md) — Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
