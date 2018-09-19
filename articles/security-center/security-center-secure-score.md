---
title: Zabezpieczanie oceny w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Ustaw priorytet swoje zalecenia dotyczące zabezpieczeń przy użyciu bezpiecznego oceny w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131322"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Poprawa bezpiecznego ocenę w usłudze Azure Security Center


W przypadku tak wiele usług oferuje korzyści w zakresie zabezpieczeń jest często wiadomo, jakie czynności umożliwiające najpierw bezpieczny i wzmocnione obciążenia. Wynik bezpiecznej usłudze Azure Security Center monitoruje zalecenia w zakresie zabezpieczeń i priorytetem je, aby było wiadomo, które zalecenia, aby najpierw wykonać pomaga znaleźć najpoważniejsze luk w zabezpieczeniach, dzięki czemu można określić priorytet badania. Bezpieczne wynik jest narzędzie miary, które pomaga utrwalanie zabezpieczeń do osiągnięcia bezpiecznego obciążenia.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![Bezpieczny pulpit nawigacyjny wynik](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Zabezpieczanie obliczania wyniku

Usługa Security Center naśladuje pracy analityka zabezpieczeń, przeglądania zalecenia w zakresie zabezpieczeń i stosowanie zaawansowanych algorytmów do ustalenia, jak niezwykle istotne jest poszczególne zalecenia.
Usługi Azure Security center stale monitoruje możesz aktywne zalecenia i oblicza bezpiecznego ocenę na ich podstawie, wynik zalecenie jest tworzony na podstawie jego ważności i zabezpieczenia najlepszych rozwiązań, które będzie miało wpływ na bezpieczeństwo obciążenia najbardziej.

**Secure wynik** jest obliczenia oparte na stosunek między zasoby w dobrej kondycji i łączna liczba zasobów. Jeśli liczba zasoby w dobrej kondycji jest równa łączna liczba zasobów, zostanie wyświetlony maksymalna ocena bezpiecznego 50. Spróbuj pobrać ocenę bezpiecznego bliżej max wynik, napraw zasoby w złej kondycji, postępując zgodnie z zaleceniami.

Security Center zapewnia także ogólną ocenę bezpieczne. 

**Zabezpieczanie ogólny wynik** jest nagromadzenia wszystkie zalecenia. Możesz wyświetlić ogólną bezpiecznego ocenę w Twojej subskrypcji lub grupy zarządzania, w zależności od tego, możesz wybrać. Wynik będzie zależeć od wybrano subskrypcji i aktywne zalecenia w ramach tych subskrypcji.

 

Aby sprawdzić, które zalecenia są najbardziej wpływ na ocenę bezpieczny, zalecenia największy wpływ pierwsze 3 mogą być wyświetlane na pulpicie nawigacyjnym usługi Security Center, lub można sortować zalecenia za pomocą bloku listę zaleceń dotyczących **bezpiecznego wynik wpływ** kolumny.


Aby wyświetlić ocenę ogólną bezpiecznego:

1. Na pulpicie nawigacyjnym platformy Azure kliknij **usługi Security Center** a następnie kliknij przycisk **zalecenia**.
2. U góry widoczny jest bezpieczne wynik, który reprezentuje wynik na zasady dla wybranej subskrypcji. 
2. W poniższej tabeli, która zawiera listę zaleceń, widać, że każde zalecenie, aby uzyskać to kolumna, która reprezentuje **Secure ocenę wpływu**. Ten numer reprezentuje ile zwiększyć ogólną bezpiecznego ocenę w sytuacji, jeśli należy postępować zgodnie z zaleceniami. Na przykład na ekranie poniżej, jeśli użytkownik **korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń w kontenerze**, bezpieczne ocenę ceny wzrosną o 35 punktów.

   ![Zabezpieczanie wynik](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Indywidualne bezpieczne wynik

Ponadto aby wyświetlić poszczególne wyniki bezpieczne, można znaleźć je w bloku poszczególne zalecenia.  

**Zalecenie secure wynik** jest obliczenia oparte na stosunek między zasoby w dobrej kondycji i łączna liczba zasobów. Jeśli liczba zasoby w dobrej kondycji jest równa łączna liczba zasobów, zostanie wyświetlony maksymalna ocena bezpiecznego zalecenia. Spróbuj pobrać ocenę bezpiecznego bliżej max wynik, Usuń zasoby w złej kondycji, wykonując czynności zaradczych.

**Wpływ rekomendacji** umożliwia sprawdzenie, ile bezpiecznego ocenę poprawi Jeśli zastosujesz kroków zaleceń. Na przykład jeśli Twój bezpieczny wynik jest 42 i **wpływ rekomendacji** + 3, wykonanie kroków opisanych w zaleceniu, bezpieczne ocenę poprawi przestanie 45.

Zalecenie przedstawia zagrożenia, które obciążenia jest narażony na, jeśli nie zostaną podjęte kroki korygowania.

![wynik bezpiecznego poszczególne zalecenia](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł pokazuje, jak zwiększyć, używając poziom zabezpieczeń **bezpiecznego wynik** w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.


