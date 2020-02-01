---
title: Zabezpiecz ocenę w Azure Security Center | Microsoft Docs
description: " Ustaw priorytety zaleceń dotyczących zabezpieczeń, korzystając z bezpiecznego wyniku w Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903448"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Popraw swój Bezpieczny wynik w Azure Security Center

> [!NOTE]
> W wersji zapoznawczej jest dostępny ulepszony bezpieczny wynik. Zwiększony bezpieczny wynik będzie ostatecznie zastępować istniejący wynik, ale przez czas, gdy będzie działać obok siebie, aby ułatwić przejście.
>
> Aby uzyskać szczegółowe informacje na temat korzyści ze zwiększonych zabezpieczeń, zobacz [tutaj](secure-score-security-controls.md).
>
> Aby skorzystać z wersji zapoznawczej, Otwórz Portal Azure, uruchom Azure Security Center i wybierz pozycję bezpieczny wynik. W tym miejscu zobaczysz transparent w górnej części strony oferującej nowy bezpieczny wynik. Alternatywnie kliknij [tutaj](https://aka.ms/ascnewscore).

Dzięki temu wielu usługom, które oferują korzyści z zabezpieczeń, często trudno jest wiedzieć, jakie kroki należy podjąć najpierw w celu zabezpieczenia i ograniczenia obciążenia. Usługa Azure Security Score przegląda zalecenia dotyczące zabezpieczeń i ustala ich priorytety, aby wiedzieć, które zalecenia należy wykonać w pierwszej kolejności. Ułatwia to znalezienie najważniejszych luk w zabezpieczeniach, dzięki czemu można określić priorytety badania. Wskaźnik bezpieczeństwa ułatwia ocenę poziomu bezpieczeństwa zasobów.

## <a name="secure-score-calculation"></a>Bezpieczne Obliczanie wyniku

Security Center naśladuje pracy analityka zabezpieczeń, przeglądanie zaleceń dotyczących zabezpieczeń i stosowanie zaawansowanych algorytmów w celu określenia, jak kluczowe są poszczególne zalecenia.
Usługa Azure Security Center stale przegląda Twoje aktywne rekomendacje i oblicza swój Bezpieczny wynik na podstawie ich, więc wynik zalecenia pochodzi od jego ważności i najlepszych rozwiązań w zakresie zabezpieczeń, które mają wpływ na bezpieczeństwo obciążeń.

Security Center zapewnia również **ogólny, bezpieczny wynik**. 

**Ogólnie bezpieczny wynik** to akumulacja wszystkich ocen rekomendacji. W zależności od wybranych opcji można wyświetlić ogólny, bezpieczny wynik w ramach subskrypcji lub grup zarządzania. Wyniki będą się różnić w zależności od wybranej subskrypcji i aktywnych zaleceń w tych subskrypcjach.

Aby sprawdzić, które zalecenia mają największy wpływ na bezpieczeństwo, możesz wyświetlić trzy najważniejsze zalecenia na pulpicie nawigacyjnym Security Center lub sortować zalecenia w bloku listy rekomendacji przy użyciu kolumny " **bezpieczeństwo oceny** ".

Aby wyświetlić ogólny, bezpieczny wynik:

1. Na pulpicie nawigacyjnym platformy Azure kliknij **Security Center** a następnie kliknij przycisk **Zabezpiecz ocenę**.

2. W górnej części można zobaczyć bezpieczny wynik:
   - **Ogólny wskaźnik bezpieczeństwa** reprezentuje wskaźnik dla zasad w wybranej subskrypcji
   - **Zabezpieczenia oceny według kategorii** pokazują, które zasoby wymagają największej uwagi
   - **Najważniejsze zalecenia według bezpiecznego wpływu na ocenę** zapewniają listę zaleceń, które pomogą ulepszyć bezpieczny wynik w większości sytuacji, gdy zostaną zaimplementowane.
 
   ![wynik zabezpieczony](./media/security-center-secure-score/secure-score-dashboard.png)

3. W poniższej tabeli można zobaczyć każdą z Twoich subskrypcji i ogólny łączny wynik dla każdego z nich.

   > [!NOTE]
   > Suma wskaźników bezpieczeństwa poszczególnych subskrypcji nie jest równa ogólnemu wskaźnikowi bezpieczeństwa. Wskaźnik bezpieczeństwa jest obliczany na podstawie proporcji między liczbą zasobów w dobrej kondycji i ogólną liczbą zasobów dla rekomendacji. Nie jest on sumą wskaźników bezpieczeństwa poszczególnych subskrypcji. 
   >
4. Kliknij pozycję **Wyświetl zalecenia** , aby zobaczyć zalecenia dla tej subskrypcji, które możesz skorygować, aby poprawić swój Bezpieczny wynik.
4. Na liście zaleceń można zobaczyć, że dla każdego zalecenia istnieje kolumna, która reprezentuje **bezpieczny wpływ na ocenę**. Ta liczba przedstawia informacje o tym, ile ogólnego stanu bezpiecznego poprawi się, jeśli wykonasz zalecenia. Na przykład na poniższym ekranie, jeśli **korygujesz luki w zabezpieczeniach, konfiguracje zabezpieczeń kontenerów**, bezpieczny wynik zostanie zwiększony o 35 punktów.

   ![wynik zabezpieczony](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Indywidualny wskaźnik zabezpieczeń

Ponadto, aby wyświetlać pojedyncze, bezpieczne oceny, można je znaleźć w bloku poszczególnych rekomendacji.  

**Wskaźnik bezpieczeństwa zalecenia** jest obliczany na podstawie proporcji między liczbą zasobów w dobrej kondycji i ogólną liczbą zasobów. Jeśli liczba zasobów w dobrej kondycji jest równa łącznej liczbie zasobów, otrzymujesz maksymalny wskaźnik bezpieczeństwa zalecenia, który wynosi 50. Aby spróbować uzyskać wskaźnik bezpieczeństwa jak najbardziej zbliżony do maksymalnego, napraw zasoby będące w złej kondycji, postępując zgodnie z zaleceniami.

**Wpływ zalecenia** umożliwia sprawdzenie, o ile zwiększy się wskaźnik bezpieczeństwa, jeśli zastosujesz zalecane czynności. Jeśli na przykład Twój wskaźnik bezpieczeństwa wynosi 42 i **wpływ zalecenia** wynosi +3, wykonując czynności opisane w zaleceniu, zwiększysz swój wskaźnik do wartości 45.

Zalecenie wskazuje, które zagrożenia są narażone na Twoje obciążenie, jeśli nie zostaną podjęte czynności zaradcze.

![indywidualny rekomendacja bezpieczna Ocena](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak ulepszyć stan zabezpieczeń przy użyciu funkcji **bezpiecznego oceny** w Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.


