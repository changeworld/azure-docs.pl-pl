---
title: 'Sieć Neuronowa Two-Class: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułów sieci neuronowych Two-Class w usłudze Azure Machine Learning w celu utworzenia modelu sieci neuronowej, który może służyć do prognozowania obiektu docelowego, który ma tylko dwie wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029223"
---
# <a name="two-class-neural-network-module"></a>Moduł Two-Class sieci neuronowych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modeli sieci neuronowych, który może służyć do prognozowania obiektu docelowego, który ma tylko dwie wartości.

Klasyfikacja za pomocą sieci neuronowych jest metodą uczenia nadzorowanego i dlatego wymaga *oznakowane zestawu danych*, która zawiera kolumnę etykiety. Na przykład można użyć tego modelu sieci neuronowej do przewidywania wyników binarne, takie jak czy pacjent ma pewne choroby, czy maszyna jest prawdopodobnie nastąpi ich awaria w określonym przedziale czasu.  

Po zdefiniowaniu modelu, szkoleń, zapewniając oznakowane zestawu danych i modelu jako dane wejściowe [uczenie modelu](./train-model.md). Następnie można uczonego modelu do prognozowania wartości dla nowych danych wejściowych.

### <a name="more-about-neural-networks"></a>Więcej informacji na temat sieci neuronowych

Sieć neuronowa jest zestaw połączonych warstw. Dane wejściowe są pierwszą warstwą i są podłączone do warstwy danych wyjściowych w grafie acyklicznym składające się z ważona krawędzie i węzłów.

Między warstwami wejściowych i wyjściowych można wstawić wiele warstw ukryte. Większość predykcyjnego zadań można osiągnąć prosty sposób za pomocą tylko jednego lub kilku warstw ukryte. Jednak ostatnich badań wykazało, że głębokich sieciach neuronowych (DNN) z wieloma warstwami można skutecznie złożonych zadań, takich jak rozpoznawania obrazów i mowy. Kolejne warstwy są używane do modelowania zwiększają poziom głębokość semantycznego.

Relacja między dane wejściowe i wyjściowe jest uzyskane od uczenie sieci neuronowej na dane wejściowe. Kierunek wykres rozpoczynające się od danych wejściowych za pośrednictwem ukrytej warstwie według następującego a warstwą danych wyjściowych. Wszystkie węzły w warstwie są połączone za ważona krawędzi do węzłów w następnej warstwy.

Do obliczenia produktu wyjściowego, sieci dla konkretnego danych wejściowych, wartość jest obliczana w każdym węźle w ukrytych warstw, a w warstwie danych wyjściowych. Wartość jest ustawiana przez obliczenie sumy ważonej wartości węzłów z poprzedniej warstwy. Funkcja aktywacji jest następnie stosowane do tej sumy ważonej.
  
## <a name="how-to-configure"></a>Jak skonfigurować

1.  Dodaj **sieci neuronowych Two-Class** modułu do eksperymentu. Można znaleźć w tym module w ramach **uczenia maszynowego**, **zainicjować**w **klasyfikacji** kategorii.  
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Wybierz tę opcję, jeśli wiesz już, jak chcesz skonfigurować modelu.  

3.  Aby uzyskać **ukryte specyfikacji warstwy**, wybierz typ architektury sieci, aby utworzyć.  
  
    -   **W pełni połączony przypadek**: Używa domyślnej architektury sieci neuronowych, zdefiniowane dla sieci neuronowych dwuklasowych w następujący sposób:
  
        -   Ma jeden ukrytej warstwie.
  
        -   Warstwa danych wyjściowych jest w pełni ukrytej warstwie według następującego, oraz ukrytej warstwie według następującego jest w pełni podłączony do warstwy danych wejściowych.
  
        -   Liczba węzłów w warstwie danych wejściowych jest równa liczbie funkcji w danych szkoleniowych.
  
        -   Liczba węzłów w ukrytej warstwie według następującego jest ustawiony przez użytkownika. Wartość domyślna to 100.
  
        -   Liczba węzłów jest równa liczbie klasy. Klasa dwóch sieci neuronowych oznacza to wszystkich danych wejściowych musi być zamapowane na jeden z dwóch węzłów w warstwie danych wyjściowych.

5.  Aby uzyskać **tempo uczenia**, określając rozmiar kroku utworzono każdej iteracji, zanim korekty. Większe wartości zawierające współczynnik może spowodować modelu, który ma być zbieżna szybciej, ale jej przekroczenia minimalne lokalnego.

6.  Dla **liczby iteracji uczenia**, określ maksymalną liczbę razy algorytm ma być przetwarzana przypadków szkoleniowych.

7.  Dla **początkowej learning przeprowadzi średnica**, określ wagi węzła na początku procesu uczenia.

8.  Aby uzyskać **tempa**, określ wagę do zastosowania podczas nauki do węzłów z poprzednich iteracji  

10. Wybierz **mieszania przykłady** możliwość mieszania przypadków między poszczególnymi iteracjami. Jeśli wyłączysz tę opcję, przypadki są przetwarzane w dokładnie takiej samej kolejności każdorazowo, gdy uruchomienie eksperymentu.
  
11. Dla **inicjatora liczb losowych**, wpisz wartość do użycia jako inicjatora.
  
     Określanie inicjatora wartość przydaje się zapewnić powtarzalność we wszystkich przebiegach aktualizacji tym samym eksperymencie.  W przeciwnym razie wartość Zegar systemu jest używana jako zalążek, co może powodować nieco różne wyniki w każdym razem, gdy uruchomienie eksperymentu.
  
13. Dodaj oznakowane zestaw danych do eksperymentu i połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](train-model.md) modułu.  
  
14. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu, wraz z funkcji wagi z szkolenia i inne parametry sieci neuronowych, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md)i wybierz **Visualize**.  

+ Aby zapisać migawkę trenowanego modelu, kliknij prawym przyciskiem myszy **modelu Trained** danych wyjściowych, a następnie wybierz pozycję **zapisania Trenowanego modelu**. Ten model nie jest aktualizowany na kolejnych uruchomień tym samym eksperymencie.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 
