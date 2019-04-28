---
title: Zadaj pytanie, na które pozwala uzyskać odpowiedzi na dane
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak sformułować pytanie do nauki o danych sharp przetwarzanie danych dla początkujących 3 wideo. Zawiera porównanie pytania dotyczące klasyfikacji i regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 815aeb9ae6036f08fc87e41980a623e2be8414e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751261"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Zadawanie pytań, na które można odpowiedzieć za pomocą danych
## <a name="video-3-data-science-for-beginners-series"></a>Wideo 3: Analiza danych dla początkujących serii
Dowiedz się, jak sformułować problem do nauki o danych na pytanie na przetwarzanie danych dla początkujących 3 wideo. Ten film zawiera porównanie pytania dotyczące klasyfikacji i regresji algorytmów.

Aby maksymalnie wykorzystać możliwości serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Przetwarzanie danych dla początkujących* jest szybkie wprowadzenie do nauki o danych w pięciu krótkich filmach wideo.

* Wideo 1: [5 pytań, analiza danych daje odpowiedzi](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [Czy dane są gotowe do analizy danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Wideo 3: Zadawanie pytań, na które można odpowiedzieć za pomocą danych
* Wideo 4: [Prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek.)*
* Wideo 5: [Kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Zapis: Zadawanie pytań, na które można odpowiedzieć za pomocą danych
Zapraszamy do zapoznania się trzeci wideo z serii "Przetwarzanie danych dla początkujących."  

W tym obiekcie otrzymasz wskazówki dotyczące opracowywania pytanie, na które można odpowiedzieć za pomocą danych.

Możesz otrzymać więcej poza tym filmie pokazano, jeśli najpierw obejrzeć dwa wideo wcześniej w tej serii: "5 pytań analizy danych pozwala uzyskać odpowiedzi na" lub "Is, Twoje dane są gotowe na analizę danych?"

## <a name="ask-a-sharp-question"></a>Zadaj pytanie sharp
Mówiliśmy o tym, jak do nauki o danych jest proces przewidywanie odpowiedzi na pytanie przy użyciu nazwy (nazywane również kategorii lub etykiety) i cyfr. Ale nie może być po prostu każde pytanie; musi ona być *sharp pytanie.*

Niezrozumiała pytanie nie ma na udzielenie odpowiedzi z nazwą lub liczbą. Musi być sharp pytanie.

Załóżmy, że znaleziono magic lamp przy użyciu genie, kto realistyczne odpowiedzą na każde pytanie, które możesz zadawać pytania. Jednak jest mischievous genie, a użytkownik zostanie podjęta próba kompromis jego odpowiedź jako niejasna i pozostał przejrzysty on uzyskać natychmiast dzięki. Chcesz przypiąć go w dół do pytania, więc hermetyczne nie może on pomóc ale informujące o tym, co chcesz wiedzieć.

Jeśli masz zamiar Zadaj pytanie niejasne, takie jak "Co się dzieje się tak zdarzyć z moich stock?", genie mogą odpowiedzieć, "cena zmieni się". To realistyczne odpowiedzi, ale nie jest to bardzo przydatne.

Ale zostałby Zadaj pytanie sharp, takich jak "Co cena detaliczna Moje zasobów będzie w następnym tygodniu?", genie nie jest jednak umożliwiają określonej odpowiedzi i przewidzieć cenę sprzedaży.

## <a name="examples-of-your-answer-target-data"></a>Przykłady odpowiedzi: Dane docelowego
Po użytkownik sformułować Twoje pytanie, sprawdź, czy masz przykłady odpowiedzi w Twoich danych.

W przypadku naszej pytanie "Co cena detaliczna Moje zasobów będzie w następnym tygodniu?" następnie mamy upewnij się, że nasze dane obejmują historię cena akcji.

W przypadku naszej pytanie "które samochodów w mojej floty zamierza najpierw zakończyć się niepowodzeniem?" następnie mamy upewnij się, że nasze dane zawierają informacje dotyczące wcześniejszych niepowodzeń.

![Dane docelowego — przykłady odpowiedzi. Formułowanie zapytania do nauki o danych.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Te przykłady odpowiedzi są nazywane obiektu docelowego. Obiekt docelowy jest co próbujemy przewidzieć o punktów danych w przyszłości, czy kategoria lub liczbą.

Jeśli nie masz żadnych danych docelowego należy uzyskać niektóre. Nie będzie w stanie udzielić odpowiedzi na pytania bez niego.

## <a name="reformulate-your-question"></a>Sformułować Twoje pytanie
Czasami można adnotacji Twoje pytanie, aby uzyskać bardziej przydatne odpowiedzi.

Pytanie "Jest to data punktu A lub B?" Prognozuje kategorii (lub nazwa lub etykieta) obiektu. Aby Odbierz, użyjemy *algorytm klasyfikacji*.

Pytanie "Ile?" lub "Ile?" Prognozuje kwoty. Aby Odbierz użyjemy *algorytmu regresji*.

Aby zobaczyć, jak firma Microsoft może zmienić je, Spójrzmy na pytanie "która historii wiadomości jest najbardziej interesujących ten czytnik?" Sprawdza, czy do przewidywania pojedynczego wyboru z wielu możliwości — innymi słowy "Jest to A lub B lub C lub D?" - i będzie używać algorytm klasyfikacji.

Ale to pytanie, być może łatwiej będzie odpowiedzieć, jeśli go jako adnotacji "jak interesujące jest każdy wątek na liście, aby ten czytnik?" Teraz możesz nadać poszczególnymi artykułami, wynik liczbowe, a to łatwo identyfikować ich najwyższym oceniania artykułu. Jest to, ponownie sformułować pytanie klasyfikacji do zapytania regresji lub ile?

![Sformułować Twoje pytanie. Pytanie klasyfikacji i regresji pytanie.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak zadać pytanie jest sugeruje algorytmu, który daje odpowiedzi.

Można znaleźć niektórych rodzin algorytmy — np w naszym przykładzie historii wiadomości — są ściśle powiązane. Można sformułować Twoje pytanie, aby używać algorytmu, który zapewnia najbardziej przydatne odpowiedzi.

Jednak najważniejsze Zadaj pytanie, na które można odpowiedzieć za pomocą danych to pytanie sharp -. I upewnij się, że masz odpowiednie dane do odpowiedzi go.

Rozmawialiśmy o pewne podstawowe zasady dotyczące zadawania pytań, że można odpowiedzieć za pomocą danych.

Pamiętaj sprawdzić inne filmy wideo "Dane do analizy dla początkujących" z usługi Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszy eksperyment dotyczący przetwarzania danych, za pomocą usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do usługi Machine Learning na Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
