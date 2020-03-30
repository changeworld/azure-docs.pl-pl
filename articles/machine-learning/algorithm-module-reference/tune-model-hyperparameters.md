---
title: Dostrajanie hiperparametrów modelu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Tune Model Hyperparameters w usłudze Azure Machine Learning do wykonywania wyciągnięcia po kursach w modelu w celu określenia optymalnych ustawień parametrów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064158"
---
# <a name="tune-model-hyperparameters"></a>Dostrajanie hiperparametrów modelu

W tym artykule opisano, jak używać modułu Tune Model Hyperparameters w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Celem jest określenie optymalnych hiperparametrów dla modelu uczenia maszynowego. Moduł tworzy i testuje wiele modeli przy użyciu różnych kombinacji ustawień. Porównuje metryki we wszystkich modelach, aby uzyskać kombinacje ustawień. 

*Parametr* terminów i *hiperparametr* może być mylące. Parametry modelu są *to,* co można ustawić w prawym okienku modułu. Zasadniczo moduł ten wykonuje *wyciągnięcie po parametrze* w określonych ustawieniach parametrów. Uczy się optymalnego zestawu _hiperparametrów_, które mogą być różne dla każdego określonego drzewa decyzyjnego, zestawu danych lub metody regresji. Proces znajdowania optymalnej konfiguracji jest czasami nazywany *strojeniem*. 

Moduł obsługuje następującą metodę znajdowania optymalnych ustawień dla modelu: *zintegrowany pociąg i dostrojenie.* W tej metodzie można skonfigurować zestaw parametrów do użycia. Następnie pozwól modułowi iterować za pośrednictwem wielu kombinacji. Moduł mierzy dokładność, dopóki nie znajdzie "najlepszego" modelu. W przypadku większości modułów uczących można wybrać, które parametry należy zmienić podczas procesu szkolenia, a które powinny pozostać stałe.

W zależności od tego, jak długo ma być uruchamiany proces strojenia, możesz zdecydować się na wyczerpujące przetestowanie wszystkich kombinacji. Można też skrócić proces, ustanawiając siatkę kombinacji parametrów i testując losowy podzbiór siatki parametrów.

Ta metoda generuje wyszkolony model, który można zapisać do ponownego użycia.  

> [!TIP] 
> Możesz wykonać powiązane zadanie. Przed rozpoczęciem dostrajania zastosuj wybór operacji, aby określić kolumny lub zmienne, które mają najwyższą wartość informacji.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak skonfigurować tune modelu hiperparametry  

Uczenie się optymalnych hiperparametrów dla modelu uczenia maszynowego wymaga znacznego wykorzystania potoków.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Trenuj model przy użyciu wyciągnięcia po kursach  

W tej sekcji opisano sposób wykonywania wyciągnięcia po ścieżce parametrów podstawowych, które trenuje model przy użyciu modułu Tune Model Hyperparameters.

1.  Dodaj moduł Tune Model Hyperparameters do potoku w projektancie.

2.  Połącz nieprzeszkolony model z lewym wejściem. 



4.  Dodaj zestaw danych, którego chcesz użyć do szkolenia, i podłącz go do środkowego wejścia Hyperparameters modelu tune.  

    Opcjonalnie, jeśli masz oznakowany zestaw danych, możesz podłączyć go do prawego portu wejściowego **(Opcjonalny zestaw danych sprawdzania poprawności).** Dzięki temu można mierzyć dokładność podczas treningu i strojenia.

5.  W prawym panelu Hyperparametrów tune model wybierz wartość **dla trybu zamiatanie parametrów**. Ta opcja określa sposób wybierania parametrów.

    - **Cała siatka**: Po wybraniu tej opcji moduł zapętla się nad siatką wstępnie zdefiniowaną przez system, aby wypróbować różne kombinacje i zidentyfikować najlepszego ucznia. Ta opcja jest przydatna, gdy nie wiesz, jakie mogą być najlepsze ustawienia parametrów i chcesz wypróbować wszystkie możliwe kombinacje wartości.

    - **Losowe wyciągnięcie po**ścieżce : Po wybraniu tej opcji moduł losowo wybierze wartości parametrów w zakresie zdefiniowanym przez system. Należy określić maksymalną liczbę przebiegów, które mają być wykonywane przez moduł. Ta opcja jest przydatna, gdy chcesz zwiększyć wydajność modelu przy użyciu metryk do wyboru, ale nadal oszczędzać zasoby obliczeniowe.    

6.  W **polu Etykieta otwórz**selektor kolumny, aby wybrać pojedynczą kolumnę etykiety.

7.  Wybierz liczbę przebiegów:

    1. **Maksymalna liczba przebiegów na losowym wyciągnięciu po ścieżce**: Jeśli wybierzesz losowe wyciągnięcie po ścieżce, można określić, ile razy model powinien być przeszkolony, używając losowej kombinacji wartości parametrów.

8.  W przypadku **rankingu**wybierz jedną metrykę, która będzie używana do klasyfikowania modeli.

    Po uruchomieniu wyciągnięcia po parametrze moduł oblicza wszystkie odpowiednie metryki dla typu modelu i zwraca je w raporcie **wyników wyciągnięcia po ścieżce.** Moduł używa oddzielnych metryk dla modeli regresji i klasyfikacji.

    Jednak metryka, która wybierzesz określa, jak modele są klasyfikowane. Tylko top model, w rankingu według wybranej metryki, jest dane wyjściowe jako wyszkolony model do użycia do oceniania.

9.  W przypadku **losowego materiału siewnego**wprowadź liczbę, która ma być używana do rozpoczęcia wyciągnięcia po parametrze. 

10. Prześlij potok.

## <a name="results-of-hyperparameter-tuning"></a>Wyniki strojenia hiperparametryczne

Po zakończeniu szkolenia:

+ Aby wyświetlić zestaw metryk dokładności dla najlepszego modelu, kliknij prawym przyciskiem myszy moduł, a następnie wybierz polecenie **Wizualizuj**.

    Dane wyjściowe obejmują wszystkie metryki dokładności, które mają zastosowanie do typu modelu, ale metryka wybrana do rankingu określa, który model jest uważany za "najlepszy".

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.


## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji i porady.

### <a name="how-a-parameter-sweep-works"></a>Jak działa wyciągnięcie po parametrach

Podczas konfigurowania wyciągnięcia po parametrze definiuje się zakres wyszukiwania. Wyszukiwanie może używać skończonej liczby parametrów wybranych losowo. Lub może to być wyczerpujące wyszukiwanie w przestrzeni parametrów, które definiujesz.

+ **Losowe wyciągnięcie po**ścieżce: Ta opcja trenuje model przy użyciu określonej liczby iteracji. 

  Należy określić zakres wartości do iteracji ponad i moduł używa losowo wybrany podzbiór tych wartości. Wartości są wybierane z wymianą, co oznacza, że liczby wcześniej wybrane losowo nie są usuwane z puli dostępnych numerów. Tak więc szansa na dowolną wartość jest wybrana pozostaje taka sama we wszystkich przejściach.  

+ **Cała siatka**: Opcja użycia całej siatki oznacza, że każda kombinacja jest testowana. Ta opcja jest najbardziej dokładna, ale wymaga najwięcej czasu. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrolowanie długości i złożoności szkolenia

Iteracja w wielu kombinacjach ustawień może być czasochłonna, więc moduł udostępnia kilka sposobów na ograniczenie procesu:

+ Ogranicz liczbę iteracji używanych do testowania modelu.
+ Ogranicz odstęp parametru.
+ Ogranicz zarówno liczbę iteracji, jak i spację parametrów.

Zaleca się potok z ustawieniami, aby określić najbardziej efektywną metodę szkolenia na określonym zestawie danych i modelu.

### <a name="choosing-an-evaluation-metric"></a>Wybieranie metryki oceny

Po zakończeniu testowania model przedstawia raport, który zawiera dokładność dla każdego modelu, dzięki czemu można przejrzeć wyniki metryki:

- Jednolity zestaw metryk jest używany dla wszystkich modeli klasyfikacji binarnej.
- Dokładność jest używana we wszystkich wieloklasowych modelach klasyfikacji.
- Inny zestaw metryk jest używany dla modeli regresji. 

Jednak podczas szkolenia należy wybrać *pojedynczą* metrykę do użycia w rankingu modeli, które są generowane podczas procesu strojenia. Może się okazać, że najlepsza metryka różni się w zależności od problemu biznesowego i kosztów fałszywych alarmów i fałszywych negatywów.

#### <a name="metrics-used-for-binary-classification"></a>Metryki używane do klasyfikacji binarnej

-   **Dokładność** to odsetek prawdziwych wyników do wszystkich przypadków.  

-   **Precyzja** to proporcja prawdziwych wyników do pozytywnych wyników.  

-   **Przypomnijmy,** że jest ułamek wszystkich prawidłowych wyników na wszystkie wyniki.  

-   **F-score** jest miarą, która równoważy precyzję i wycofanie.  

-   **AUC** jest wartością reprezentującą obszar pod krzywą, gdy na osi x kreślone są fałszywe alarmy, a na osi y są kreślone prawdziwe wartości dodatnie.  

-   **Średnia strata dziennika** jest różnica między dwóch rozkładów prawdopodobieństwa: true jeden i jeden w modelu.  

#### <a name="metrics-used-for-regression"></a>Metryki używane do regresji

-   **Średni błąd bezwzględny** uśrednia wszystkie błędy w modelu, gdzie *błąd* oznacza odległość przewidywanej wartości od wartości rzeczywistej. To jest często skracane jako *MAE*.  

-   **Korzeń średniego kwadratu błąd** mierzy średnią kwadratów błędów, a następnie pobiera katalog główny tej wartości. To jest często skracane jako *RMSE*.  

-   **Względny błąd bezwzględny** reprezentuje błąd jako procent wartości rzeczywistej.  

-   **Względny kwadratowy błąd** normalizuje całkowity kwadratowy błąd przez podzielenie przez całkowity kwadrat błędu przewidywanych wartości.  

-   **Współczynnik oznaczania** jest pojedynczą liczbą, która wskazuje, jak dobrze dane pasują do modelu. Wartość jednego oznacza, że model dokładnie pasuje do danych. Wartość zero oznacza, że dane są losowe lub w inny sposób nie mogą być dostosowane do modelu. Często nazywa się *r<sup>2,</sup>* *R<sup>2</sup>* lub *r-kwadrat.*  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduły, które nie obsługują wyciągnięcia po parametrze

Prawie wszyscy uczniowie w usłudze Azure Machine Learning obsługują krzyżowe sprawdzanie poprawności za pomocą zintegrowanego wyciągnięcia po ścieżce parametrów, które umożliwia wybranie parametrów do potoku. Jeśli uczeń nie obsługuje ustawiania zakresu wartości, nadal można go używać w krzyżowej weryfikacji. W takim przypadku dla wyciągnięcia po ścieżce wyboru jest wybrany zakres dozwolonych wartości. 


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

