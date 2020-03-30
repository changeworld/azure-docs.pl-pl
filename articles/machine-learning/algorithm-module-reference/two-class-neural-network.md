---
title: 'Dwuklasowa sieć neuronowa: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć dwuklasowego modułu sieci neuronowej w usłudze Azure Machine Learning do utworzenia modelu sieci neuronowej, który może służyć do przewidywania obiektu docelowego, który ma tylko dwie wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477395"
---
# <a name="two-class-neural-network-module"></a>Dwuklasowy moduł sieci neuronowej

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu sieci neuronowej, który może służyć do przewidywania obiektu docelowego, który ma tylko dwie wartości.

Klasyfikacja przy użyciu sieci neuronowych jest nadzorowaną metodą uczenia się i dlatego wymaga *oznakowanego zestawu danych*, który zawiera kolumnę etykiet. Na przykład można użyć tego modelu sieci neuronowej do przewidywania wyników binarnych, takich jak czy pacjent ma pewną chorobę lub czy maszyna może zakończyć się niepowodzeniem w określonym przedziale czasu.  

Po zdefiniowaniu modelu trenuj go, podając oznakowany zestaw danych i model jako dane wejściowe do [modelu pociągu](./train-model.md). Przeszkolony model może następnie służyć do przewidywania wartości dla nowych danych wejściowych.

### <a name="more-about-neural-networks"></a>Więcej informacji o sieciach neuronowych

Sieć neuronowa to zestaw połączonych ze sobą warstw. Dane wejściowe są pierwszą warstwą i są połączone z warstwą wyjściową za pomocą wykresu acyklicznego składającego się z ważonych krawędzi i węzłów.

Między warstwami wejściowymi i wyjściowymi można wstawić wiele ukrytych warstw. Większość zadań predykcyjnych można łatwo wykonać za pomocą tylko jednej lub kilku ukrytych warstw. Jednak ostatnie badania wykazały, że głębokie sieci neuronowe (DNN) z wieloma warstwami mogą być skuteczne w złożonych zadaniach, takich jak rozpoznawanie obrazu lub mowy. Kolejne warstwy są używane do modelowania rosnących poziomów głębi semantycznej.

Relacji między wejściami i wyjściami uczy się od szkolenia sieci neuronowej na danych wejściowych. Kierunek wykresu przechodzi od wejść przez warstwę ukrytą i do warstwy wyjściowej. Wszystkie węzły w warstwie są połączone ważonymi krawędziami z węzłami w następnej warstwie.

Aby obliczyć dane wyjściowe sieci dla określonego wejścia, wartość jest obliczana w każdym węźle w warstwach ukrytych i w warstwie wyjściowej. Wartość jest ustawiana przez obliczenie ważonej sumy wartości węzłów z poprzedniej warstwy. Funkcja aktywacji jest następnie stosowana do tej sumy ważonej.
  
## <a name="how-to-configure"></a>Jak skonfigurować

1.  Dodaj moduł **sieci neuronowej** dwuklasowej do potoku. Ten moduł można znaleźć w obszarze **Uczenie maszynowe**, **Inicjalizuj**, w kategorii **Klasyfikacja.**  
  
2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.

    -   **Zakres parametrów:** Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry za pomocą modułu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Należy podać zakres wartości, a trener iteruje przez wiele kombinacji ustawień, aby określić kombinację wartości, która daje najlepszy wynik.  

3.  W przypadku **specyfikacji warstwy ukrytej**wybierz typ architektury sieci do utworzenia.  
  
    -   **W pełni podłączony przypadek:** Używa domyślnej architektury sieci neuronowej, zdefiniowanej dla dwuklasowych sieci neuronowych w następujący sposób:
  
        -   Ma jedną ukrytą warstwę.
  
        -   Warstwa wyjściowa jest w pełni połączona z warstwą ukrytą, a warstwa ukryta jest w pełni połączona z warstwą wejściową.
  
        -   Liczba węzłów w warstwie wejściowej jest równa liczbie obiektów w danych szkoleniowych.
  
        -   Liczba węzłów w warstwie ukrytej jest ustawiana przez użytkownika. Wartość domyślna to 100.
  
        -   Liczba węzłów jest równa liczbie klas. W przypadku dwuklasowej sieci neuronowej oznacza to, że wszystkie dane wejściowe muszą być mapowane do jednego z dwóch węzłów w warstwie wyjściowej.

5.  W przypadku **szybkości uczenia się**należy zdefiniować rozmiar kroku wykonanego przy każdej iteracji przed korektą. Większa wartość dla szybkości uczenia się może spowodować, że model zbiega się szybciej, ale może przekroczyć lokalne minima.

6.  W przypadku **liczby iteracji uczenia się**określ maksymalną liczbę przypadków, w których algorytm powinien przetwarzać przypadki szkolenia.

7.  W przypadku **początkowej średnicy wag uczenia się**określ wagi węzła na początku procesu uczenia się.

8.  Dla **pędu**, określić wagę do zastosowania podczas nauki do węzłów z poprzednich iteracji  

10. Wybierz opcję **Losuj przykłady,** aby przetasować sprawy między iteracjami. Jeśli usuniesz zaznaczenie tej opcji, sprawy są przetwarzane w dokładnie tej samej kolejności przy każdym uruchomieniu potoku.
  
11. W polu **Ilość materiału siewnego liczb losowych**wpisz wartość, która ma być używana jako materiał siewny.
  
     Określanie wartości inicjatora jest przydatne, gdy chcesz zapewnić powtarzalność w przebiegach tego samego potoku.  W przeciwnym razie wartość zegara systemowego jest używana jako inicjatora, co może spowodować nieco inne wyniki przy każdym uruchomieniu potoku.
  
13. Dodaj oznakowany zestaw danych do potoku i połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](train-model.md)  
  
14. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

+ Aby użyć modelu do oceniania, dodaj **wynik modelu** modułu do potoku.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
