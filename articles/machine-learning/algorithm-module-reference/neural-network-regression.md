---
title: 'Regresja sieci neuronowej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji sieci neuronowej w usłudze Azure Machine Learning do utworzenia modelu regresji przy użyciu dostosowywanego algorytmu sieci neuronowej.Learn how to use the Neural Network Regression module in Azure Machine Learning to create a regression model using a customizable neural network algorithm.Learn how to use the Neural Network Regression module in Azure Machine Learning to create a regression model using a customizable neural network algorithm
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456102"
---
# <a name="neural-network-regression-module"></a>Moduł regresji sieci neuronowej

*Tworzy model regresji przy użyciu algorytmu sieci neuronowej*  
  
 Kategoria: Uczenie maszynowe / Inicjalizacja modelu / regresja
  
## <a name="module-overview"></a>Omówienie modułu  

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu regresji przy użyciu konfigurowalny algorytm sieci neuronowej.
  
 Chociaż sieci neuronowe są powszechnie znane do stosowania w uczeniu głębokim i modelowania złożonych problemów, takich jak rozpoznawanie obrazu, są one łatwo dostosowane do problemów regresji. Dowolną klasę modeli statystycznych można nazwać siecią neuronową, jeśli używają adaptacyjnych wag i mogą przybliżyć nieliniowe funkcje swoich wejść. W związku z tym regresja sieci neuronowej jest dostosowana do problemów, w których bardziej tradycyjny model regresji nie może zmieścić rozwiązania.
  
 Regresja sieci neuronowej jest nadzorowaną metodą uczenia się i dlatego wymaga *oznakowanego zestawu danych*, który zawiera kolumnę etykiety. Ponieważ model regresji przewiduje wartość liczbową, kolumna etykiety musi być typem danych liczbowych.  
  
 Model można trenować, udostępniając model i oznakowany zestaw danych jako dane wejściowe do [modelu pociągu](./train-model.md). Przeszkolony model może następnie służyć do przewidywania wartości dla nowych przykładów wejściowych.  
  
## <a name="configure-neural-network-regression"></a>Konfigurowanie regresji sieci neuronowej 

Sieci neuronowe można w znacznym stopniu dostosować. W tej sekcji opisano sposób tworzenia modelu przy użyciu dwóch metod:
  
+ [Tworzenie modelu sieci neuronowej przy użyciu architektury domyślnej](#bkmk_DefaultArchitecture)  
  
    Jeśli zaakceptujesz domyślną architekturę sieci neuronowej, użyj okienka **Właściwości,** aby ustawić parametry, które kontrolują zachowanie sieci neuronowej, takie jak liczba węzłów w warstwie ukrytej, szybkość uczenia się i normalizacja.

    Zacznij tutaj, jeśli jesteś nowy w sieciach neuronowych. Moduł obsługuje wiele dostosowań, a także dostrajanie modelu, bez głębokiej wiedzy na temat sieci neuronowych. 

+ Definiowanie architektury niestandardowej dla sieci neuronowej 

    Użyj tej opcji, jeśli chcesz dodać dodatkowe ukryte warstwy lub w pełni dostosować architekturę sieci, jej połączenia i funkcje aktywacji.
    
    Ta opcja jest najlepsza, jeśli jesteś już nieco zaznajomiony z sieciami neuronowymi. Do zdefiniowania architektury sieci użyj języka Net#.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Tworzenie modelu sieci neuronowej przy użyciu architektury domyślnej

1.  Dodaj moduł **regresji sieci neuronowej** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Uczenie maszynowe**, **Inicjalizuj**, w kategorii **Regresja.** 
  
2. Wskaż sposób trenowania modelu, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.

    -   **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.   

3.  W **obszarze Specyfikacja warstwy ukryte**wybierz opcję W pełni **połączona obudowa**. Ta opcja tworzy model przy użyciu domyślnej architektury sieci neuronowej, która dla modelu regresji sieci neuronowej ma następujące atrybuty:  
  
    + Sieć ma dokładnie jedną ukrytą warstwę.
    + Warstwa wyjściowa jest w pełni połączona z warstwą ukrytą, a warstwa ukryta jest w pełni połączona z warstwą wejściową.
    + Liczba węzłów w warstwie ukrytej może być ustawiona przez użytkownika (wartość domyślna to 100).  
  
    Ponieważ liczba węzłów w warstwie wejściowej jest określana przez liczbę obiektów w danych szkoleniowych, w modelu regresji może istnieć tylko jeden węzeł w warstwie wyjściowej.  
  
4. W **polach Liczba ukrytych węzłów**wpisz liczbę ukrytych węzłów. Wartość domyślna to jedna ukryta warstwa ze 100 węzłami. (Ta opcja nie jest dostępna, jeśli architektura niestandardowa jest definiowana przy użyciu sieci Net#.)
  
5.  W polu **Learning rate**wpisz wartość definiują krok wykonany przy każdej iteracji przed korektą. Większa wartość dla szybkości uczenia się może spowodować, że model zbiega się szybciej, ale może przekroczyć lokalne minima.

6.  W przypadku **liczby iteracji uczenia się**określ maksymalną liczbę przypadków przetwarzania przez algorytm przypadków szkolenia.


8.  W polu **Pęd**wpisz wartość, która ma być stosowana podczas nauki jako waga węzłów z poprzednich iteracji.

10. Wybierz opcję **Tasuj przykłady**, aby zmienić kolejność spraw między iteracjami. Jeśli usuniesz zaznaczenie tej opcji, sprawy są przetwarzane w dokładnie tej samej kolejności przy każdym uruchomieniu potoku.
  
11. W polu **Ilość materiału siewnego liczb losowych**można opcjonalnie wpisać wartość używaną jako materiał siewny. Określanie wartości inicjatora jest przydatne, gdy chcesz zapewnić powtarzalność w przebiegach tego samego potoku.
  
13. Podłącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych:](module-reference.md) 
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj [modelu pociągu](./train-model.md).  
  
   
14. Prześlij potok.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

- Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 