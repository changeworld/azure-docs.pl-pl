---
title: Ocena danych
titleSuffix: Azure Machine Learning Studio
description: Cztery kryteria dane muszą spełnić, aby przystąpić do analizy danych. To wideo zawiera konkretne przykłady ułatwiające wykonywanie podstawowych danych oceny.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 1d9475b49b3ef8a2e672e1ce505a7d94d19cb14f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875323"
---
# <a name="is-your-data-ready-for-data-science"></a>Czy Twoje dane są gotowe na analizę danych?
## <a name="video-2-data-science-for-beginners-series"></a>Wideo 2: Analiza danych dla początkujących serii
Dowiedz się, jak do analizowania danych w taki sposób, aby upewnić się, że spełnia ono kryteriów podstawowa jest gotowy do nauki o danych.

Aby maksymalnie wykorzystać możliwości serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Przetwarzanie danych dla początkujących* jest szybkie wprowadzenie do nauki o danych w pięciu krótkich filmach wideo.

* Wideo 1: [5 odpowiedzi na pytania naukowe dotyczące danych](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: Czy Twoje dane są gotowe na analizę danych?
* Wideo 3: [Zadaj pytanie, na które można odpowiedzieć za pomocą danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
* Wideo 4: [Prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [Kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minuty 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Zapisy Czy Twoje dane są gotowe na analizę danych?
"Czy dane są gotowe do analizy danych?" — Zapraszamy! drugi wideo z serii *przetwarzanie danych dla początkujących*.  

Przed analiza danych daje odpowiedzi, który ma, należy nadać mu niektóre surowce wysokiej jakości do pracy z. Podobnie jak tworzenie głosi, tym lepsze składników, który jest uruchamiany z tym lepsze produktu końcowego. 

## <a name="criteria-for-data"></a>Kryteria dla danych
W zakresie analizy danych istnieją niektóre składniki, które muszą zostać pobrane ze sobą w tym:

* Istotne
* Połączono
* Dokładne
* Wystarczająco dużo, aby pracować z

## <a name="is-your-data-relevant"></a>Czy Twoje dane są istotne?
Dlatego pierwszy składnik — należy danych, która jest odpowiednia.

![Odpowiednie dane, a dane nie ma znaczenia — ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Po lewej stronie w tabeli przedstawiono poziom alkoholu krwi siedem osób przetestowane poza pasek Boston, średnia batting Red Sox w ich ostatnich gry i cena mleka w najbliższej sklepie.

Jest to wszystkie dane bez zarzutu uzasadnione. Błąd tylko jego jest, że nie jest istotne. Nie ma widocznych relacji między tymi liczbami. Jeśli osoba udostępniła Ci bieżąca cena mleka oraz średnią batting Sox czerwony, nie ma możliwości można odgadnąć ich zawartość alkoholu krwi.

Teraz sprawdźmy tabeli po prawej stronie. Tym razem treści każda osoba urządzeń pamięci masowej został zmierzony, a także liczbę w przeddzień one wystąpiły.  Liczby w każdym wierszu są teraz odpowiednie do siebie nawzajem. Jeśli I udostępniła Ci Moje treści urządzeń pamięci masowej oraz liczby Margaritas miałem, można dokonać przewidywanie Moje krwi alkoholu zawartości.

## <a name="do-you-have-connected-data"></a>Czy nawiązano połączenie danych?
Następny składnik jest połączonych danych.

![Połączenia danych, a odłączonego dane — kryteria danych gotowe](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Poniżej przedstawiono niektóre istotne dane dotyczące jakości hamburgers: gril temperatury, waga patty i oceny w lokalnym żywności magazine. Zauważmy jednak luki w tabeli po lewej stronie.

Większość zestawów danych brakuje niektórych wartości. Jest często mają luk w następujący sposób i sposoby ich obejścia. Ale jeśli istnieje zbyt wiele Brak, dane zaczynają się wyglądać Swiss miejscowych serów.

W przypadku tabeli po lewej stronie, istnieje tak wiele brakuje danych, trudno jest opracowywane dowolnego rodzaju relację między rusztem temperatury i patty wagi. Ten przykład przedstawia danych odłączonych.

Tabela po prawej stronie, jednak jest zapełniony i ukończenia — przykład połączonych danych.

## <a name="is-your-data-accurate"></a>Czy Twoje dane są dokładne?
Następny składnik jest dokładności. Poniżej przedstawiono cztery elementy docelowe, aby trafić.

![Dokładne dane, a niedokładne dane — kryteria danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Spójrz na obiekt docelowy w prawym górnym rogu. Brak ścisłej grupowania bezpośrednio wokół oka byków. Oczywiście, są dokładne. Dziwnie danego języka do nauki o danych wydajności po prawej stronie docelowej poniżej jest również uważana za dokładne.

W przypadku zamapowany się na środku tych strzałek widział się bardzo blisko oka byków. Strzałki są rozłożyć wszystkie wokół obiektu docelowego, aby były uważane za nieprecyzyjny, ale one wyśrodkowany wokół byków oka, aby były uważane dokładne.

Teraz sprawdźmy lewy górny element docelowy. W tym miejscu strzałki bardzo blisko siebie trafień ścisłej grupowania. Są one dokładne, ale są one niedokładne, ponieważ Centrum jest sposób wyłączyć oka byków. Strzałki w lewym dolnym docelowej są niedokładne i niedokładna. Ten archer wymaga więcej rozwiązaniem.

## <a name="do-you-have-enough-data-to-work-with"></a>Czy masz wystarczającą ilość danych do pracy?
Na koniec składnik #4 jest wystarczających danych.

![Czy masz wystarczającą ilość danych na potrzeby analizy Ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pomyśl o każdego punktu danych w tabeli jako pędzla, w stolik. Jeśli masz tylko kilka z nich, rysowania może być niewyraźny — trudno stwierdzić, co to jest.

Jeśli dodasz kilka więcej pędzlem, Twoje malowania rozpoczyna się uzyskać nieco ostrzejsze.

W przypadku ledwie wystarczająco dużo pociągnięć widoczne tylko wystarczająco dużo, aby niektóre decyzje dotyczące szerokiego. Jest innym miejscu, które mogę odwiedzić? Wygląda na to jasne, to wygląda czystej wody — tak, który jest, gdzie użyję na urlopie.

W miarę dodawania większej ilości danych obraz staje się bardziej zrozumiały, i uzyskać bardziej szczegółowy decyzje. Teraz możesz obejrzeć trzy hoteli na bank po lewej stronie. Można zauważyć funkcji architektury jeden na pierwszym planie. Możesz nawet wybrać pozostanie w trzecim floor ze względu na widok.

Z danymi, które są istotne, połączone, dokładne i wystarczająco, możesz mieć wszystkie składniki wymagane do wykonania niektórych nauki o danych wysokiej jakości.

Zapoznaj się z innymi czterema filmami wideo w *nauce danych dla początkujących* z Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Następne kroki
* [Spróbuj pierwszy eksperyment dotyczący przetwarzania danych, za pomocą usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do usługi Machine Learning na Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
