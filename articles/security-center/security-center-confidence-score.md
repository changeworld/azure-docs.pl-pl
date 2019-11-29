---
title: Wynik pewności w Azure Security Center | Microsoft Docs
description: Security Center generuje oceny zaufania, aby pomóc zespołowi określić, czy zagrożenie jest wiarygodne i jak Klasyfikacja i ustalać priorytety alertów.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 5b7e53291e10db17f848553b17dae2f84bdb08b7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558977"
---
# <a name="alert-confidence-score"></a>Współczynnik ufności alertu 

Azure Security Center zapewnia wgląd w zasoby działające na platformie Azure i ostrzega o wykryciu potencjalnych problemów. Liczba alertów może być wyzwaniem dla zespołu operacji zabezpieczeń na indywidualnym adresie i konieczne jest określenie priorytetów, które alerty należy zbadać. Badanie alertów może być złożone i czasochłonne, a w związku z tym niektóre alerty są ignorowane.

Wynik zaufania w Security Center może pomóc zespołowi Klasyfikacja i wyznaczyć priorytety alertów. Security Center automatycznie stosuje najlepsze rozwiązania branżowe, inteligentne algorytmy i procesy używane przez analityków, aby określić, czy zagrożenie jest wiarygodne i zapewnia wgląd w informacje w formie oceny zaufania.

## <a name="how-the-confidence-score-is-triggered"></a>Jak jest wyzwalany wynik zaufania

Alerty są generowane po wykryciu podejrzanych procesów uruchomionych na maszynach wirtualnych. Security Center Recenzje i analizuje te alerty na maszynach wirtualnych z systemem Windows uruchomionych na platformie Azure. Wykonuje automatyczne sprawdzanie i korelacje przy użyciu zaawansowanych algorytmów w wielu jednostkach i źródłach danych w całej organizacji, a także wszystkich zasobów platformy Azure i prezentuje wynik zaufania, który jest miarą tego, jak pewność Security Center alert jest autentyczny i należy go zbadać.

## <a name="understanding-the-confidence-score"></a>Zrozumienie wyniku pewności

Wynik zaufania obejmuje zakres od 1 do 100 i reprezentuje Security Center pewności, że należy zbadać alert. Im wyższy wynik, tym bardziej niezawodna Security Center polega na tym, że alert wskazuje na oryginalne złośliwe działanie. Wynik pewności zawiera listę najważniejszych przyczyn, dla których alert otrzymał swój wynik zaufania. Wynik pewności ułatwia analitykom zabezpieczeń określanie priorytetów odpowiedzi na alerty i rozwiązywanie pierwszych ataków, a ostatecznie skrócenie czasu reakcji na ataki i naruszenia.

Aby wyświetlić wynik pewności:
- W portalu Security Center Otwórz blok alerty zabezpieczeń.
-  Alerty i zdarzenia są zorganizowane od najwyższego do najniższego, co oznacza, że jest to bardziej przekonane Security Center polegające na tym, że alert reprezentuje zagrożenie, tym bliżej w górnej części strony. 


 ![Współczynnik ufności][1]

Aby wyświetlić dane, które przyczyniają się do Security Center zaufania w alercie:
- W bloku alertu zabezpieczeń, w obszarze **zaufanie**, przejrzyj uwagi, które przyczyniają się do oceny pewności, i uzyskaj szczegółowe informacje dotyczące alertu. Zapewnia to dokładniejszy wgląd w charakter działań, które spowodowały alert.

  ![Wynik podejrzanej pewności][2]

Użyj Security Centerego wyniku pewności, aby określić priorytety klasyfikacjai alertów w danym środowisku. Wynik zaufania umożliwia zaoszczędzenie czasu i wysiłku przez automatyczne badanie alertów, stosowanie najlepszych rozwiązań branżowych i inteligentnych algorytmów, a także działanie jako analityków wirtualnych w celu ustalenia, które zagrożenia są prawdziwe i w jakich sytuacjach należy skoncentrować uwagę.


## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak użyć wyniku pewności, aby określić priorytety badania alertów. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
