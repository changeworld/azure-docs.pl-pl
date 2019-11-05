---
title: Dostrajanie parametrów modelu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu dostrajania parametrów modelu w usłudze Azure Machine Learning, aby przeprowadzić odczyszczenie parametrów na modelu w celu określenia optymalnych ustawień parametrów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515657"
---
# <a name="tune-model-hyperparameters"></a>Dostrajanie parametrów modelu

W tym artykule opisano, jak używać modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu określenia optymalnych parametrów dla danego modelu uczenia maszynowego. Moduł kompiluje i testuje wiele modeli przy użyciu różnych kombinacji ustawień i porównuje metryki dla wszystkich modeli w celu uzyskania kombinacji ustawień. 

*Parametr* terms i *parametr preparameter* mogą być mylące. *Parametry* modelu są ustawiane w okienku właściwości. Zasadniczo ten moduł wykonuje *odczyszczenie parametrów* względem określonych ustawień parametrów i uczy się optymalnym zbiorem _parametrów_, które mogą być różne dla każdego określonego drzewa decyzyjnego, zestawu danych lub metody regresji. Proces znajdowania optymalnej konfiguracji jest czasami nazywany *dostrajaniem*. 

Moduł obsługuje następującą metodę wyszukiwania optymalnych ustawień dla modelu 

**Zintegrowane uczenie i dostrajanie**: należy skonfigurować zestaw parametrów do użycia, a następnie pozwolić, aby moduł przeprowadził iterację nad wieloma kombinacjami, mierząc dokładność do momentu znalezienia "najlepszego" modelu. W przypadku większości modułów szkoleniowych można wybrać parametry, które należy zmienić podczas procesu szkolenia, a które powinny pozostać stałe.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Ta metoda generuje model szkolony, który można zapisać do ponownego użycia.  

### <a name="related-tasks"></a>Powiązane zadania

+ Przed przeprowadzeniem dostrajania Zastosuj wybór funkcji, aby określić kolumny lub zmienne, które mają największą wartość informacji.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak skonfigurować dostrojenie parametrów modelu  

Ogólnie rzecz biorąc, uczenie optymalnych parametrów dla danego modelu uczenia maszynowego wymaga dużej potoku.

### <a name="train-a-model-using-a-parameter-sweep"></a>Uczenie modelu przy użyciu odchylenia parametrów  

W tej sekcji opisano, jak wykonać podstawowe odwzorowanie parametrów, które pociąga za pomocą modułu [strojenia modelu](tune-model-hyperparameters.md) .

1.  Dodaj moduł [dostrajania parametrów modelu](tune-model-hyperparameters.md) do potoku w projektancie.

2.  Połącz niepociąg model z lewej strony wejściowej. 

3. Dla opcji **Utwórz tryb Trainer** ustaw wartość **zakres parametrów** i Użyj **konstruktora zakresu** , aby określić zakres wartości do użycia w wyczyszczeniu parametrów.  

    Prawie wszystkie moduły klasyfikacji i regresji obsługują odchylenia zintegrowanego parametru. Dla tych osób, które nie obsługują konfigurowania zakresu parametrów, można przetestować tylko dostępne wartości parametrów.

    Można ręcznie ustawić wartość dla jednego lub kilku parametrów, a następnie odszukać pozostałe parametry. Może to spowodować oszczędność czasu.

4.  Dodaj zestaw danych, który ma być używany na potrzeby szkolenia, i połącz go z środkowym wejściem [parametrów strojenia modelu](tune-model-hyperparameters.md).  

    Opcjonalnie, jeśli masz oznakowany zestaw danych, możesz połączyć go z najbardziej przyłączonym portem wejściowym (**opcjonalny zestaw danych walidacji**). Pozwala to mierzyć dokładność podczas uczenia i dostrajania.

5.  W okienku **Właściwości** okna [strojenie modelu dostrajania](tune-model-hyperparameters.md)wybierz wartość dla **trybu odchylenia parametru**. Ta opcja określa, jak są wybierane parametry.

    - **Cała siatka**: w przypadku wybrania tej opcji moduł jest pętlą względem siatki wstępnie zdefiniowanej przez system, aby wypróbować różne kombinacje i zidentyfikować najlepszą naukę. Ta opcja jest przydatna w przypadkach, gdy nie wiesz, jakie są ustawienia najlepszych parametrów, i chcesz wypróbować wszystkie możliwe kombinacje wartości.

    - **Losowe wyczyszczenie**: po wybraniu tej opcji moduł będzie losowo wybierać wartości parametrów w zakresie zdefiniowanym przez system. Należy określić maksymalną liczbę uruchomień wykonywanych przez moduł. Ta opcja jest przydatna w przypadku, gdy chcesz zwiększyć wydajność modelu przy użyciu wybranych przez siebie metryk, ale nadal zachowuj zasoby obliczeniowe.

    W przypadku wybrania losowego odchylenia można określić **maksymalną liczbę przebiegów losowych**, co oznacza, ile razy model powinien być przeszkolony przy użyciu losowej kombinacji wartości parametrów.

6.  Dla **kolumny etykieta**Uruchom selektor kolumny, aby wybrać jedną kolumnę etykiety.

7.  **Maksymalna liczba przebiegów na losowym wyczyszczeniu: w**przypadku wybrania losowego wycierania można określić, ile razy model ma być szkolony, przy użyciu losowej kombinacji wartości parametrów.

    **Maksymalna liczba przebiegów w losowej siatce**: Ta opcja określa również liczbę iteracji w losowym próbkowaniu wartości parametrów, ale wartości nie są generowane losowo z określonego zakresu; Zamiast tego tworzona jest macierz wszystkich możliwych kombinacji wartości parametrów, a losowe próbkowanie jest pobierane przez macierz. Ta metoda jest wydajniejsza i mniej podatna na Przepróbkowanie regionalne lub Podpróbkowanie.

8.  Wybierz pojedynczą metrykę do użycia podczas **klasyfikowania** modeli.

    Po uruchomieniu odchylenia parametrów wszystkie odpowiednie metryki dla typu modelu są obliczane i są zwracane w raporcie **wyniki odchylenia** . Różne metryki są używane na potrzeby regresji i modeli klasyfikacji.

    Wybrana Metryka określa jednak, w jaki sposób modele są klasyfikowane. Tylko górny model, uporządkowany według wybranej metryki, jest wyprowadzany jako model przeszkolony do użycia na potrzeby oceniania.

9.  W przypadku **losowego inicjatora**wpisz liczbę, która ma być używana podczas inicjowania odchylenia parametrów. 

10. Uruchamianie potoku.

## <a name="results-of-hyperparameter-tuning"></a>Wyniki strojenia parametru

Po zakończeniu szkolenia:

+ Aby wyświetlić zestaw metryk dokładności dla najlepszego modelu, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **wyniki odchylenia**, a następnie wybierz polecenie **Wizualizuj**.

    Wszystkie metryki dokładności mające zastosowanie do typu modelu są wyjściowe, ale Metryka wybrana do klasyfikacji określa model, który jest uznawany za "najlepszy".

+ Aby użyć modelu do oceniania w innych potokach bez konieczności powtarzania procesu dostrajania, kliknij prawym przyciskiem myszy dane wyjściowe modelu i wybierz polecenie **Zapisz jako przeszkolony model**. 


## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="how-a-parameter-sweep-works"></a>Jak działa odczyszczenie parametrów

W tej sekcji opisano sposób, w jaki operacje odchylenia parametrów są ogólne i jak działają opcje w tym module.

Podczas konfigurowania odchylenia parametrów należy zdefiniować zakres wyszukiwania, aby użyć ograniczonej liczby parametrów wybranych losowo, lub wyczerpującego wyszukiwania na zdefiniowanej przestrzeni parametrów.

+ **Losowe wyczyszczenie**: Ta opcja powoduje przeciąganie modelu przy użyciu ustawionej liczby iteracji. 

     Należy określić zakres wartości do iteracji, a moduł używa losowo wybranego podzbioru tych wartości.  Wartości są wybierane z zastępowaniem, co oznacza, że numery wcześniej wybrane losowo nie są usuwane z puli dostępnych numerów. W ten sposób szansa wyboru wartości jest taka sama we wszystkich przebiegach.  

+ **Cała siatka**: opcja korzystania z całej siatki oznacza tylko, że: każda kombinacja jest testowana. Ta opcja może być uważana za najbardziej gruntowną, ale wymaga najwięcej czasu. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrolowanie długości i złożoności szkolenia

Iterowanie wielu kombinacji ustawień może być czasochłonne, więc moduł oferuje kilka sposobów ograniczenia procesu:

+ Ogranicz liczbę iteracji używanych do testowania modelu
+ Ogranicz miejsce parametru
+ Ogranicz liczbę iteracji i miejsce parametru

Zalecamy, aby przetworzyć ustawienia, aby określić najbardziej wydajną metodę szkolenia dla określonego zestawu danych i modelu.

### <a name="choosing-an-evaluation-metric"></a>Wybieranie metryki oceny

Raport zawierający dokładność poszczególnych modeli jest prezentowany na końcu, aby można było przejrzeć wyniki pomiarów. Jednolity zestaw metryk jest używany dla wszystkich modeli klasyfikacji binarnej, dokładność jest używana dla wszystkich wieloklasowych modeli klasyfikacji, a dla modeli regresji jest używany inny zestaw metryk. Jednak podczas uczenia należy wybrać **jedną** metrykę do użycia w celu klasyfikowania modeli, które są generowane podczas procesu dostrajania. Może się okazać, że Najlepsza Metryka zależy od problemu biznesowego i kosztu fałszywych dodatnich i fałszywych wartości ujemnych.

#### <a name="metrics-used-for-binary-classification"></a>Metryki używane na potrzeby klasyfikacji binarnej

-   **Dokładność** Proporcja prawdziwych wyników do łącznej liczby przypadków.  

-   **Precyzja** Proporcja prawdziwych wyników do pozytywnych wyników.  

-   **Odwołaj** Część wszystkich poprawnych wyników dla wszystkich wyników.  

-   **F-Score** Miara, która równoważy precyzję i odwołanie.  

-   **AUC** Wartość, która reprezentuje obszar pod krzywą w przypadku, gdy fałszywie dodatnie są kreślone na osi x i prawdziwe wartości dodatnie są kreślone na osi y.  

-   **Średnia utrata dziennika** Różnica między dwoma dystrybucjami prawdopodobieństwa: wartość true i jeden w modelu.  

#### <a name="metrics-used-for-regression"></a>Metryki używane na potrzeby regresji

-   Średni **błąd bezwzględny oznacza** wszystkie błędy w modelu, gdzie błąd oznacza odległość przewidywanej wartości od prawdziwej wartości. Często skraca się jako **Mae**.  

-   **Element główny średniej wartości w kwadracie** mierzy średnią kwadratów błędów, a następnie pobiera element główny tej z nich. Często skrócony jako **RMSE**  

-   **Względny błąd bezwzględny** reprezentuje błąd jako procent wartości prawdziwej.  

-   **Względny kwadratowy błąd** normalizuje łączny kwadratowy błąd, dzieląc przez łączny kwadratowy błąd prognozowanych wartości.  

-   **Współczynnik wyznaczania** Pojedyncza liczba, która wskazuje, jak dobre dane pasują do modelu. Wartość jednego oznacza, że model dokładnie dopasowuje dane; wartość zero oznacza, że dane są losowo lub w przeciwnym razie nie można dopasować do modelu. Często określana jako **r<sup>2</sup>** , **r<sup>2</sup>** lub **r-kwadrat**.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Moduły, które nie obsługują odchylenia parametrów

Prawie wszyscy użytkownicy w Azure Machine Learning obsługują krzyżowe sprawdzanie poprawności przy użyciu zintegrowanego odchylenia parametru, który umożliwia wybranie parametrów do potoku. Jeśli dowiedzenie nie obsługuje ustawiania zakresu wartości, można nadal używać go do wzajemnego sprawdzania poprawności. W takim przypadku dla odchylenia jest wybierany zakres dozwolonych wartości. 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 

