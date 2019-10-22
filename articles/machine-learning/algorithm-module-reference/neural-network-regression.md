---
title: 'Regresja sieci neuronowych: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu regresja sieci neuronowych w usłudze Azure Machine Learning, aby utworzyć model regresji przy użyciu dostosowywalnego algorytmu sieciowego neuronowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 54518d21db0c9b14cbb7b4fc4316d1db2b871573
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692732"
---
# <a name="neural-network-regression-module"></a>Moduł regresji sieci neuronowych

*Tworzy model regresji przy użyciu algorytmu sieci neuronowych*  
  
 Kategoria: Machine Learning/zainicjuj model/regresję
  
## <a name="module-overview"></a>Przegląd modułu  

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu regresji przy użyciu dostosowywalnego algorytmu sieci neuronowych.
  
 Chociaż sieci neuronowych są szeroko znane do użycia w uczeniu głębokiego i modelowania złożonych problemów, takich jak rozpoznawanie obrazu, można je łatwo dostosować do problemów z regresją. Każda Klasa modeli statystycznych może być neuronowych sieci w przypadku używania wag adaptacyjnych i może przybliżać funkcje nieliniowe danych wejściowych. W ten sposób regresja sieci neuronowych jest przeznaczona do problemów, w których bardziej tradycyjny model regresji nie może pasować do rozwiązania.
  
 Neuronowych regresji sieci to nadzorowana Metoda uczenia i dlatego wymaga *oznakowanego zestawu danych*, który zawiera kolumnę etykieta. Ponieważ model regresji przewiduje wartość liczbową, kolumna etykiety musi być typem danych liczbowych.  
  
 Możesz nauczyć model, dostarczając model i otagowany zestaw danych jako dane wejściowe do [modelu uczenia](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych przykładów wejściowych.  
  
## <a name="configure-neural-network-regression"></a>Konfigurowanie regresji sieci neuronowych 

Sieci neuronowych można dostosowywać w szerokim stopniu. W tej sekcji opisano sposób tworzenia modelu przy użyciu dwóch metod:
  
+ [Tworzenie modelu sieci neuronowych przy użyciu architektury domyślnej](#bkmk_DefaultArchitecture)  
  
    Jeśli zaakceptujesz domyślną architekturę sieci neuronowych, użyj okienka **Właściwości** , aby ustawić parametry kontrolujące zachowanie sieci neuronowych, takie jak liczba węzłów w warstwie ukryta, szybkość uczenia i normalizacja.

    Zacznij tutaj, jeśli jesteś nowym neuronowych sieci. Moduł obsługuje wiele dostosowań, a także dostrajanie modelu bez dokładnej znajomości sieci neuronowych. 

+ Definiowanie niestandardowej architektury dla sieci neuronowych 

    Użyj tej opcji, jeśli chcesz dodać dodatkowe ukryte warstwy lub w pełni dostosować architekturę sieci, jej połączenia i funkcje aktywacji.
    
    Ta opcja jest Najlepsza, jeśli masz już doświadczenie w korzystaniu z sieci neuronowych. Język NET # jest używany do definiowania architektury sieci.  

##  <a name="bkmk_DefaultArchitecture"></a>Tworzenie modelu sieci neuronowych przy użyciu architektury domyślnej
  
1.  Dodaj moduł **regresji sieci neuronowych** do potoku w interfejsie. Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **regresja** . 
  
2. Wskaż, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.  

3.  W obszarze **Specyfikacja warstwy ukrytej**wybierz pozycję w **pełni połączony przypadek**. Ta opcja tworzy model przy użyciu domyślnej architektury sieci neuronowych, która dla modelu regresji sieci neuronowych ma następujące atrybuty:  
  
    + Sieć ma dokładnie jedną ukrytą warstwę.
    + Warstwa wyjściowa jest w pełni połączona z ukrytą warstwą, a Ukryta warstwa jest w pełni podłączona do warstwy wejściowej.
    + Liczbę węzłów w warstwie Ukryta można ustawić przez użytkownika (wartość domyślna to 100).  
  
    Ponieważ liczba węzłów w warstwie wejściowej jest określana na podstawie liczby funkcji w danych szkoleniowych, w modelu regresji może istnieć tylko jeden węzeł w warstwie wyjściowej.  
  
4. Dla **liczby ukrytych węzłów**wpisz liczbę ukrytych węzłów. Wartość domyślna to jedna ukryta warstwa z węzłami 100. (Ta opcja jest niedostępna, jeśli zdefiniujesz architekturę niestandardową przy użyciu programu NET #).
  
5.  W polu **stawka szkoleniowa**wpisz wartość określającą krok wykonany dla każdej iteracji przed poprawką. Większa wartość współczynnika uczenia może spowodować szybsze zbieżność modelu, ale może przekroczyć wartości minimalne.

6.  Dla **liczby iteracji uczenia**należy określić maksymalną liczbę przypadków, w których algorytm przetwarza przypadki szkoleniowe.

7.  Dla * * średnica początkowej wagi uczenia, wpisz wartość określającą wagi węzłów na początku procesu uczenia.

8.  Na czas **pędu**wpisz wartość, która ma zostać zastosowana podczas uczenia się jako waga na węzłach z poprzednich iteracji.

10. Wybierz opcję, **losowo przykłady**, aby zmienić kolejność przypadków między iteracjami. W przypadku zaznaczenia tej opcji przypadki są przetwarzane w dokładnie tym samym porządku przy każdym uruchomieniu potoku.
  
11. W przypadku **liczby losowej inicjatora**można opcjonalnie wpisać wartość, która ma być używana jako inicjator. Określanie wartości inicjatora jest przydatne, gdy chcesz zapewnić powtarzalność między przebiegami tego samego potoku.
  
13. Połącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych](module-reference.md): 
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj opcji [model uczenia](./train-model.md).  
  
   
14. Uruchamianie potoku.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu wraz z wagami funkcji pochodzącymi z uczenia i innymi parametrami sieci neuronowych, kliknij prawym przyciskiem myszy dane wyjściowe [modelu uczenia](./train-model.md)i wybierz polecenie **Wizualizuj**.  

+ Aby zapisać migawkę przeszkolonego modelu, kliknij prawym przyciskiem myszy **wyszkolony model** wyjściowy i wybierz pozycję **Zapisz jako model przeszkolony**. Ten model nie jest aktualizowany po kolejnych uruchomieniach tego samego potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 