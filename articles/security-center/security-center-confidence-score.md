---
title: Wskaźnik zaufania w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Usługa Security Center generuje wyniki zaufania, aby pomóc zespołowi określić, czy zagrożenie jest uzasadnione i jak klasyfikować i ustalać priorytety alertów.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604387"
---
# <a name="alert-confidence-score-preview"></a>Wynik zaufania alertu (wersja zapoznawcza)

Usługa Azure Security Center zapewnia wgląd w zasoby uruchamiane na platformie Azure i ostrzega, gdy wykryje potencjalne problemy. Ilość alertów może być trudne dla zespołu operacji zabezpieczeń do indywidualnego adresu i staje się konieczne, aby ustalić priorytety, które alerty do zbadania. Badanie alertów może być skomplikowane i czasochłonne, w wyniku czego niektóre alerty są ignorowane.

Wynik zaufania (obecnie w wersji zapoznawczej) w u centrum zabezpieczeń może pomóc w klasyfikowaniu zespołu i ustalaniu priorytetów alertów. Usługa Security Center automatycznie stosuje najlepsze praktyki branżowe, inteligentne algorytmy i procesy używane przez analityków do określenia, czy zagrożenie jest uzasadnione, i zapewnia znaczące spostrzeżenia w postaci wyniku zaufania.

## <a name="how-the-confidence-score-is-triggered"></a>Jak wyzwalany jest wynik zaufania

Alerty są generowane po wykryciu podejrzanych procesów uruchomionych na maszynach wirtualnych. Usługa Security Center przegląda i analizuje te alerty na maszynach wirtualnych systemu Windows uruchomionych na platformie Azure. Przeprowadza automatyczne kontrole i korelacje przy użyciu zaawansowanych algorytmów w wielu jednostkach i źródłach danych w całej organizacji oraz wszystkich zasobach platformy Azure i prezentuje się z wynikiem zaufania, który jest miarą pewności, jak pewny jest Usługa Security Center. że wpis jest prawdziwy i należy go zbadać.

## <a name="understanding-the-confidence-score"></a>Zrozumienie wyniku zaufania

Wynik zaufania waha się od 1 do 100 i reprezentuje zaufanie Centrum zabezpieczeń ma, że alert musi być zbadane. Im wyższy wynik, tym większa pewność, że usługa Security Center oznacza, że alert wskazuje na prawdziwą szkodliwą aktywność. Wynik zaufania zawiera listę najważniejszych powodów, dla których alert otrzymał swój wynik zaufania. Wskaźnik zaufania ułatwia analitykom bezpieczeństwa priorytetowe traktowanie odpowiedzi na alerty i zajęcie się najpierw najpilniejszymi atakami, co ostatecznie skraca czas potrzebny na reagowanie na ataki i naruszenia.

Aby wyświetlić wynik zaufania:
- W portalu Centrum zabezpieczeń otwórz bloku Alerty zabezpieczeń.
-  Alerty i zdarzenia są zorganizowane od najwyższego do najniższego, co oznacza, że bardziej pewny siebie Centrum zabezpieczeń jest to, że alert reprezentuje zagrożenie, tym bliżej górnej części strony. 


 ![Współczynnik ufności][1]

Aby wyświetlić dane, które przyczyniły się do zaufania usługi Security Center do alertu:
- W obszarze Ufność w obszarze **Zaufanie**wyświetl obserwacje, które przyczyniły się do wyniku zaufania i uzyskaj szczegółowe informacje związane z alertem. Zapewnia to lepszy wgląd w charakter działań, które spowodowały alert.

  ![Podejrzany wynik zaufania][2]

Użyj wskaźnika zaufania centrum zabezpieczeń, aby nadać priorytet klasyfikacji alertów w twoim środowisku. Wskaźnik zaufania pozwala zaoszczędzić czas i wysiłek, automatycznie badając alerty, stosując najlepsze praktyki branżowe i inteligentne algorytmy oraz działając jako wirtualny analityk, aby określić, które zagrożenia są prawdziwe i gdzie musisz skupić swoją uwagę.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
