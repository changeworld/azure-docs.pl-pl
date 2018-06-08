---
title: Czy Twoje dane są gotowe na analizę danych? Ocena danych - Azure Machine Learning | Dokumentacja firmy Microsoft
description: Cztery kryteria danych trzeba spełnić, aby być gotowa do analizy danych. Ten plik wideo ma konkretnego przykłady ułatwiające oceny podstawowe dane.
keywords: odpowiednie dane oceny danych, należy przygotować danych kryteria danych, danych gotowe
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 5ab3c7716485053432240cb74be8ebc60c9ad274
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834621"
---
# <a name="is-your-data-ready-for-data-science"></a>Czy Twoje dane są gotowe na analizę danych?
## <a name="video-2-data-science-for-beginners-series"></a>Wideo 2: Nauki danych serii dla początkujących
Dowiedz się, jak można obliczyć wartości danych, aby upewnić się, że spełnia on podstawowe kryteria gotowość do analizy danych.

Aby uzyskać wykorzystanie serii, obejrzyj je wszystkie. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne pliki wideo w tej serii
*Nauki danych dla początkujących* jest szybkie wprowadzenie do analizy danych w pięciu krótkie wideo.

* Wideo 1: [danych nauki odpowiedzi na pytania 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: Jest gotowy do analizy danych danych?
* Wideo 3: [Zadaj pytanie może odpowiedzieć z danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 s 17 min)*
* Wideo 4: [prognozowania odpowiedzi z modelu prostego](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 s 42 min)*
* Wideo 5: [skopiuj pracy innych osób nauki danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Zapis: Jest gotowy do analizy danych danych?
— Zapraszamy! "Jest danych gotowe do analizy danych?" drugi wideo w serii *nauki danych dla początkujących*.  

Przed nauki danych można nadać odpowiedzi, który ma, należy nadać mu niektórych materiałów raw wysokiej jakości do pracy z. Podobnie jak wprowadzenie składników rozpoczęcia z lepsze produktu końcowego pizza, tym lepiej. 

## <a name="criteria-for-data"></a>Kryteria dla danych
W nauce danych istnieją niektórych składników, które muszą być pobierane razem w tym:

* Odpowiednie
* Połączono
* Dokładne
* Aby pracować z

## <a name="is-your-data-relevant"></a>Dotyczy danych?
Pierwszy składnik — należy więc dane.

![Istotne dane, a dane nie ma znaczenia — oceny danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Po lewej stronie tabela przedstawia poziom alkohol krwi siedmiu osób przetestowane poza pasek Boston, Sox czerwony średnią batting ich ostatniego gry, w i cen mleka w najbliższej sklepie.

To są wszystkie dane doskonale uzasadnionych. Błąd tylko jego jest, że nie jest odpowiedni. Nie ma żadnej oczywiste zależności między te liczby. Jeśli określono bieżącej ceny i średnia batting Sox czerwony, nie istnieje sposób można odgadnąć ich zawartość alkoholu krwi.

Teraz wyglądać w tabeli po prawej stronie. Tym razem treści każda osoba masowej zmierzono oraz liczbę napojów one właśnie.  Numery w każdym wierszu teraz mają zastosowanie do siebie. Jeśli I udostępniła Ci Moje treści masowej oraz liczby Margaritas I właśnie, można utworzyć wynik w mojej krwi alkohol zawartości.

## <a name="do-you-have-connected-data"></a>Czy nawiązano połączenie danych?
Następny składnik jest połączonych danych.

![Połączenia danych, a dane bez połączenia — kryteria danych danych gotowe](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Oto niektóre dane dotyczące jakości hamburgers: kraty temperatury, waga patty i ocenę w lokalnym żywności magazyn. Jednak zauważyć luk w tabeli po lewej stronie.

Brak niektórych wartości większość zestawów danych. Jest często mają luk w następujący sposób i sposoby ich obejścia. Jednak jeśli istnieje za dużo Brak, dane rozpocznie się wyglądały jak ser Szwajcaria.

Jeśli Szukaj w tabeli po lewej stronie ma tak dużo brakujące dane, trudno jest opracowywane dowolnego rodzaju relacji między rusztem temperatury i patty wagi. Ten przykład przedstawia bez połączenia danych.

Tabeli po prawej stronie, jednak jest zapełniony i ukończenia — przykład połączonych danych.

## <a name="is-your-data-accurate"></a>Dane są dokładne?
Następny składnik jest dokładności. Poniżej przedstawiono cztery miejsc docelowych trafień.

![Dokładne dane a niedokładne dane — kryteria danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Szukaj w celu, w prawym górnym rogu. Brak ścisłej grupowania prawo wokół oka byków. Oczywiście jest dokładna. Dziwnie danego języka nauki danych wydajności z prawej strony docelowej poniżej jest traktowana jako dokładne.

Jeśli zmapowaniu Centrum te strzałki, zobaczysz, że bardzo jest bliski oka byków. Strzałki są rozproszone wszystkie wokół docelowej, więc są traktowane jako nieprecyzyjna, ale jest wyśrodkowywana wokół oka byków, są traktowane jako dokładne.

Teraz wyglądać w lewym górnym docelowej. W tym miejscu strzałki bardzo blisko siebie osiągnęła ścisłej grupowania. Są one dokładne, ale są one nieprawidłowe, ponieważ Centrum jest sposób poza oka byków. Strzałki w lewym dolnym rogu docelowej są nieprawidłowe i niedokładna. Ten archer wymaga więcej rozwiązań.

## <a name="do-you-have-enough-data-to-work-with"></a>Masz za mało danych do pracy z?
Na koniec składnik #4 jest wystarczającą ilość danych.

![Masz za mało danych do analizy? Ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pomyśl o każdego punktu danych w tabeli jako pędzla w malowania. Jeśli masz tylko niektóre z nich może być niewyraźny rysowania — jest stwierdzić, co to jest.

Jeśli dodasz kilka więcej pociągnięć Twojej rysowania uruchamia uzyskać nieco ostrzejszy.

Jeśli masz pociągnięć prawie za mało widoczne są tylko do niektórych szerokie decyzje. Jest to miejsce, które można znaleźć? Wygląda na to jasny, to wygląda czystej wody — tak, będącą, gdzie użyjemy na urlop.

Jak dodać więcej danych obraz staje się jaśniejszy i bardziej szczegółowe decyzje. Teraz można przyjrzeć się trzy hotels na lewym bank. Można zauważyć architektury funkcje co na pierwszym planie. Można nawet pozostać na trzeci floor z powodu widoku.

Dane, które jest odpowiednie, połączony, dokładne i wystarczająco, należy mieć wszystkie składniki wymagane do wykonania niektórych nauki wysokiej jakości danych.

Należy koniecznie zapoznaj się z czterech wideo w *nauki danych dla początkujących* z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Kolejne kroki
* [Spróbuj pierwszego eksperymentu analizy danych z usługi Machine Learning Studio](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego w systemie Microsoft Azure](what-is-machine-learning.md)
