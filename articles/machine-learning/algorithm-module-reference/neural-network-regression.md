---
title: 'Regresja sieci neuronowej: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu neuronowych regresji sieci w usłudze Azure Machine Learning, aby utworzyć model regresji, przy użyciu algorytmu sieć neuronowa można dostosowywać...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029313"
---
# <a name="neural-network-regression-module"></a>Neuronowych modułu regresji sieci

*Tworzy model regresji, przy użyciu algorytmu sieć neuronowa*  
  
 Kategoria: Uczenie maszynowe / zainicjować modelu / regresji
  
## <a name="module-overview"></a>Omówienie modułu  

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu regresji przy użyciu algorytmu sieć neuronowa można dostosowywać.
  
 Sieci neuronowe na potrzeby uczenia głębokiego i modelowanie złożonych problemów, takich jak rozpoznawanie obrazów są powszechnie znane są one łatwo dostosować do problemów regresji. Każdej klasy modeli statystyczne można określane jako sieci neuronowych jeśli ich wagi adaptacyjne przybliżyć nieliniowych funkcji ich dane wejściowe. Ten sposób sieć neuronowa regresji nadaje się gdzie bardziej tradycyjnego modelu regresji nie mieści się w rozwiązaniu problemów.
  
 Sieć neuronowa regresji jest metodą uczenia nadzorowanego i dlatego wymaga *oznakowane zestawu danych*, który znajduje się kolumna etykiety. Ponieważ model regresji przewiduje wartość liczbową, kolumna etykiety musi być typu danych liczbowych.  
  
 Można nauczenia modelu, zapewniając modelu i oznakowane zestawu danych jako dane wejściowe [uczenie modelu](./train-model.md). Następnie można uczonego modelu do prognozowania wartości dla nowe przykłady danych wejściowych.  
  
## <a name="configure-neural-network-regression"></a>Konfigurowanie sieci neuronowych regresji 

Często można dostosować sieci neuronowych. W tej sekcji opisano sposób tworzenia modelu przy użyciu dwóch metod:
  
+ [Tworzenie modelu sieci neuronowej przy użyciu architektury domyślne](#bkmk_DefaultArchitecture)  
  
    Jeśli zdecydujesz się zaakceptować domyślne architektury sieci neuronowych, użyj **właściwości** okienko, aby ustawić parametry, które kontrolują zachowanie sieci neuronowych, takie jak liczba węzłów w ukrytej warstwie według następującego, tempo uczenia i normalizacji.

    Jeśli dopiero zaczynasz korzystać z sieci neuronowych, zacznij tutaj. Ten moduł umożliwia wielu dostosowań, a także modelu strojenia, bez dogłębnej wiedzy na temat sieci neuronowych. 

+ Zdefiniuj niestandardową architekturę sieci neuronowych 

    Użyj tej opcji, jeśli chcesz dodać dodatkowych ukrytych warstw, lub do pełnego dostosowania funkcje architektury, jego połączenia i aktywacji sieci.
    
    Ta opcja jest przydatna, jeśli znasz już nieco sieci neuronowych. Język Net # umożliwia definiowanie architektury sieci.  

##  <a name="bkmk_DefaultArchitecture"></a> Tworzenie modelu sieci neuronowej przy użyciu architektury domyślne
  
1.  Dodaj **neuronowych regresji sieci** modułu do eksperymentu w interfejsie. Można znaleźć w tym module w ramach **uczenia maszynowego**, **zainicjować**w **regresji** kategorii. 
  
2. Wskazać, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Wybierz tę opcję, jeśli wiesz już, jak chcesz skonfigurować modelu.  

3.  W **ukryte specyfikacji warstwy**, wybierz opcję **nawiązane pełne połączenie przypadek**. Ta opcja tworzy model przy użyciu domyślnego architektury sieci neuronowych, mającej modelu regresji sieci neuronowych, te atrybuty:  
  
    + Sieci ma dokładnie jednego ukrytej warstwie.
    + Warstwa danych wyjściowych jest w pełni ukrytej warstwie według następującego oraz ukrytej warstwie według następującego jest w pełni podłączony do warstwy danych wejściowych.
    + Liczbę węzłów w ukrytej warstwie według następującego mogą być ustawione przez użytkownika (wartość domyślna to 100).  
  
    Ponieważ węzły w warstwie danych wejściowych jest określana przez liczbę funkcji dane szkoleniowe, w modelu regresji może istnieć tylko jeden węzeł w warstwie danych wyjściowych.  
  
4. Aby uzyskać **Liczba ukrytych węzłów**, wpisz liczbę ukrytych węzłów. Wartość domyślna to jeden ukrytej warstwie według następującego ze 100 węzłów. (Ta opcja nie jest dostępna, jeśli zdefiniujesz niestandardową architekturę przy użyciu Net #)
  
5.  Aby uzyskać **tempo uczenia**, wpisz wartość, która definiuje kroku utworzono każdej iteracji, zanim korekty. Większe wartości zawierające współczynnik może spowodować modelu, który ma być zbieżna szybciej, ale jej przekroczenia minimalne lokalnego.

6.  Aby uzyskać **liczby iteracji uczenia**, określ maksymalną liczbę razy algorytm przetwarza przypadków szkoleniowych.

7.  Aby uzyskać ** początkowej learning przeprowadzi średnicy, wpisz wartość, która określa wagi węzła na początku procesu uczenia.

8.  Aby uzyskać **tempa**, wpisz wartość do zastosowania podczas nauki jako wagi w węzłach z poprzednich iteracji.

10. Wybierz opcję **mieszania przykłady**, aby zmienić kolejność przypadków między poszczególnymi iteracjami. Jeśli wyłączysz tę opcję, przypadki są przetwarzane w dokładnie takiej samej kolejności każdorazowo, gdy uruchomienie eksperymentu.
  
11. Aby uzyskać **inicjatora liczb losowych**, możesz opcjonalnie wpisać wartość do użycia jako inicjatora. Określanie inicjatora wartość przydaje się zapewnić powtarzalność we wszystkich przebiegach aktualizacji tym samym eksperymencie.
  
13. Połącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych](module-reference.md): 
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](./train-model.md).  
  
   
14. Uruchom eksperyment.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu, wraz z funkcji wagi z szkolenia i inne parametry sieci neuronowych, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md)i wybierz **Visualize**.  

+ Aby zapisać migawkę trenowanego modelu, kliknij prawym przyciskiem myszy **modelu Trained** danych wyjściowych, a następnie wybierz pozycję **zapisania Trenowanego modelu**. Ten model nie jest aktualizowany na kolejnych uruchomień tym samym eksperymencie.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 