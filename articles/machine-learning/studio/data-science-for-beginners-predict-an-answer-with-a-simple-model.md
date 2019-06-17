---
title: Prognozowanie odpowiedzi za pomocą modele regresji
titleSuffix: Azure Machine Learning Studio
description: Jak utworzyć model regresji prosty, aby przewidzieć cenę w zakresie analizy danych dla początkujących 4 wideo. Obejmuje regresji liniowej z danych docelowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 9165e51d07cf97756408c7f73720931abe067bb2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751583"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prognozowanie odpowiedzi za pomocą prostego modelu
## <a name="video-4-data-science-for-beginners-series"></a>Wideo 4: Analiza danych dla początkujących serii
Dowiedz się, jak utworzyć model regresji proste przewidzieć cenę romb w zakresie analizy danych dla początkujących 4 wideo. Firma Microsoft wyciąga modelu regresji przy użyciu danych docelowych.

Aby maksymalnie wykorzystać możliwości serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Przetwarzanie danych dla początkujących* jest szybkie wprowadzenie do nauki o danych w pięciu krótkich filmach wideo.

* Wideo 1: [5 pytań, analiza danych daje odpowiedzi](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [Czy dane są gotowe do analizy danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Wideo 3: [Zadaj pytanie, na które można odpowiedzieć za pomocą danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
* Wideo 4: Prognozowanie odpowiedzi za pomocą prostego modelu
* Wideo 5: [Kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Zapis: Prognozowanie odpowiedzi za pomocą prostego modelu
Witamy w czwartym wideo w "Data Science for Beginners" serii. W tym, co możemy zbudowania prostego modelu i prognozowania.

A *modelu* jest uproszczone artykuł o naszych danych. Czy mogę pokazano, co mam na myśli.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Zbierz odpowiednie, dokładne i połączone, wystarczającej ilości danych
Załóżmy, że chcę sklep romb. Mam pierścień, który należał do mojego ma z ustawieniem dla romb 1.35 daszka i chcę, aby poznać ile będzie kosztować. Przyjmuję do Notatnika i pióra do magazynu Biżuteria i Zapisz I ceny wszystkich karo w przypadku i ile porównać w carats. Począwszy od pierwszego romb - carats 1.01 jego i 7,366 $.

Teraz przejść i zrób to dla wszystkich diamenty w magazynie.

![Liczba kolumn danych romb](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Należy zauważyć, że listy ma dwie kolumny. Każda kolumna ma inny atrybut — waga carats i cena — oraz każdy wiersz jest pojedynczego punktu danych reprezentujący pojedynczego romb.

Faktycznie utworzyliśmy małego zestawu danych w tym miejscu — tabeli. Zwróć uwagę, że spełnia on nasze kryteria jakości:

* Dane są **odpowiednie** -wagi zdecydowanie jest powiązany do ceny
* Ma ona **dokładne** — firma Microsoft double-checked ceny, które firma Microsoft Zapisz
* Ma ona **połączone** — istnieją nie spacji w dowolnej z tych kolumn
* I, zobaczymy, ma ona **wystarczająco dużo** odpowiedź na pytanie naszej

## <a name="ask-a-sharp-question"></a>Zadaj pytanie sharp
Firma Microsoft będzie teraz stanowić naszych pytanie, w sposób sharp: "Ile będzie kosztować kupić romb 1.35 daszka?"

Listy nie ma romb 1.35 daszka, dlatego odpowiemy na potrzeby uzyskania odpowiedzi na pytanie na pozostałe dane.

## <a name="plot-the-existing-data"></a>Istniejące dane wykresu
Pierwszą rzeczą, jaką wykonamy jest rysowanie linii poziomej liczbę, o nazwie osi do wykresu wag. Zakres wag jest 0 do 2, dzięki czemu firma Microsoft będzie narysować linię uwzględniającą zakres i umieścić znaczniki dla każdego połowa daszka.

Następnie firma Microsoft wyciąga osi pionowej rejestrowania ceny i połączyć ją do osi poziomej wagi. Są to w jednostkach dolarów. Teraz mamy zestaw współrzędnych osi.

![Osie wagi i ceny](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Omówimy teraz wykonać tych danych i włączyć go do *wykres punktowy*. Jest to doskonały sposób wizualizować zestawów danych liczbowych.

Dla pierwszego punktu danych Firma Microsoft eyeball pionu podczas 1.01 carats. Następnie możemy eyeball linii poziomej w 7,366 $. Jeżeli spełniają one możemy zwrócić pojedynczego znaku kropki. Reprezentuje naszym pierwszym romb.

Teraz możemy przejść przez każdy rombu na tej liście i zrobić to samo. Gdy będziemy za pośrednictwem to otrzymujemy: wiele kropki, po jednym dla każdego romb.

![Wykres punktowy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rysowanie modelu za pośrednictwem punktów danych
Jeśli spojrzysz na squint i kropek, Kolekcja wygląda teraz następująco linię fat, rozmytego. Możemy pobrać naszych znacznika i narysuj prostej przy jego użyciu.

Za pomocą rysowania linii, utworzyliśmy *modelu*. Myśl o tym jako biorąc rzeczywistych i jego wersji kreskówki uproszczony. Teraz kreskówki jest nieprawidłowy — wiersz nie jest akceptowana przez wszystkie punkty danych. Jednak jest to przydatne uproszczenie.

![Regresji liniowej](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Fakt, że wszystkie kropki nie są kierowane dokładnie za pośrednictwem wiersza jest OK. Naukowcy wyjaśnić to przez informujący o tym, że istnieje model — jest to wiersz — i następnie każdej kropce ma kilka *szumu* lub *wariancji* skojarzonych z nim. Istnieje relacja podstawowej doskonałe i ma niepowtarzalne świata rzeczywistego, dodającego hałasu i niepewność.

Ponieważ chcemy znaleźć odpowiedź na pytanie *ile?* jest to nazywane *regresji*. A ponieważ używamy linię prostą jest *regresji liniowej*.

## <a name="use-the-model-to-find-the-answer"></a>Użyj modelu, aby znaleźć odpowiedzi
Teraz mamy modelu, a firma Microsoft Zadaj pytanie naszej: Ile będzie kosztować romb 1.35 daszka

Odpowiedź z naszej pytanie, firma Microsoft oka 1.35 carats i rysowania pionowych linii. Gdzie go przecięcie wiersza modelu, firma Microsoft eyeball linii poziomej do osi dolara. Trafienia w zasięgu 10 000. Nagle! To znaczy odpowiedzi: Koszty romb 1.35 daszka o 10 000 USD.

![Znajdź odpowiedzi na podstawie modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Utwórz przedział ufności
To fizyczne, aby dowiedzieć się, jak dokładne jest to prognozy. Jest grupowaniu można sprawdzić, czy romb 1.35 daszka będzie bardzo blisko 10 000 USD, lub o wiele wyższą lub niższą. Aby znaleźć tę możliwość, Narysujmy koperty wokół regresji, który zawiera większość punktów. Nazywa się to koperty naszych *interwał ufności*: Jesteśmy dość pewność, że ceny mieszczą się w tym kopercie, ponieważ w ciągu ostatnich większość z nich. Firma Microsoft można narysować dwa więcej poziome linie z miejsca przecięcia wiersza 1.35 daszka, u góry i u dołu tego schematu envelope.

![Interwał ufności](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Teraz możemy coś, co mówią o naszych interwał ufności:  Będzie można powiedzieć bez obaw, cena romb 1.35 daszka jest około $ 10 000 -, ale może być możliwie jak $8000 i może być możliwie jak 12 000.

## <a name="were-done-with-no-math-or-computers"></a>Firma Microsoft będzie gotowe, bez matematyczne lub komputerów
Przeprowadziliśmy Zarabiaj, jakie analitykom danych wykonaj, a następnie Robiliśmy to po prostu za pomocą rysowania:

* Firma Microsoft zadał pytanie firma Microsoft może odpowiedzieć za pomocą danych
* Utworzyliśmy *modelu* przy użyciu *regresji liniowej*
* Wprowadziliśmy *prognozowania*, wraz z *interwał ufności*

I użyliśmy matematyczne lub komputerów, aby to zrobić.

Teraz, gdyby miał więcej informacji, takie jak...

* Wytnij rombu
* kolory (jak blisko rombu jest on biały)
* Liczba dołączenia w romb

.. .i firma Microsoft musiałaby większą liczbę kolumn. W takim przypadku matematyczne staje się przydatne. Jeśli masz więcej niż dwie kolumny, jest trudny do rysowania kropki na papierze. Obliczenia umożliwia bardzo dobrze nadające się tego wiersza lub że płaszczyzny danych.

Ponadto jeśli zamiast tylko niewielki podzbiór diamenty, mieliśmy dwa tysiące lub miliony dwa, a następnie wykonaj pracę znacznie szybciej z komputerem.

Obecnie mówiliśmy o tym, jak zrobić regresji liniowej i wprowadziliśmy prognozowania, przy użyciu danych.

Pamiętaj sprawdzić inne filmy wideo "Dane do analizy dla początkujących" z usługi Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszy eksperyment dotyczący przetwarzania danych, za pomocą usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do usługi Machine Learning na Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
