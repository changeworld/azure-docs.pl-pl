---
title: Współczynnik ufności w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak pracować z oceną zaufania usługi Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916596"
---
# <a name="alert-confidence-score"></a>Współczynnik ufności alertu 

Usługa Azure Security Center zapewnia wgląd w zasoby, których uruchamianie na platformie Azure, a ostrzega, gdy wykryje potencjalne problemy. Liczby alertów może być wyzwaniem dla zespołu operacji zabezpieczeń do indywidualnego adresowania i staje się niezbędne do określenia priorytetów, który umożliwia generowanie alertów do badania. Badanie alertów mogą być złożone i czasochłonne, a w rezultacie niektóre alerty są ignorowane.

Współczynnik ufności w usłudze Security Center może pomóc w klasyfikacji Twojego zespołu i ustalanie priorytetu alertów. Usługa Security Center automatycznie stosuje najlepsze rozwiązania w branży, inteligentne algorytmy i procesy używane przez analityków, aby określić, czy zagrożenie jest uzasadnione i udostępniają Ci istotne dane w postaci współczynnik ufności.

## <a name="how-the-confidence-score-is-triggered"></a>Jak jest wyzwalany, współczynnik ufności

Alerty są generowane w przypadku wykrycia podejrzanych procesów uruchomionych na maszynach wirtualnych. Usługa Security Center sprawdza i analizuje te alerty na maszynach wirtualnych Windows działających na platformie Azure. Sprawdza zautomatyzowane i korelacji, przy użyciu zaawansowanych algorytmów w wielu jednostek i źródeł danych w organizacji, a także wszystkich zasobów platformy Azure i przedstawia bez obaw, których wynik, który jest miarą pewność, jak usługa Security Center czy alert jest oryginalna i należy zbadać.

## <a name="understanding-the-confidence-score"></a>Opis współczynnik ufności

Współczynnik ufności reprezentuje pewność, której usługa Security Center ma alert musi zostać zbadana i zakresu od 1 do 100. Im większa jest liczba punktów, większa pewność Centrum zabezpieczeń jest, że ten alert wskazuje oryginalnego złośliwych działań. Współczynnik ufności zawiera listę głównych powodów dlaczego alert Odebrano jego współczynnik ufności. Współczynnik ufności ułatwia analityków zabezpieczeń określić priorytety ich odpowiedzi na alerty i rozwiązać większość najpierw naciskając ataków, ostatecznie skrócenie czasu potrzebnego na ataków i naruszeń.

Aby wyświetlić współczynnik ufności:
- Otwórz blok alerty zabezpieczeń w portalu usługi Security Center.
-  Alerty i zdarzenia są uporządkowane od najwyższego do najniższego, co oznacza, że większa pewność Centrum zabezpieczeń jest, że alert stanowi zagrożenie, im bliżej stanowi on górnej części strony. 


 ![Współczynnik ufności][1]

Aby wyświetlić dane, które przyczyniły się do Centrum zabezpieczeń zaufania alertu:
- W zabezpieczeniach alert bloku, w obszarze **ufności**, wyświetlanie uwag, które przyczyniły się do współczynnik ufności oraz uzyskiwanie szczegółowych informacji związanych z alertem. Zapewnia lepszy wgląd w rodzaju działań, które spowodował wygenerowanie alertu.

  ![Współczynnik ufności podejrzanych][2]

Współczynnik ufności usługa Security Center, aby określić priorytety alertu klasyfikacji w danym środowisku. Współczynnik ufności oszczędza czas i wysiłek automatycznie badania alertów, stosując najlepsze rozwiązania w branży i inteligentnych algorytmów i działający jako wirtualnego analityka w celu ustalenia, które zagrożenia są na rzeczywistych i wymagających koncentrować.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule wyjaśniono, jak określić priorytet badania alertów za pomocą współczynnik ufności. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
