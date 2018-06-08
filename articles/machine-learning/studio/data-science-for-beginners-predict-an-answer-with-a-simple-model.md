---
title: Przewidywanie odpowiedzi z modelu regresji proste — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak utworzyć model regresji proste do prognozowania cen w nauce danych dla początkujących 4 wideo. Obejmuje regresji liniowej z danych docelowych.
keywords: Tworzenie modelu, modelu prostego, prognozowanie cen, model regresji proste
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: ad1b8369358f7811a02d344fdc0306662413a404
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833829"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prognozowanie odpowiedzi za pomocą prostego modelu
## <a name="video-4-data-science-for-beginners-series"></a>Wideo 4: Nauki danych serii dla początkujących
Dowiedz się, jak utworzyć model regresji proste do prognozowania cen romb w nauce danych dla początkujących 4 wideo. Firma Microsoft będzie Rysuj modelu regresji z danych docelowych.

Aby uzyskać wykorzystanie serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne pliki wideo w tej serii
*Nauki danych dla początkujących* jest szybkie wprowadzenie do analizy danych w pięciu krótkie wideo.

* Wideo 1: [danych nauki odpowiedzi na pytania 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [jest gotowy do analizy danych danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 s 56 min)*
* Wideo 3: [Zadaj pytanie może odpowiedzieć z danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 s 17 min)*
* Wideo 4: Odpowiedź z prostego modelu prognozowania
* Wideo 5: [skopiuj pracy innych osób nauki danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Zapis: Przewidywanie odpowiedzi z modelu prostego
Witamy w czwartym wideo w "danych nauki dla początkujących" serii. W tym przypadku firma Microsoft budowanie prostego modelu i prognozowanie.

A *modelu* jest uproszczone artykuł o naszych danych. Będzie można wyświetlić I znaczenie.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Zbieranie odpowiednich, dokładne, połączenie, jest za mało danych
Załóżmy, że chcę sklep romb. Mam pierścień, który należał do mojego babcia z ustawieniem dla romb 1.35 karatach i chcę uzyskać informacje o tym, jaki będzie koszt. Muszę zrobić Notatnik i pióra do magazynu Biżuteria i I Zanotuj cen wszystkich diamentów w przypadku i ile porównać w carats. Począwszy od pierwszego romb - carats 1.01 jego i 7,366 $.

Teraz przejdź i zrób to inne diamentowego w magazynie.

![Kolumny danych romb](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Zwróć uwagę, że naszej listy ma dwie kolumny. Każda kolumna ma inny atrybut — wagę carats i cen — oraz każdy wiersz jest pojedynczego punktu danych reprezentujący pojedynczego romb.

Faktycznie utworzyliśmy małą tutaj — zestawu danych tabeli. Zwróć uwagę, że spełnia kryteriów jakości:

* Dane są **odpowiednich** -wagi ostatecznie jest powiązana do ceny
* Ma ona **dokładne** -możemy double-checked ceny, które firma Microsoft Zapisz
* Ma ona **połączone** — istnieją nie spacji w dowolnej z tych kolumn
* I jak zajmiemy się tym, ma **za mało** odpowiedź na pytanie naszych

## <a name="ask-a-sharp-question"></a>Zadaj pytanie sharp
Obecnie firma Microsoft będzie stanowić naszych zapytania w sposób sharp: "będzie kosztem, jaki kupić romb karatach 1.35?"

Naszej listy nie ma romb 1.35 karatach, dlatego firma Microsoft będzie konieczne Użyj pozostałej części danych, aby uzyskać odpowiedzi na pytanie.

## <a name="plot-the-existing-data"></a>Istniejących danych
W pierwszej kolejności robimy jest poziomej linii numer o nazwie osi, do wykresu wag. Zakres wag jest 0-2, dlatego firma Microsoft będzie rysowanie linii obejmujący zakresu i umieścić znaczniki dla każdego połowa karatach.

Firma Microsoft będzie dalej Rysuj osi pionowej do rejestrowania ceny i podłącz go do osi poziomej wagi. Są to w jednostkach kwoty. Teraz mamy zestaw współrzędnych osi.

![Osie wagi i cen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Chcemy się teraz pobrać te dane i włącz go do *wykres punktowy*. Jest to dobry sposób na wizualizowania wartości liczbowych zestawów danych.

Dla pierwszego punktu danych Firma Microsoft eyeball pionowych linii w 1.01 carats. Następnie możemy eyeball poziomych linii w 7,366 $. Jeżeli spełniają one możemy zwrócić kropką. Reprezentuje naszym pierwszym romb.

Teraz możemy przejść przez każdego romb na tej liście i tak samo postąpić. Kiedy jesteśmy za pośrednictwem jest będziemy mieć: licznych kropkami, jeden dla każdej romb.

![Wykres punktowy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rysuj modelu punktów danych
Teraz można spojrzeć na squint i kropek kolekcji wygląda jak linia fat, rozmytego. Możemy zająć naszych znacznika i rysowanie linii prostej za jego pośrednictwem.

Za pomocą rysowania linii, utworzyliśmy *modelu*. Należy traktować jako biorąc rzeczywistych i wersji simplistic kreskówki go. Teraz kreskówki jest nieprawidłowy — wiersza nie jest akceptowana wszystkich punktów danych. Jednak jest przydatne uproszczenia.

![Regresji liniowej](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Fakt, że wszystkie punkty nie przechodzą dokładnie wiersz jest OK. Analityków danych opisano to przez informujący, że jest model — jest to wiersz - i następnie każdego punktu ma kilka *szumu* lub *wariancji* skojarzonych z nim. Podstawowej relacji doskonałe, a następnie istnieje świata niepowtarzalne, rzeczywistych, w której dodaje szumu i niedokładność.

Ponieważ próbujemy odpowiedzi na pytanie *ile?* jest to *regresji*. I dlatego firma Microsoft korzysta z prostej, jest *regresji liniowej*.

## <a name="use-the-model-to-find-the-answer"></a>Użyj modelu, aby znaleźć odpowiedzi
Teraz mamy modelu i poprosimy go naszych pytanie: jaka będzie romb 1.35 karatach koszt?

Odpowiedzi na pytanie naszych, firma Microsoft oka 1.35 carats i rysowanie linii pionowej. Gdzie go przecina wiersza modelu możemy eyeball linii poziomej osi dolara ($). Trafienia jego prawej strony na 10 000. Wysięgnik! To odpowiedź: romb 1.35 karatach koszty około 10 000.

![Znajdź odpowiedzi na modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Utwórz przedział ufności
Jest fizyczne do zastanawiasz się, jak dokładne jest to prognozowania. Jest grupowaniu można sprawdzić, czy romb 1.35 karatach będzie bardzo bliski 10 000, lub znacznie większej lub mniejszej. Aby ustalić tę możliwość, umożliwia Rysuj koperty wokół regresji, który zawiera większość punktów. Ten koperty jest wywoływana naszych *przedział ufności*: jesteśmy pretty pewność, że ceny mieszczą się w tym koperty, ponieważ w ciągu ostatnich większość z nich. Firma Microsoft Rysuj dwóch więcej poziome linie, z których wiersza 1.35 karatach przecina góry i u dołu tej koperty.

![Przedział ufności](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Teraz możemy powiedzieć coś o naszych przedział ufności: możemy powiedzieć bez obaw czy cena romb 1.35 karatach jest około $ 10 000 -, ale może być możliwie jak $8000 i może być możliwie jak 12 000.

## <a name="were-done-with-no-math-or-computers"></a>Skończymy, bez matematyczne lub komputerów
Robiliśmy, jakie analityków danych uzyskać płatną zrobić i robiliśmy tylko za pomocą rysowania:

* Firma Microsoft zadawane pytania firma Microsoft może odpowiedzi z danymi
* Budujemy *modelu* przy użyciu *regresji liniowej*
* Wprowadziliśmy *prognozowania*, wraz z *przedział ufności*

I nie używamy matematyczne lub komputerów Aby wykonać to zadanie.

Teraz, jeśli tak samo, jak gdyby było więcej informacji...

* Przecięcie romb
* kolory (jak blisko romb jest jest białe)
* Liczba dołączenia w romb

.. .i firma musiałaby większą liczbę kolumn. W takim przypadku matematyczne staje się przydatne. Jeśli masz więcej niż dwie kolumny jest trudna do rysowania kropki w dokumencie. Obliczenia umożliwia bardzo dobrze nadające się tego wiersza lub tej płaszczyzny do danych.

Ponadto jeśli zamiast tylko kilku karo, było dwa tysiące lub dwóch milionów, a następnie wykonaj pracy znacznie szybciej z komputerem.

Obecnie zajmowaliśmy sposób wykonywania regresji liniowej i wprowadziliśmy prognozowania, przy użyciu danych.

Należy koniecznie zapoznaj się z innych plików wideo "Danych nauki dla początkujących" z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszego eksperymentu analizy danych z usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego w systemie Microsoft Azure](what-is-machine-learning.md)
