---
title: 'Wieloklasowy las decyzyjny: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć wieloklasowego modułu Las decyzji w usłudze Azure Machine Learning do utworzenia modelu uczenia maszynowego na podstawie algorytmu *lasu decyzyjnego.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477548"
---
# <a name="multiclass-decision-forest-module"></a>Wieloklasowy moduł decyzyjny lasu

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie algorytmu *lasu decyzji.* Las decyzyjny to model zespołu, który szybko buduje serię drzew decyzyjnych, ucząc się na podstawie oznakowanych danych.

## <a name="more-about-decision-forests"></a>Więcej o lasach decyzyjnych

Algorytm lasu decyzyjnego jest metodą uczenia się zespołu do klasyfikacji. Algorytm działa poprzez tworzenie wielu drzew decyzyjnych, a następnie *głosowanie* na najbardziej popularne klasy wyjściowej. Głosowanie jest formą agregacji, w której każde drzewo w decyzji o klasyfikacji lasów wyprowadza nieznormalizowany histogram częstotliwości etykiet. Proces agregacji sumuje te histogramy i normalizuje wynik, aby uzyskać "prawdopodobieństwa" dla każdej etykiety. Drzewa, które mają wysokie zaufanie przewidywania mają większą wagę w ostatecznej decyzji zespołu.

Drzewa decyzyjne są ogólnie modelami nieparametrycznymi, co oznacza, że obsługują dane o zróżnicowanych rozkładach. W każdym drzewie sekwencja prostych testów jest uruchamiana dla każdej klasy, zwiększając poziomy struktury drzewa, dopóki nie zostanie osiągnięty węzeł liścia (decyzja).

Drzewa decyzyjne mają wiele zalet:

+ Mogą one reprezentować nieliniowe granice decyzji.
+ Są one wydajne w obliczeniach i użycia pamięci podczas szkolenia i przewidywania.
+ Wykonują one zintegrowany wybór i klasyfikację funkcji.
+ Są odporne w obecności hałaśliwych funkcji.

Klasyfikator lasu decyzyjnego w usłudze Azure Machine Learning składa się z zestawu drzew decyzyjnych. Ogólnie rzecz biorąc, modele zespołu zapewniają lepsze pokrycie i dokładność niż drzewa pojedynczej decyzji. Aby uzyskać więcej informacji, zobacz [Drzewa decyzyjne](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak skonfigurować wieloklasowy las decyzyjny

1. Dodaj **wieloklasowy moduł Las decyzyjny** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Uczenie maszynowe**, **Inicjalizuj model**i **Klasyfikacja**.

2. Kliknij dwukrotnie moduł, aby otworzyć okienko **Właściwości.**

3. W przypadku **metody ponownego**próby wybierz metodę używaną do tworzenia poszczególnych drzew.  Można wybrać opcję pakowania lub replikacji.

    + **Bagging**: Bagging jest również nazywany *bootstrap agregacji*. W tej metodzie każde drzewo jest uprawiane na nowej próbce, tworzone przez losowe próbkowanie oryginalnego zestawu danych z zastąpieniem, dopóki nie masz zestawu danych o rozmiarze oryginału. Wyniki modeli są łączone przez *głosowanie,* co jest formą agregacji. Aby uzyskać więcej informacji, zobacz wpis w Wikipedii dla agregowania Bootstrap.

    + **Replikacja:** W replikacji każde drzewo jest przeszkolone na dokładnie tych samych danych wejściowych. Określenie, które predykatu podziału jest używany dla każdego węzła drzewa pozostaje losowy, tworząc różne drzewa.

   

4. Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**

    + **Pojedynczy parametr:** Wybierz tę opcję, jeśli wiesz, jak chcesz skonfigurować model, i podaj zestaw wartości jako argumenty.

    + **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.   

5. **Liczba drzew decyzyjnych**: Wpisz maksymalną liczbę drzew decyzyjnych, które mogą być tworzone w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia może wzrosnąć.

    Ta wartość kontroluje również liczbę drzew wyświetlanych w wynikach podczas wizualizacji przeszkolonego modelu. Aby wyświetlić lub wydrukować pojedyncze drzewo, można ustawić wartość na 1; jednak oznacza to, że można wyprodukować tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.

6. **Maksymalna głębokość drzew decyzyjnych**: Wpisz liczbę, aby ograniczyć maksymalną głębokość dowolnego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję, na ryzyko niektórych overfitting i wydłużony czas szkolenia.

7. **Liczba losowych podziałów na węzeł:** Wpisz liczbę podziałów, które mają być używane podczas tworzenia każdego węzła drzewa. *Podział* oznacza, że operacje na każdym poziomie drzewa (węzła) są losowo podzielone.

8. **Minimalna liczba próbek na węzeł liścia:** Wskazać minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminala (liścia) w drzewie. Zwiększając tę wartość, można zwiększyć próg tworzenia nowych reguł.

    Na przykład z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. Jeśli zwiększysz wartość do 5, dane szkoleniowe będą musiały zawierać co najmniej pięć przypadków, które spełniają te same warunki.



10. Połącz oznaczony zestaw danych i jeden z modułów szkoleniowych:

    + Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](./train-model.md)

11. Prześlij potok.



## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 