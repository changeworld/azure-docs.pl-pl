---
title: Zadaj pytanie dane mogą odpowiedzieć
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak sformułować ostre pytanie do nauki o danych w wideo 3 data science dla początkujących. Zawiera porównanie klasyfikacji i regresji pytania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838839"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Zadawanie pytań, na które można odpowiedzieć za pomocą danych
## <a name="video-3-data-science-for-beginners-series"></a>Wideo 3: Data Science dla początkujących serii
Dowiedz się, jak sformułować problem nauki o danych w pytaniu w wideo 3 data science dla początkujących. Ten film wideo zawiera porównanie pytań dotyczących algorytmów klasyfikacji i regresji.

Aby w pełni wykorzystać wyniki serii, obejrzyj je wszystkie. [Przejdź do listy filmów](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy z tej serii
*Data Science dla początkujących* to krótkie wprowadzenie do nauki o danych w pięciu krótkich filmach.

* Film 1: [Odpowiedzi na dane z 5 pytań](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Film 2: [Czy Twoje dane są gotowe do nauki o danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Film 3: Zadaj pytanie, na które możesz odpowiedzieć danymi
* Wideo 4: [Przewidywanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [Kopiowanie pracy innych osób w celu nauki o danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transkrypcja: Zadaj pytanie, na które możesz odpowiedzieć danymi
Witamy w trzecim filmie z serii "Data Science dla początkujących".  

W tym jednym, otrzymasz kilka wskazówek dotyczących formułowania pytanie, na które można odpowiedzieć za pomocą danych.

Możesz uzyskać więcej z tego filmu, jeśli po raz pierwszy obejrzeć dwa wcześniejsze filmy z tej serii: "5 pytań data science może odpowiedzieć" i "Czy twoje dane są gotowe do nauki o danych?"

## <a name="ask-a-sharp-question"></a>Zadaj ostre pytanie
Rozmawialiśmy o tym, jak nauka o danych jest procesem używania nazw (nazywanych również kategoriami lub etykietami) i liczb do przewidywania odpowiedzi na pytanie. Ale to nie może być tylko żadne pytanie; to musi być *ostre pytanie.*

Niejasne pytanie nie musi być odpowiedzią na nazwisko lub numer. Ostre pytanie musi.

Wyobraź sobie, że znalazłeś magiczną lampę z dżinem, który zgodnie z prawdą odpowie na każde pytanie, które zadajesz. Ale to złośliwy dżin, który postara się, aby ich odpowiedź była tak niejasna i myląca, jak mogą uciec. Chcesz przypiąć je z pytaniem tak hermetycznym, że nie mogą pomóc, ale powiedzieć, co chcesz wiedzieć.

Jeśli miałbyś zadać niejasne pytanie, takie jak "Co się stanie z moim zapasem?", dżin może odpowiedzieć: "Cena się zmieni". To prawdziwa odpowiedź, ale nie jest zbyt pomocna.

Ale jeśli miałbyś zadać ostre pytanie, takie jak "Jaka będzie cena sprzedaży moich akcji w przyszłym tygodniu?", dżin nie może pomóc, ale daje konkretną odpowiedź i przewidzieć cenę sprzedaży.

## <a name="examples-of-your-answer-target-data"></a>Przykłady odpowiedzi: dane docelowe
Po sformułowaniu pytania sprawdź, czy w danych są przykłady odpowiedzi.

Jeśli nasze pytanie brzmi: "Jaka będzie cena sprzedaży moich akcji w przyszłym tygodniu?" następnie musimy upewnić się, że nasze dane obejmują historię cen akcji.

Jeśli nasze pytanie brzmi: "Który samochód w mojej flocie zawiedzie w pierwszej kolejności?" następnie musimy upewnić się, że nasze dane zawierają informacje o poprzednich awariach.

![Dane docelowe - przykłady twojej odpowiedzi. Sformułowanie pytania do nauki o danych.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Te przykłady odpowiedzi są nazywane celem. Cel jest tym, co staramy się przewidzieć na temat przyszłych punktów danych, niezależnie od tego, czy jest to kategoria, czy liczba.

Jeśli nie masz żadnych danych docelowych, musisz je uzyskać. Bez niego nie będziesz w stanie odpowiedzieć na twoje pytanie.

## <a name="reformulate-your-question"></a>Przeformułuj swoje pytanie
Czasami możesz przeformułować pytanie, aby uzyskać bardziej użyteczną odpowiedź.

Pytanie "Czy ten punkt danych A czy B?" przewiduje kategorię (lub nazwę lub etykietę) czegoś. Aby odpowiedzieć, używamy *algorytmu klasyfikacji*.

Pytanie "Ile?" lub "Ile?" przewiduje kwotę. Aby na nie odpowiedzieć, używamy *algorytmu regresji.*

Aby zobaczyć, jak możemy je przekształcić, spójrzmy na pytanie: "Która historia wiadomości jest najbardziej interesująca dla tego czytelnika?" Prosi o przewidywanie jednego wyboru z wielu możliwości - innymi słowy "Czy to A czy B, C czy D?" - i będzie korzystać z algorytmu klasyfikacji.

Ale to pytanie może być łatwiejsze do odpowiedzi, jeśli przeredagować go jako "Jak interesująca jest każda historia na tej liście do tego czytelnika?" Teraz możesz nadać każdemu artykułowi wynik liczbowy, a następnie łatwo jest zidentyfikować artykuł o najwyższej punktacji. Jest to przeformułowanie pytania klasyfikacji do pytania regresji lub Ile?

![Przeformułuj swoje pytanie. Pytanie klasyfikacji a pytanie regresji.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak zadać pytanie jest wskazówką, na które algorytm może dać odpowiedź.

Przekonasz się, że niektóre rodziny algorytmów - jak te w naszym przykładzie news story - są ze sobą ściśle powiązane. Można przeformułować pytanie, aby użyć algorytmu, który daje najbardziej użyteczną odpowiedź.

Ale, co najważniejsze, zadaj to ostre pytanie - pytanie, na które możesz odpowiedzieć danymi. I upewnij się, że masz odpowiednie dane, aby na nie odpowiedzieć.

Rozmawialiśmy o kilku podstawowych zasadach zadawania pytań, na które możesz odpowiedzieć danymi.

Pamiętaj, aby sprawdzić inne klipy wideo w "Data Science dla początkujących" z Microsoft Azure Machine Learning Studio (classic).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego na platformie Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
