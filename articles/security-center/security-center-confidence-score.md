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
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604387"
---
# <a name="alert-confidence-score-preview"></a>Wynik pewności alertu (wersja zapoznawcza)

Azure Security Center zapewnia wgląd w zasoby działające na platformie Azure i ostrzega o wykryciu potencjalnych problemów. Liczba alertów może być wyzwaniem dla zespołu operacji zabezpieczeń na indywidualnym adresie i konieczne jest określenie priorytetów, które alerty należy zbadać. Badanie alertów może być złożone i czasochłonne, a w związku z tym niektóre alerty są ignorowane.

Wyniki pewności (obecnie dostępne w wersji zapoznawczej) w Security Center mogą pomóc zespołowi Klasyfikacja i wyznaczyć priorytety alertów. Security Center automatycznie stosuje najlepsze rozwiązania branżowe, inteligentne algorytmy i procesy używane przez analityków, aby określić, czy zagrożenie jest wiarygodne i zapewnia wgląd w informacje w formie oceny zaufania.

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


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
