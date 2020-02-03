---
title: Wybór funkcji w procesie nauki o danych zespołu
description: Zawiera wyjaśnienie przeznaczenia wybór funkcji i przykłady ich rolę w procesie rozszerzenia danych, uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716686"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Wybór funkcji w zespołowym przetwarzaniu danych dla celów naukowych
W tym artykule wyjaśniono celów wybór funkcji i zawiera przykłady swojej roli w procesie rozszerzenia danych, uczenia maszynowego. Te przykłady są pobierane z usługi Azure Machine Learning Studio.

Inżynieria i wybór funkcji to jedna część procesu nauki o danych zespołowych (przetwarzania TDSP), którą opisano w artykule [co to jest proces nauki o danych zespołowych?](overview.md) Inżynieria funkcji i wybór są częścią kroku **opracowywania funkcji** przetwarzania TDSP.

* **Inżynieria funkcji**: ten proces podejmuje próbę utworzenia dodatkowych odpowiednich funkcji z istniejących pierwotnych funkcji w danych i zwiększenia mocy predykcyjnej do algorytmu uczenia.
* **wybór funkcji**: ten proces wybiera podzestaw kluczy oryginalnych funkcji danych w próbie zmniejszenia liczby problemów szkoleniowych.

Zwykle **Funkcja inżynierii funkcji** jest stosowana najpierw w celu wygenerowania dodatkowych funkcji, a następnie krok **wyboru funkcji** jest wykonywany w celu wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych funkcji.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkcje filtrowania danych — wybór funkcji
Wybór funkcji może być używany na potrzeby zadań klasyfikacji lub regresji. Celem jest Wybierz podzbiór funkcji z oryginalnego zestawu danych, który zmniejszyć jego wymiarów, używając minimalny zbiór funkcji do reprezentowania maksymalną ilość wariancji w danych. Ten podzbiór funkcji, jest używany do nauczenia modelu. Wybór funkcji służy dwa główne cele.

* Po pierwsze funkcji wyboru cech często zwiększa dokładność klasyfikacji przez wyeliminowanie nie ma znaczenia, nadmiarowe lub ściśle powiązane funkcje.
* Po drugie zmniejsza liczbę funkcji, która sprawia, że proces szkolenia modelu jest bardziej wydajne. Wydajność jest ważna dla zdobywających informacje, które są kosztowne do uczenia się, takich jak maszyny wektorów pomocy technicznej.

Chociaż wybór funkcji dążyć do ograniczenia liczby funkcji w zestawie danych, używane do nauczenia modelu, go nie jest określany przez określenie "wymiarowości". Metody wyboru funkcji Wyodrębnij podzbiór funkcji oryginalne dane bez ich zmieniania.  Wymiary redukcji metody używają zaprojektowanych funkcje, które można przekształcić oryginalnego funkcji i zmodyfikuj je w ten sposób. Przykładami metod redukcji wymiarach analizy składnika podmiot zabezpieczeń, analizy canonical korelacji i pojedynczej rozkładu wartości.

Między innymi jedną kategorię powszechnie stosowane metody wyboru funkcji w kontekście nadzorowanego jest nazywany "Wybór funkcji na podstawie filtru". Poprzez ocenę korelację między każdej funkcji i atrybut target, te metody mają zastosowanie statystyczne miary, aby przypisać wynik do każdej funkcji. Funkcje są następnie uszeregowane według wynik, który może służyć do ustaw próg utrzymywanie lub wyeliminowanie określonych funkcji. Przykładami miar statystyczne w tych metodach osoby korelacji wzajemnego informacji i test chi-kwadrat.

W usłudze Azure Machine Learning Studio są moduły spośród funkcji. Jak pokazano na poniższej ilustracji, te moduły obejmują [wybór funkcji oparty na filtrowaniu][filter-based-feature-selection] i [analizę liniową discriminant][fisher-linear-discriminant-analysis].

![Modułów wyboru funkcji](./media/select-features/feature-Selection.png)

Rozważmy na przykład użycie modułu [wyboru funkcji opartego na filtrze][filter-based-feature-selection] . Dla wygody nadal korzystając z przykładu wyszukiwania tekstu. Załóżmy, że chcesz skompilować model regresji po utworzeniu zestawu funkcji 256 za pośrednictwem modułu [wyznaczania wartości skrótu funkcji][feature-hashing] oraz że zmienna odpowiedzi jest "Kol1", która zawiera klasyfikacje przeglądów z zakresu od 1 do 5. Przez ustawienie "Funkcji oceniania metody" jako "Korelacji Pearsona", "kolumna docelowa" "Col1" i "Liczba żądanych funkcji" do 50. Następnie [wybór funkcji oparty na filtrze][filter-based-feature-selection] modułu tworzy zestaw danych zawierający 50 funkcji wraz z atrybutem Target "Kol1". Na poniższej ilustracji przedstawiono przepływ ten eksperyment i parametry wejściowe:

![Właściwości modułu wyboru funkcji opartej na filtrze](./media/select-features/feature-Selection1.png)

Na poniższej ilustracji przedstawiono wynikowe zestawy danych:

![Wynikowy zestaw danych dla modułu filtru na podstawie funkcji wyboru](./media/select-features/feature-Selection2.png)

Każda funkcja jest oceniany na podstawie korelacji Pearsona między sobą i atrybut target "Col1". Oceny najważniejszych funkcji są zachowywane.

Odpowiednie wyniki wybrane funkcje są wyświetlane na poniższej ilustracji:

![Wyniki dla modułu filtru na podstawie funkcji wyboru](./media/select-features/feature-Selection3.png)

Po zastosowaniu tego modułu [wyboru funkcji opartego na filtrze][filter-based-feature-selection] są wybierane 50 z 256 funkcji, ponieważ mają one najbardziej skorelowane funkcje z zmienną docelową "Kol1" na podstawie metody oceniania "Pearson korelacji".

## <a name="conclusion"></a>Podsumowanie
Inżynieria funkcji i wybór funkcji są dwa powszechnie używane i wybrane funkcje zwiększają wydajność procesu szkolenia, który próbuje wyodrębnić informacje o kluczu zawarte w danych. Mogą również zwiększyć możliwości tych modeli do klasyfikowania dokładnie dane wejściowe i bardziej niezawodnie przewidywanie wyników zainteresowania. Funkcja inżynieria i wybór można także połączyć się bardziej praktyce tractable szkoleniowe. Robi to poprawa, a następnie zmniejszenie liczby funkcje niezbędne do kalibrować lub wytrenuj model. Funkcje wybrane do nauczenia modelu, ze sobą matematycznie rzecz biorąc, są minimalny zestaw zmienne niezależne, wyjaśnić wzorce w danych, które następnie pomyślnie przewidywania wyników.

Nie zawsze jest koniecznie do wykonywania funkcji inżynierii lub funkcji wyboru cech. Czy jest to wymagane lub nie zależy od tego, zebranych danych, algorytm zaznaczone i celem eksperymentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

