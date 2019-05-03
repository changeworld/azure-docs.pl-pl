---
title: 'Multiclass Neural Network: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułów sieci neuronowych kontra w usłudze Azure Machine Learning w celu utworzenia modelu sieci neuronowej, który może służyć do prognozowania obiektu docelowego, który ma wiele wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029388"
---
# <a name="multiclass-neural-network-module"></a>Wieloklasowej modułów sieci neuronowych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modeli sieci neuronowych, który może służyć do prognozowania obiektu docelowego, który ma wiele wartości. 

Na przykład sieci neuronowych, tego rodzaju mogą być używane w zadaniach wizji złożonych komputera, takich jak rozpoznawanie cyfrę lub literę, Klasyfikacja dokumentów i funkcję rozpoznawania wzorców jako.

Klasyfikacja za pomocą sieci neuronowych jest metodą uczenia nadzorowanego i dlatego wymaga *oznakowane dataset* zawierającej kolumnę etykiety.

Można nauczenia modelu, zapewniając modelu i oznakowane zestawu danych jako dane wejściowe [uczenie modelu](./train-model.md). Następnie można uczonego modelu do prognozowania wartości dla nowe przykłady danych wejściowych.  

## <a name="about-neural-networks"></a>O sieci neuronowych

Sieć neuronowa jest zestaw połączonych warstw. Dane wejściowe są pierwszą warstwą i są podłączone do warstwy danych wyjściowych w grafie acyklicznym składające się z ważona krawędzie i węzłów.

Między warstwami wejściowych i wyjściowych można wstawić wiele warstw ukryte. Większość predykcyjnego zadań można osiągnąć prosty sposób za pomocą tylko jednego lub kilku warstw ukryte. Jednak ostatnich badań wykazało, że głębokich sieciach neuronowych (DNN) z wieloma warstwami można skutecznie złożonych zadań, takich jak rozpoznawania obrazów i mowy. Kolejne warstwy są używane do modelowania zwiększają poziom głębokość semantycznego.

Relacja między dane wejściowe i wyjściowe jest uzyskane od uczenie sieci neuronowej na dane wejściowe. Kierunek wykres rozpoczynające się od danych wejściowych za pośrednictwem ukrytej warstwie według następującego a warstwą danych wyjściowych. Wszystkie węzły w warstwie są połączone za ważona krawędzi do węzłów w następnej warstwy.

Do obliczenia produktu wyjściowego, sieci dla konkretnego danych wejściowych, wartość jest obliczana w każdym węźle w ukrytych warstw, a w warstwie danych wyjściowych. Wartość jest ustawiana przez obliczenie sumy ważonej wartości węzłów z poprzedniej warstwy. Funkcja aktywacji jest następnie stosowane do tej sumy ważonej.

## <a name="configure-multiclass-neural-network"></a>Konfigurowanie Wieloklasowej sieci neuronowych

1. Dodaj **sieci neuronowych kontra** modułu do eksperymentu w interfejsie. Można znaleźć w tym module w ramach **uczenia maszynowego**, **zainicjować**w **klasyfikacji** kategorii.

2. **Tworzenie trybu trainer**: Ta opcja umożliwia określenie sposobu modelu, który ma być uczony:

    - **Pojedynczy parametr**: Wybierz tę opcję, jeśli wiesz już, jak chcesz skonfigurować modelu.

    

3. **Ukryte specyfikacji warstwy**: Wybierz typ architektury sieci do utworzenia.

    - **W pełni połączony przypadek**: Wybierz tę opcję, aby utworzyć model przy użyciu architektury sieci neuronowych domyślne. Modele sieci neuronowych wieloklasowej wartości domyślne są następujące:

        - Ukrytej warstwie według jedną
        - Warstwa danych wyjściowych w pełni jest podłączony do ukrytej warstwie.
        - Ukrytej warstwie według następującego podłączona do warstwy danych wejściowych.
        - Liczba węzłów w warstwie danych wejściowych jest określana przez liczbę funkcji w danych szkoleniowych.
        - Można ustawić liczbę węzłów w ukrytej warstwie według następującego przez użytkownika. Wartość domyślna to 100.
        - Liczba węzłów w warstwie danych wyjściowych jest zależna od liczby klas.
  
   

5. **Liczba ukrytych węzłów**: Ta opcja umożliwia dostosowanie Liczba ukrytych węzłów w architekturze domyślne. Wpisz liczbę ukrytych węzłów. Wartość domyślna to jeden ukrytej warstwie według następującego ze 100 węzłów.

6. **Tempo uczenia**: Określ rozmiar kroku utworzono każdej iteracji, zanim korekty. Większe wartości zawierające współczynnik może spowodować modelu, który ma być zbieżna szybciej, ale jej przekroczenia minimalne lokalnego.

7. **Liczba iteracji uczenia**: Określ maksymalną liczbę razy algorytm ma być przetwarzana przypadków szkoleniowych.

8. **Początkowa learning przeprowadzi średnica**: Określ wagi węzła na początku procesu uczenia.

9. **Pęd**: Określ wagę do zastosowania podczas nauki do węzłów z poprzednich iteracji.
  
11. **Shuffle przykłady**: Wybierz tę opcję, aby mieszania przypadków między poszczególnymi iteracjami.

    Jeśli wyłączysz tę opcję, przypadki są przetwarzane w dokładnie takiej samej kolejności każdorazowo, gdy uruchomienie eksperymentu.

12. **Inicjator losowy numer**: Wpisz wartość do użycia jako inicjator, jeśli chcesz zapewnić powtarzalność we wszystkich przebiegach aktualizacji tym samym eksperymencie.

14. Połącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych](module-reference.md): 

    - Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](train-model.md).  
  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

- Aby wyświetlić podsumowanie parametrów modelu, wraz z funkcji wagi z szkolenia i inne parametry sieci neuronowych, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) i wybierz **Visualize**.  

- Aby zapisać migawkę trenowanego modelu, kliknij prawym przyciskiem myszy **modelu Trained** danych wyjściowych, a następnie wybierz pozycję **zapisania Trenowanego modelu**. Ten model nie jest aktualizowany na kolejnych uruchomień tym samym eksperymencie.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 