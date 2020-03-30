---
title: Bezpieczny wynik w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: " Ustalaj priorytety zaleceń dotyczących zabezpieczeń przy użyciu bezpiecznego wyniku w usłudze Azure Security Center. "
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
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415764"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Popraw swój bezpieczny wynik w usłudze Azure Security Center

> [!NOTE]
> Ten artykuł dotyczy poprzedniej wersji bezpiecznego wyniku. To bezpieczne doświadczenie wynik jest nadal dostępna w interfejsie użytkownika, ale będzie stopniowo wycofywana w czasie. Dwa bezpieczne doświadczenia wynik są uruchomione obok siebie, aby umożliwić płynniejsze przejście.
>
> Aby uzyskać szczegółowe informacje na temat nowszego bezpiecznego wyniku, zobacz [tutaj](secure-score-security-controls.md).
>

Przy tak wielu usługach oferujących korzyści w zakresie bezpieczeństwa często trudno jest wiedzieć, jakie kroki należy podjąć w pierwszej kolejności, aby zabezpieczyć i wzmocnić obciążenie pracą. Bezpieczny wynik sprawdza zalecenia dotyczące zabezpieczeń i nadaje im priorytet, dzięki czemu wiesz, które zalecenia należy wykonać jako pierwsze. Pomaga to znaleźć najpoważniejsze luki w zabezpieczeniach, dzięki czemu można ustalić priorytety badania. Secure Score to narzędzie, które pomaga ocenić postawę bezpieczeństwa obciążenia.

## <a name="secure-score-calculation"></a>Bezpieczne obliczanie wyniku

Usługa Security Center naśladuje pracę analityka zabezpieczeń, przegląda zalecenia dotyczące zabezpieczeń i stosuje zaawansowane algorytmy, aby określić, jak ważne jest każde zalecenie.
Usługa Azure Security Center stale przegląda aktywne zalecenia i oblicza bezpieczny wynik na podstawie nich, wynik zalecenia pochodzi z jego ważności i najlepszych rozwiązań w zakresie zabezpieczeń, które będą miały największy wpływ na bezpieczeństwo obciążenia.

Usługa Security Center zapewnia również **ogólny wynik secure.** 

**Ogólny bezpieczny wynik** to akumulacja wszystkich wyników rekomendacji. Ogólny wynik bezpiecznego wyniku można wyświetlać w różnych subskrypcjach lub grupach zarządzania, w zależności od wybranej opcji. Wynik będzie się różnić w zależności od wybranej subskrypcji i aktywnych zaleceń dotyczących tych subskrypcji.

Aby sprawdzić, które zalecenia mają największy wpływ na bezpieczny wynik, możesz wyświetlić trzy najważniejsze zalecenia na pulpicie nawigacyjnym usługi Security Center lub posortować zalecenia w bloku listy rekomendacji przy użyciu kolumny **Wpływ bezpiecznego wyniku.**

Aby wyświetlić ogólny wynik secure:

1. Na pulpicie nawigacyjnym platformy Azure kliknij pozycję **Centrum zabezpieczeń,** a następnie kliknij pozycję **Bezpieczny wynik**.

2. U góry można zobaczyć najważniejsze informacje o bezpiecznym wyniku:
   - **Ogólny wynik secure** reprezentuje wynik na zasady, na wybraną subskrypcję
   - **Bezpieczny wynik według kategorii** pokazuje, które zasoby wymagają największej uwagi
   - **Najważniejsze rekomendacje dotyczące wpływu bezpiecznego wyniku** zawiera listę zaleceń, które poprawią twój Bezpieczny wynik najbardziej, jeśli je zaimplementujesz.
 
   ![Bezpieczny wynik](./media/security-center-secure-score/secure-score-dashboard.png)

3. W poniższej tabeli możesz zobaczyć każdą z subskrypcji i ogólny bezpieczny wynik dla każdego z nich.

   > [!NOTE]
   > Suma bezpiecznego wyniku każdej subskrypcji nie jest równa ogólnemu Wynikowi secure. Bezpieczny wynik to obliczenie oparte na stosunku między zdrowymi zasobami a całkowitymi zasobami na zalecenie, a nie sumą bezpiecznych punktów w ramach subskrypcji. 
   >
4. Kliknij **pozycję Wyświetl rekomendacje,** aby wyświetlić zalecenia dotyczące tej subskrypcji, które można skorygować, aby poprawić bezpieczny wynik.
4. Na liście zaleceń widać, że dla każdego zalecenia znajduje się kolumna reprezentująca **wpływ Bezpiecznego wyniku**. Ta liczba reprezentuje, jak bardzo ogólny wynik secure poprawi, jeśli zastosujesz się do zaleceń. Na przykład na poniższym ekranie, jeśli **naprawisz luki w konfiguracjach zabezpieczeń kontenerów,** twój wynik secure wzrośnie o 35 punktów.

   ![Bezpieczny wynik](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Indywidualny bezpieczny wynik

Ponadto, aby wyświetlić poszczególne bezpieczne wyniki, można je znaleźć w poszczególnych bloku rekomendacji.  

**Bezpieczny wynik rekomendacji** to obliczenie oparte na stosunku między zdrowymi zasobami a całkowitymi zasobami. Jeśli liczba zdrowych zasobów jest równa całkowitej liczbie zasobów, otrzymasz maksymalny bezpieczny wynik zalecenia 50. Aby spróbować zbliżyć swój wynik secure score do maksymalnego wyniku, napraw niezdrowe zasoby, postępając zgodnie z zaleceniami.

**Wpływ rekomendacji** informuje, jak bardzo twój bezpieczny wynik poprawia się, jeśli zastosujesz kroki zalecenia. Jeśli na przykład twój bezpieczny wynik wynosi 42, a **wpływ rekomendacji** to +3, wykonanie kroków opisanych w zaleceniu poprawi wynik, aby stać się 45.

Zalecenie pokazuje, na jakie zagrożenia jest narażony na obciążenie, jeśli kroki korygowania nie są podejmowane.

![indywidualna rekomendacja bezpieczny wynik](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak poprawić swoją postawę zabezpieczeń przy użyciu **bezpiecznego wyniku** w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
