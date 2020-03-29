---
title: 'Wieloklasowa sieć neuronowa: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu multiklasowej sieci neuronowej w usłudze Azure Machine Learning do utworzenia modelu sieci neuronowej, który może służyć do przewidywania obiektu docelowego, który ma wiele wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920420"
---
# <a name="multiclass-neural-network-module"></a>Wieloklasowy moduł sieci neuronowej

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu sieci neuronowej, który może służyć do przewidywania obiektu docelowego, który ma wiele wartości. 

Na przykład sieci neuronowe tego rodzaju mogą być używane w złożonych zadaniach widzenia komputerowego, takich jak rozpoznawanie cyfr lub liter, klasyfikacja dokumentów i rozpoznawanie wzorców.

Klasyfikacja przy użyciu sieci neuronowych jest nadzorowaną metodą uczenia się i dlatego wymaga *oznakowanego zestawu danych,* który zawiera kolumnę etykiet.

Model można trenować, udostępniając model i oznakowany zestaw danych jako dane wejściowe do [modelu pociągu](./train-model.md). Przeszkolony model może następnie służyć do przewidywania wartości dla nowych przykładów wejściowych.  

## <a name="about-neural-networks"></a>Informacje o sieciach neuronowych

Sieć neuronowa to zestaw połączonych ze sobą warstw. Dane wejściowe są pierwszą warstwą i są połączone z warstwą wyjściową za pomocą wykresu acyklicznego składającego się z ważonych krawędzi i węzłów.

Między warstwami wejściowymi i wyjściowymi można wstawić wiele ukrytych warstw. Większość zadań predykcyjnych można łatwo wykonać za pomocą tylko jednej lub kilku ukrytych warstw. Jednak ostatnie badania wykazały, że głębokie sieci neuronowe (DNN) z wieloma warstwami mogą być skuteczne w złożonych zadaniach, takich jak rozpoznawanie obrazu lub mowy. Kolejne warstwy są używane do modelowania rosnących poziomów głębi semantycznej.

Relacji między wejściami i wyjściami uczy się od szkolenia sieci neuronowej na danych wejściowych. Kierunek wykresu przechodzi od wejść przez warstwę ukrytą i do warstwy wyjściowej. Wszystkie węzły w warstwie są połączone ważonymi krawędziami z węzłami w następnej warstwie.

Aby obliczyć dane wyjściowe sieci dla określonego wejścia, wartość jest obliczana w każdym węźle w warstwach ukrytych i w warstwie wyjściowej. Wartość jest ustawiana przez obliczenie ważonej sumy wartości węzłów z poprzedniej warstwy. Funkcja aktywacji jest następnie stosowana do tej sumy ważonej.

## <a name="configure-multiclass-neural-network"></a>Konfigurowanie wieloklasowej sieci neuronowej

1. Dodaj moduł **sieci neuronowej multiklasy** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Uczenie maszynowe**, **Inicjalizuj**, w kategorii **Klasyfikacja.**

2. **Utwórz tryb trenera:** Użyj tej opcji, aby określić sposób szkolenia modelu:

    - **Pojedynczy parametr:** Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.

    - **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.  

3. **Ukryta specyfikacja warstwy:** Wybierz typ architektury sieci do utworzenia.

    - **W pełni połączony przypadek:** Wybierz tę opcję, aby utworzyć model przy użyciu domyślnej architektury sieci neuronowej. W przypadku wieloklasowych modeli sieci neuronowych wartości domyślne są następujące:

        - Jedna ukryta warstwa
        - Warstwa wyjściowa jest w pełni połączona z warstwą ukrytą.
        - Warstwa ukryta jest w pełni połączona z warstwą wejściową.
        - Liczba węzłów w warstwie wejściowej zależy od liczby obiektów w danych szkoleniowych.
        - Liczba węzłów w warstwie ukrytej może być ustawiona przez użytkownika. Wartość domyślna to 100.
        - Liczba węzłów w warstwie wyjściowej zależy od liczby klas.
  
   

5. **Liczba ukrytych węzłów:** Ta opcja umożliwia dostosowanie liczby ukrytych węzłów w architekturze domyślnej. Wpisz liczbę ukrytych węzłów. Wartość domyślna to jedna ukryta warstwa ze 100 węzłami.

6. **Szybkość uczenia się:** Zdefiniuj rozmiar kroku podjętego przy każdej iteracji przed korektą. Większa wartość dla szybkości uczenia się może spowodować, że model zbiega się szybciej, ale może przekroczyć lokalne minima.

7. **Liczba iteracji uczenia się**: Określ maksymalną liczbę razy algorytm powinien przetwarzać przypadki szkolenia.

8. **Początkowa średnica wag uczenia się**: Określ wagi węzła na początku procesu uczenia się.

9. **Pęd**: Określ wagę do zastosowania podczas nauki do węzłów z poprzednich iteracji.
  
11. **Losowanie przykładów:** Wybierz tę opcję, aby przetasować sprawy między iteracjami.

    Jeśli usuniesz zaznaczenie tej opcji, sprawy są przetwarzane w dokładnie tej samej kolejności przy każdym uruchomieniu potoku.

12. **Losowy numer materiału siewnego:** Wpisz wartość do użycia jako inicjatora, jeśli chcesz zapewnić powtarzalność w przebiegach tego samego potoku.

14. Podłącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych:](module-reference.md) 

    - Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj [modelu pociągu](train-model.md).  
  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

- Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 