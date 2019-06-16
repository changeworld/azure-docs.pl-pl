---
title: Zabezpieczanie oceny w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Ustaw priorytet swoje zalecenia dotyczące zabezpieczeń przy użyciu bezpiecznego oceny w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968289"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Poprawa bezpiecznego ocenę w usłudze Azure Security Center


W przypadku tak wiele usług oferuje korzyści w zakresie zabezpieczeń jest często wiadomo, jakie czynności umożliwiające najpierw bezpieczny i wzmocnione obciążenia. Wynik bezpiecznej platformy Azure monitoruje zalecenia w zakresie zabezpieczeń i ich priorytetem, aby było wiadomo, które zalecenia, aby najpierw wykonać. Dzięki temu można znaleźć najpoważniejsze luk w zabezpieczeniach, dzięki czemu można określić priorytet badania. Bezpieczne wynik jest narzędziem, które pomaga ocenić poziomu bezpieczeństwa obciążenia.

## <a name="secure-score-calculation"></a>Zabezpieczanie obliczania wyniku

Usługa Security Center naśladuje pracy przez analityka zabezpieczeń, przeglądania zalecenia w zakresie zabezpieczeń i stosowanie zaawansowanych algorytmów do ustalenia, jak niezwykle istotne jest poszczególne zalecenia.
Usługi Azure Security center stale monitoruje możesz aktywne zalecenia i oblicza bezpiecznego ocenę na ich podstawie, wynik zalecenie jest tworzony na podstawie jego ważności i najlepszych rozwiązań dotyczących zabezpieczeń, które będzie miało wpływ na bezpieczeństwo obciążenia najbardziej.

Usługa Security Center zapewnia także **secure ogólną ocenę**. 

**Zabezpieczanie ogólny wynik** jest nagromadzenia wyniki zalecenia. Możesz wyświetlić ogólną bezpiecznego ocenę w Twojej subskrypcji lub grupy zarządzania, w zależności od tego, możesz wybrać. Wynik będzie zależeć od wybrano subskrypcji i aktywne zalecenia w ramach tych subskrypcji.

 
Zalecenia dotyczące największy trzy pierwsze aby sprawdzić, które zalecenia dotyczące najbardziej wpływ na ocenę bezpieczny, mogą być wyświetlane na pulpicie nawigacyjnym usługi Security Center lub można sortować zalecenia za pomocą bloku listę zaleceń dotyczących **bezpiecznego wynik wpływ** kolumny.


Aby wyświetlić ocenę ogólną bezpiecznego:

1. Na pulpicie nawigacyjnym platformy Azure kliknij **usługi Security Center** a następnie kliknij przycisk **bezpiecznego wynik**.
2. U góry możesz zobaczyć bezpiecznego ocena najważniejsze funkcje:
   - **Ogólny wskaźnik bezpieczeństwa** reprezentuje wskaźnik dla zasad w wybranej subskrypcji
   - **Zabezpieczanie wynik według kategorii** dowiesz się, które zasoby wymagają uwagi najbardziej
   - **Najlepszych zaleceń zabezpieczenia przez ocenę wpływu** zawiera listę zaleceń, które poprawią bezpiecznego ocenę, najlepiej, jeśli zostaną zaimplementowane.
 
   ![Zabezpieczanie wynik](./media/security-center-secure-score/secure-score-dashboard.png)

3. W poniższej tabeli widać wszystkich subskrypcji i wynik ogólny bezpieczny dla każdego.

   > [!NOTE]
   > Suma wskaźników bezpieczeństwa poszczególnych subskrypcji nie jest równa ogólnemu wskaźnikowi bezpieczeństwa. Wskaźnik bezpieczeństwa jest obliczany na podstawie proporcji między liczbą zasobów w dobrej kondycji i ogólną liczbą zasobów dla rekomendacji. Nie jest on sumą wskaźników bezpieczeństwa poszczególnych subskrypcji. 
   >
4. Kliknij przycisk **Wyświetl zalecenia** Aby wyświetlić zalecenia dla tej subskrypcji, która może rozwiązać w celu bezpiecznego ocenę.
4. Na liście zaleceń widać, że każde zalecenie, aby uzyskać to kolumna, która reprezentuje **Secure ocenę wpływu**. Ten numer reprezentuje ile zwiększyć ogólną bezpiecznego ocenę w sytuacji, jeśli należy postępować zgodnie z zaleceniami. Na przykład na ekranie poniżej, jeśli użytkownik **korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń w kontenerze**, bezpieczne ocenę ceny wzrosną o 35 punktów.

   ![Zabezpieczanie wynik](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Indywidualne bezpieczne wynik

Ponadto aby wyświetlić poszczególne wyniki bezpieczne, można znaleźć je w bloku poszczególne zalecenia.  

**Wskaźnik bezpieczeństwa zalecenia** jest obliczany na podstawie proporcji między liczbą zasobów w dobrej kondycji i ogólną liczbą zasobów. Jeśli liczba zasobów w dobrej kondycji jest równa łącznej liczbie zasobów, otrzymujesz maksymalny wskaźnik bezpieczeństwa zalecenia, który wynosi 50. Aby spróbować uzyskać wskaźnik bezpieczeństwa jak najbardziej zbliżony do maksymalnego, napraw zasoby będące w złej kondycji, postępując zgodnie z zaleceniami.

**Wpływ zalecenia** umożliwia sprawdzenie, o ile zwiększy się wskaźnik bezpieczeństwa, jeśli zastosujesz zalecane czynności. Jeśli na przykład Twój wskaźnik bezpieczeństwa wynosi 42 i **wpływ zalecenia** wynosi +3, wykonując czynności opisane w zaleceniu, zwiększysz swój wskaźnik do wartości 45.

Zalecenie przedstawia zagrożenia, które obciążenia jest narażony na, jeśli nie zostaną podjęte kroki korygowania.

![wynik bezpiecznego poszczególne zalecenia](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Kolejne kroki
Ten artykuł pokazuje, jak zwiększyć, używając poziom zabezpieczeń **bezpiecznego wynik** w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.


