---
title: Wybór funkcji w procesie nauki o danych zespołu
description: Zawiera wyjaśnienie przeznaczenia wybór funkcji i przykłady ich rolę w procesie rozszerzenia danych, uczenia maszynowego.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a74f2c21746deb16372174d4a769f9abb825a1cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809638"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Wybór funkcji w zespołowym przetwarzaniu danych dla celów naukowych
W tym artykule wyjaśniono celów wybór funkcji i zawiera przykłady swojej roli w procesie rozszerzenia danych, uczenia maszynowego. Te przykłady są pobierane z usługi Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Inżynieria i wybór funkcji jest jedną z części z zespołu Data Science naukowych opisane w artykule [co to jest zespołu danych dla celów naukowych?](overview.md). Funkcja inżynieria i wybór są częściami **opracowywanie funkcji** krok przetwarzania TDSP.

* **inżynieria cech**: Ten proces próbuje utworzyć dodatkowe funkcje odpowiednie na podstawie istniejących funkcji nieprzetworzonych danych i zwiększyć możliwości predykcyjnych do algorytmu uczenia.
* **Wybór funkcji**: Ten proces wybiera klucza podzbiór funkcji, oryginalnym danych w celu podjęcia próby zmniejszenia wymiarach problemu szkolenia.

Zwykle **Inżynieria funkcji** najpierw stosowany jest do generowania dodatkowe funkcje, a następnie **wybór funkcji** kroku jest przeprowadzana w celu wyeliminowania funkcji nie ma znaczenia, nadmiarowe lub bardzo skorelowany.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkcje filtrowania danych — wybór funkcji
Wybór funkcji to proces, który jest powszechnie stosowana do tworzenia zestawów danych szkoleniowych modelowania predykcyjnego zadań, takich jak zadania klasyfikacji lub regresji. Celem jest Wybierz podzbiór funkcji z oryginalnego zestawu danych, który zmniejszyć jego wymiarów, używając minimalny zbiór funkcji do reprezentowania maksymalną ilość wariancji w danych. Ten podzbiór funkcji, jest używany do nauczenia modelu. Wybór funkcji służy dwa główne cele.

* Po pierwsze funkcji wyboru cech często zwiększa dokładność klasyfikacji przez wyeliminowanie nie ma znaczenia, nadmiarowe lub ściśle powiązane funkcje.
* Po drugie zmniejsza liczbę funkcji, która sprawia, że proces szkolenia modelu jest bardziej wydajne. Wydajność jest szczególnie ważne w przypadku uczących, które są kosztowne do szkolenia, takie jak obsługa wektor maszyn.

Chociaż wybór funkcji dążyć do ograniczenia liczby funkcji w zestawie danych, używane do nauczenia modelu, go nie jest określany przez określenie "wymiarowości". Metody wyboru funkcji Wyodrębnij podzbiór funkcji oryginalne dane bez ich zmieniania.  Wymiary redukcji metody używają zaprojektowanych funkcje, które można przekształcić oryginalnego funkcji i zmodyfikuj je w ten sposób. Przykładami metod redukcji wymiarach analizy składnika podmiot zabezpieczeń, analizy canonical korelacji i pojedynczej rozkładu wartości.

Między innymi jedną kategorię powszechnie stosowane metody wyboru funkcji w kontekście nadzorowanego jest nazywany "Wybór funkcji na podstawie filtru". Poprzez ocenę korelację między każdej funkcji i atrybut target, te metody mają zastosowanie statystyczne miary, aby przypisać wynik do każdej funkcji. Funkcje są następnie uszeregowane według wynik, który może służyć do ustaw próg utrzymywanie lub wyeliminowanie określonych funkcji. Przykładami miar statystyczne w tych metodach osoby korelacji wzajemnego informacji i test chi-kwadrat.

W usłudze Azure Machine Learning Studio są moduły spośród funkcji. Jak pokazano na poniższej ilustracji, obejmują moduły [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] i [Fishera analiza liniowa w Discriminant] [ fisher-linear-discriminant-analysis].

![Modułów wyboru funkcji](./media/select-features/feature-Selection.png)

Na przykład, rozważ użycie [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] modułu. Dla wygody nadal korzystając z przykładu wyszukiwania tekstu. Przyjęto założenie, chcesz zbudowania modelu regresji, po utworzeniu zestawu funkcji 256 za pośrednictwem [Tworzenie skrótu funkcji] [ feature-hashing] modułu, a zmienna odpowiedzi jest "Col1" zawierający książki przeglądu oceny w zakresie od 1 do 5. Przez ustawienie "Funkcji oceniania metody" jako "Korelacji Pearsona", "kolumna docelowa" "Col1" i "Liczba żądanych funkcji" do 50. Następnie moduł [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] tworzy zestaw danych zawierający 50 funkcji wraz z atrybut target "Col1". Na poniższej ilustracji przedstawiono przepływ ten eksperyment i parametry wejściowe:

![Właściwości modułu na podstawie wybór funkcji filtru](./media/select-features/feature-Selection1.png)

Na poniższej ilustracji przedstawiono wynikowe zestawy danych:

![Wynikowy zestaw danych dla modułu filtru na podstawie funkcji wyboru](./media/select-features/feature-Selection2.png)

Każda funkcja jest oceniany na podstawie korelacji Pearsona między sobą i atrybut target "Col1". Oceny najważniejszych funkcji są zachowywane.

Odpowiednie wyniki wybrane funkcje są wyświetlane na poniższej ilustracji:

![Wyniki dla modułu filtru na podstawie funkcji wyboru](./media/select-features/feature-Selection3.png)

Stosując to [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] modułu, 50 poza 256 funkcje są zaznaczone, ponieważ mają one najbardziej skorelowany funkcji zawiera zmienną docelową "Col1" oparte na metodzie oceniania "Korelacji Pearsona".

## <a name="conclusion"></a>Podsumowanie
Funkcja inżynieria i wybór funkcji są dwa najczęściej zaprojektowany i wybrane funkcje zwiększenia efektywności procesu uczenia, który próbuje wyodrębnić kluczowe informacje zawarte w danych. Mogą również zwiększyć możliwości tych modeli do klasyfikowania dokładnie dane wejściowe i bardziej niezawodnie przewidywanie wyników zainteresowania. Funkcja inżynieria i wybór można także połączyć się bardziej praktyce tractable szkoleniowe. Robi to poprawa, a następnie zmniejszenie liczby funkcje niezbędne do kalibrować lub wytrenuj model. Funkcje wybrane do nauczenia modelu, ze sobą matematycznie rzecz biorąc, są minimalny zestaw zmienne niezależne, wyjaśnić wzorce w danych, które następnie pomyślnie przewidywania wyników.

Nie zawsze jest koniecznie do wykonywania funkcji inżynierii lub funkcji wyboru cech. Czy jest to wymagane lub nie zależy od tego, zebranych danych, algorytm zaznaczone i celem eksperymentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

