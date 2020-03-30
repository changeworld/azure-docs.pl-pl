---
title: Ocena danych
titleSuffix: ML Studio (classic) - Azure
description: Cztery kryteria, które muszą spełniać dane, aby były gotowe do nauki o danych. Ten film wideo ma konkretne przykłady, które pomogą w ocenie podstawowych danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837801"
---
# <a name="is-your-data-ready-for-data-science"></a>Czy Twoje dane są gotowe na analizę danych?
## <a name="video-2-data-science-for-beginners-series"></a>Film 2: Data Science dla początkujących serii
Dowiedz się, jak oceniać dane, aby upewnić się, że spełniają podstawowe kryteria, aby były gotowe do nauki o danych.

Aby w pełni wykorzystać wyniki serii, obejrzyj je wszystkie. [Przejdź do listy filmów](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy z tej serii
*Data Science dla początkujących* to krótkie wprowadzenie do nauki o danych w pięciu krótkich filmach.

* Film 1: [Odpowiedzi na dane z 5 pytań](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Film 2: Czy Twoje dane są gotowe do nauki o danych?
* Wideo 3: [Zadaj pytanie, na które możesz odpowiedzieć danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Wideo 4: [Przewidywanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [Kopiowanie pracy innych osób w celu nauki o danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transkrypcja: Czy twoje dane są gotowe do nauki o danych?
Witamy w "Czy Twoje dane są gotowe do nauki o danych?" drugi film z serii *Data Science dla początkujących*.  

Zanim analityka danych da Ci odpowiedzi, które chcesz, musisz dać mu kilka wysokiej jakości surowców do pracy. Podobnie jak robienie pizzy, im lepsze składniki zaczynasz, tym lepszy produkt końcowy. 

## <a name="criteria-for-data"></a>Kryteria dotyczące danych
W analityki danych istnieją pewne składniki, które muszą być połączone, w tym:

* Odpowiednich
* Połączone
* Dokładne
* Wystarczy pracować z

## <a name="is-your-data-relevant"></a>Czy Twoje dane są istotne?
Więc pierwszy składnik - potrzebujesz danych, które są istotne.

![Odpowiednie dane a nieistotne dane - ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Po lewej stronie, tabela przedstawia poziom alkoholu we krwi siedmiu osób testowanych poza bar Boston, Red Sox mrugnięcia średniej w ostatniej grze, a cena mleka w najbliższym sklepie spożywczym.

To wszystko jest całkowicie uzasadnione dane. To tylko wina jest to, że nie jest istotne. Nie ma oczywistego związku między tymi liczbami. Jeśli ktoś dał ci aktualną cenę mleka i średnią mrugnięcia Red Sox, nie ma sposobu, można odgadnąć ich zawartość alkoholu we krwi.

Spójrzcie teraz na stół po prawej stronie. Tym razem mierzono masę ciała każdej osoby, a także liczbę napojów, które mieli.  Liczby w każdym wierszu są teraz istotne dla siebie. Gdybym dał ci moją masę ciała i liczbę Margaritas, którą miałem, możesz odgadnąć moją zawartość alkoholu we krwi.

## <a name="do-you-have-connected-data"></a>Czy masz podłączone dane?
Kolejnym składnikiem są połączone dane.

![Połączone dane a odłączone dane - kryteria danych, gotowe dane](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Oto kilka istotnych danych na temat jakości hamburgerów: temperatura grilla, waga patty i ocena w lokalnym magazynie żywności. Ale zwróć uwagę na luki w tabeli po lewej stronie.

W większości zestawów danych brakuje niektórych wartości. Często ma takie dziury i są sposoby na ich obejście. Ale jeśli brakuje zbyt wiele, twoje dane zaczynają wyglądać jak szwajcarski ser.

Jeśli spojrzysz na tabelę po lewej stronie, brakuje tak wielu danych, trudno jest wymyślić jakikolwiek związek między temperaturą grilla a wagą patty. W tym przykładzie pokazano odłączone dane.

Tabela po prawej stronie jest jednak pełna i kompletna - przykład połączonych danych.

## <a name="is-your-data-accurate"></a>Czy Twoje dane są dokładne?
Kolejnym składnikiem jest dokładność. Oto cztery cele do trafienia.

![Dokładne dane a niedokładne dane - kryteria danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Spójrz na cel w prawym górnym rogu. Wokół oka byków znajduje się ciasne zgrupowanie. To, oczywiście, jest dokładne. Co dziwne, w języku nauki o danych wydajność na stronie docelowej tuż poniżej jest również uważana za dokładną.

Jeśli nakreśliłeś środek tych strzałek, zobaczysz, że jest bardzo blisko oka byków. Strzałki są rozłożone wokół celu, więc są uważane za nieprecyzyjne, ale są wyśrodkowane wokół oka byków, więc są uważane za dokładne.

Spójrzmy teraz na lewy górny cel. Tutaj strzały uderzył bardzo blisko siebie, ciasne zgrupowanie. Są precyzyjne, ale są niedokładne, ponieważ centrum jest daleko od oka byków. Strzałki w lewym dolnym celu są niedokładne i nieprecyzyjne. Ten łucznik potrzebuje więcej praktyki.

## <a name="do-you-have-enough-data-to-work-with"></a>Czy masz wystarczająco dużo danych do pracy?
Wreszcie, składnik #4 jest wystarczające dane.

![Czy masz wystarczająco dużo danych do analizy? Ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Każdy punkt danych w tabeli należy potraktować jako pociągnięcie pędzlem w obrazie. Jeśli masz tylko kilka z nich, obraz może być rozmyty - trudno powiedzieć, co to jest.

Jeśli dodasz kilka pociągnięć pędzla, obraz zacznie być nieco ostrzejszy.

Kiedy masz ledwie wystarczająco dużo uderzeń, widzisz tylko tyle, aby podjąć pewne szerokie decyzje. Czy jest to miejsce, w których mógłbym chcieć odwiedzić? Wygląda jasno, że wygląda jak czysta woda - tak, to gdzie jadę na wakacje.

W miarę dodawania większej ilości danych obraz staje się jaśniejszy i można podejmować bardziej szczegółowe decyzje. Teraz możesz spojrzeć na trzy hotele na lewym brzegu. Można zauważyć cechy architektoniczne jednego na pierwszym planie. Możesz nawet zdecydować się na pobyt na trzecim piętrze ze względu na widok.

Dzięki danym, które są istotne, połączone, dokładne i wystarczające, masz wszystkie składniki potrzebne do nauki o danych wysokiej jakości.

Zapoznaj się z pozostałymi czterema klipami wideo w *witrynie Data Science dla początkujących* z usługi Microsoft Azure Machine Learning Studio (classic).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego na platformie Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
