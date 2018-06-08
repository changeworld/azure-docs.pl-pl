---
title: Poproś danych zapytania mogą odpowiadać - danych nauki problemów — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sformułować pytanie nauki sharp danych w nauce danych dla początkujących 3 wideo. Zawiera porównanie pytania klasyfikacji i regresji.
keywords: dane nauki problemów, pytania dotyczące analizy danych, sformułować pytania, pytania regresji, klasyfikacji pytania, sharp zapytania
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 2078f7e84522a40cf6fcf6dff860d24307084467
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833805"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Zadawanie pytań, na które można odpowiedzieć za pomocą danych
## <a name="video-3-data-science-for-beginners-series"></a>Wideo 3: Nauki danych serii dla początkujących
Dowiedz się, jak sformułować problem nauki danych do zapytania w nauce danych dla początkujących 3 wideo. Ten film zawiera porównanie pytania dotyczące algorytmów klasyfikacji i regresji.

Aby uzyskać wykorzystanie serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne pliki wideo w tej serii
*Nauki danych dla początkujących* jest szybkie wprowadzenie do analizy danych w pięciu krótkie wideo.

* Wideo 1: [danych nauki odpowiedzi na pytania 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [jest gotowy do analizy danych danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 s 56 min)*
* Wideo 3: Zadaj pytanie, które mogą odpowiedzieć z danymi
* Wideo 4: [prognozowania odpowiedzi z modelu prostego](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
* Wideo 5: [skopiuj pracy innych osób nauki danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Zapis: Zadaj pytanie, które mogą odpowiedzieć z danymi
"Nauki danych dla początkujących." trzeci wideo w serii — Zapraszamy!  

W tym przypadku można uzyskać wskazówki dotyczące opracowywania pytanie, które pozwala odpowiedzieć z danymi.

Może zostać wyświetlony więcej poza ten film, jeśli najpierw Obejrzyj dwóch wideo wcześniej w tej serii: "nauki dane 5 pytania mogą odpowiadać" i "Jest danych jest gotowy do analizy danych?"

## <a name="ask-a-sharp-question"></a>Zadaj pytanie sharp
Zajmowaliśmy jak nauki danych to proces przy użyciu nazwy (nazywanych również kategorii lub etykiety) i numery do prognozowania odpowiedzi na pytanie. Ale nie można go do dowolnego zapytania; musi to być *sharp pytanie.*

Niezrozumiała pytanie nie musi odpowiedzieć na nazwę lub cyfrą. Musi być sharp pytanie.

Wyobraź sobie znalezione magic świateł genie, który będzie wyczerpujących odpowiedzi na każde pytanie zapytaniem. Jednak jest mischievous genie i będzie on spróbuj nawiązać jego odpowiedzi jako niezrozumiała i mylące, ponieważ może on uzyskać optymalizacji z. Chcesz przypiąć go w dół do pytania, więc hermetyczne nie może on pomóc ale informujące o tym, co chcesz wiedzieć.

Jeśli masz zamiar Zadaj pytanie niezrozumiała, takie jak "Co się dzieje się tak zdarzyć z mojej giełdowych?", może odpowiedzieć genie, "zmieni cenę". To truthful odpowiedzi, ale nie jest to bardzo przydatne.

Ale jeśli aby zadać pytanie sharp, jak "Co cena sprzedaży Moje giełdowych zostanie w następnym tygodniu?", genie nie może pomóc ale umożliwiają określony odpowiedzi i przewidywanie ceny sprzedaży.

## <a name="examples-of-your-answer-target-data"></a>Przykłady odpowiedzi: dane docelowej
Po sformułować pytanie, sprawdź, czy masz przykłady odpowiedzi w Twoich danych.

W przypadku naszego pytanie "Co cena sprzedaży Moje giełdowych zostanie w następnym tygodniu?" następnie mamy upewnij się, że nasze dane obejmują giełdowy historii.

W przypadku naszego pytanie "samochodów, które w mojej floty ma zakończyć się niepowodzeniem, najpierw?" następnie mamy upewnij się, że nasze dane zawierają informacje dotyczące wcześniejszych niepowodzeń.

![Dane — przykłady odpowiedzi docelowej. Formułowanie zapytania analizy danych.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Te przykłady odpowiedzi są określane jako element docelowy. Element docelowy jest co próbujemy prognozowania o punktów danych w przyszłości, kategorii lub liczbą.

Jeśli nie masz żadnych danych docelowych, należy pobrać niektórych. Nie można odpowiedź na Twoje pytanie bez niego.

## <a name="reformulate-your-question"></a>Sformułować pytanie
Czasami może adnotacji Twoje pytanie, aby uzyskać bardziej użyteczna w odpowiedzi.

Pytanie "Jest to danych punktu A lub B?" Prognozuje kategorii (lub nazwę lub etykiety) elementu. Odpowiada on używamy *algorytm klasyfikacji*.

Pytanie "Ile?" lub "Ile?" Prognozuje kwotę. Odpowiada on używamy *algorytm regresji*.

Aby zobaczyć, jak firma Microsoft można przekształcać je, Przyjrzyjmy się pytanie, "wątku wiadomości, które jest najbardziej interesujące tego czytnika"? Sprawdza, czy dla Prognozowanie pojedynczego wyboru z możliwości wielu — innymi słowy "Jest to A lub B i C lub D"? - i użyje algorytm klasyfikacji.

Jednak to pytanie może być łatwiejsze do odpowiedzi, jeśli go jako adnotacji "jak interesujące jest każdy wątek na liście, aby ten czytnik?" Teraz możesz nadać każdego artykułu numeryczny wynik i jest łatwo zidentyfikować najwyższy oceniania artykułu. To jest ponownie sformułować pytanie klasyfikacji do zapytania regresji lub ile?

![Sformułować pytanie. Pytanie klasyfikacji i regresji pytanie.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak zadać pytanie jest clue, który algorytm można zapewnić odpowiedzi.

Można znaleźć niektórych rodzin algorytmów — takich jak te w naszym przykładzie wątku wiadomości - są ściśle powiązane. Można sformułować pytanie próbę użycia algorytmu, który zapewnia najbardziej przydatne odpowiedzi.

Jednak najważniejsze poproś to pytanie sharp — pytania, na które pozwala odpowiedzieć z danymi. I upewnij się, że masz odpowiednie dane do odpowiedzi go.

Zajmowaliśmy niektórych podstawowych zasad zadać pytanie się, że można odpowiedzi z danymi.

Należy koniecznie zapoznaj się z innych plików wideo "Danych nauki dla początkujących" z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszego eksperymentu analizy danych z usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego w systemie Microsoft Azure](what-is-machine-learning.md)
