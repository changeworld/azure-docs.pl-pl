---
title: Zalecenia dotyczące zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób zalecenia w usłudze Azure Security Center ułatwiają ochronę zasobów platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
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
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064236"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlić i poznać zalecenia w usłudze Azure Security Center, aby pomóc w ochronie Twoich zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

## <a name="implementing-security-recommendations"></a>Wdrażanie zaleceń dotyczących zabezpieczeń
### <a name="set-recommendations"></a>Zestaw zaleceń
W [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md), Dowiedz się, jak:

* Podczas konfigurowania zasad zabezpieczeń.
* Włącz zbieranie danych.
* Wybierz, które zalecenia, aby zobaczyć jako część zasad zabezpieczeń.

Bieżący Centrum zalecenia dotyczące zasad wokół aktualizacji systemu, reguły linii bazowej, programy chroniące przed złośliwym kodem [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) podsieci i interfejsów sieciowych, inspekcji usługi SQL database, SQL database technologii transparent data encryption, i zapory aplikacji sieci web.  [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) zawiera opis każdej opcji zalecenia.

### <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania
Po skonfigurowaniu zasad zabezpieczeń usługa Security Center analizuje stan zabezpieczeń zasobów w celu identyfikowania potencjalnych luk w zabezpieczeniach. **Zalecenia** kafelka w obszarze **Przegląd** zawiera całkowitą liczbę zaleceniami zostały zidentyfikowane przez usługę Security Center.

![Omówienie usługi Security center](./media/security-center-recommendations/asc-overview.png)

1. Wybierz **Kafelek zalecenia** w obszarze **Przegląd**. **Zalecenia** zostanie otwarta lista.
    
      ![Wyświetlanie zaleceń](./media/security-center-recommendations/view-recommendations.png)

    Można filtrować zalecenia. Aby filtrować zalecenia, wybierz **filtru** na **zalecenia** bloku. **Filtru** zostanie otwarty blok, a następnie wybierz ważność i stan wartości, które chcesz wyświetlić.

   * **ZALECENIA DOTYCZĄCE**: Zalecenie.
   * **ZABEZPIECZANIE OCENĘ WPŁYWU**: Ocena generowane przez usługę Security Center przy użyciu zalecenia w zakresie zabezpieczeń i stosowanie zaawansowanych algorytmów do ustalenia, jak niezwykle istotne jest poszczególne zalecenia. Aby uzyskać więcej informacji, zobacz [Secure obliczania wyniku](security-center-secure-score.md#secure-score-calculation).
   * **ZASÓB**: Wyświetla listę zasobów, do których zostanie zastosowana tego zalecenia.
   * **PASKI STANU**:  Opisuje ważność określonego zalecenia:
       * **Wysoki (czerwony)** : Luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (np. aplikacji, maszyny Wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
       * **Średni (kolor pomarańczowy)** : Istnieje luka w zabezpieczeniach i niekrytyczne lub dodatkowe kroki są wymagane, aby wyeliminować go lub do ukończenia procesu.
       * **Niski (niebieski)** : Istnieje luka w zabezpieczeniach, które powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale można filtrować według zalecenia o niskiej ważności, jeśli chcesz je zobaczyć). 
       * **Zdrowy (zielony)** :
       * **Niedostępny (szary)** :

1. Aby wyświetlić szczegóły każdego zalecenia, kliknij zalecenie.

    ![Szczegóły rekomendacji](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Zobacz [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
  
 ### <a name="apply-recommendations"></a>Stosowanie zalecenia
> Po zapoznaniu się z wszystkich zaleceń, zdecyduj, który z nich zastosować najpierw. Zalecamy użycie bezpiecznego ocena wpływu na ocenę, zalecenia, które powinny być stosowane najpierw.

1. Z listy kliknij zalecenie.
1. Postępuj zgodnie z instrukcjami w *czynności zaradczych* sekcji.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie Przedstawiliśmy zalecenia dotyczące zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
