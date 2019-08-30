---
title: 'Multiclass Neural Network: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać wieloklasowego modułu sieci neuronowych w usłudze Azure Machine Learning, aby utworzyć model sieci neuronowych, który może służyć do przewidywania obiektu docelowego, który ma wiele wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128596"
---
# <a name="multiclass-neural-network-module"></a>Wieloklasowy moduł sieci neuronowych

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu sieci neuronowych, który może służyć do przewidywania celu, który ma wiele wartości. 

Na przykład sieci neuronowych tego rodzaju mogą być używane w złożonych zadaniach przetwarzania obrazów, takich jak rozpoznawanie cyfr lub liter, klasyfikacja dokumentu i Rozpoznawanie wzorców.

Klasyfikacja przy użyciu sieci neuronowych to nadzorowana Metoda uczenia i dlatego wymaga oznakowanego *zestawu danych* , który zawiera kolumnę etykieta.

Możesz nauczyć model, dostarczając model i otagowany zestaw danych jako dane wejściowe do [modelu uczenia](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych przykładów wejściowych.  

## <a name="about-neural-networks"></a>Informacje o sieciach neuronowych

Sieć neuronowych to zestaw wzajemnie połączonych warstw. Dane wejściowe są pierwszą warstwą i są połączone z warstwą wyjściową przez wykres acykliczne, składający się z ważonych krawędzi i węzłów.

Między warstwami wejściowymi i wyjściowymi można wstawiać wiele ukrytych warstw. Większość zadań predykcyjnych można łatwo wykonać przy użyciu tylko jednej lub kilku ukrytych warstw. Jednak ostatnie badania wykazały, że głębokie neuronowych Networks (DNN) z wieloma warstwami mogą być skuteczne w złożonych zadaniach, takich jak rozpoznawanie obrazów lub mowy. Kolejne warstwy są używane do modelowania rosnącego poziomu głębokości semantycznej.

Relacja między danymi wejściowymi i wyjściowymi jest uzyskiwana z uczenia sieci neuronowych w danych wejściowych. Kierunek wykresu przechodzi z danych wejściowych za pośrednictwem warstwy Ukryta i do warstwy wyjściowej. Wszystkie węzły w warstwie są połączone przy użyciu ważonych krawędzi do węzłów w następnej warstwie.

Aby obliczyć dane wyjściowe sieci dla konkretnych danych wejściowych, wartość jest obliczana w każdym węźle ukrytych warstw i w warstwie wyjściowej. Wartość jest ustawiana przez obliczenie ważonej sumy wartości węzłów z poprzedniej warstwy. Funkcja aktywacji zostanie następnie zastosowana do tej kwoty ważonej.

## <a name="configure-multiclass-neural-network"></a>Konfiguruj wieloklasową sieć neuronowych

1. Dodaj wieloklasowy moduł **sieci neuronowych** do eksperymentu w interfejsie. Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **Klasyfikacja** .

2. **Utwórz tryb Trainer**: Użyj tej opcji, aby określić, w jaki sposób model ma być szkolony:

    - **Pojedynczy parametr**: Wybierz tę opcję, Jeśli wiesz już, jak chcesz skonfigurować model.

    

3. **Ukryta Specyfikacja warstwy**: Wybierz typ architektury sieci do utworzenia.

    - W **pełni połączony przypadek**: Wybierz tę opcję, aby utworzyć model przy użyciu domyślnej architektury sieci neuronowych. W przypadku modeli sieci z wieloklasową neuronowych wartości domyślne są następujące:

        - Jedna ukryta warstwa
        - Warstwa wyjściowa jest w pełni połączona z ukrytą warstwą.
        - Warstwa ukryta jest w pełni podłączona do warstwy wejściowej.
        - Liczba węzłów w warstwie wejściowej jest określana na podstawie liczby funkcji w danych szkoleniowych.
        - Liczba węzłów w warstwie Ukryta może być ustawiana przez użytkownika. Wartość domyślna to 100.
        - Liczba węzłów w warstwie wyjściowej zależy od liczby klas.
  
   

5. **Liczba ukrytych węzłów**: Ta opcja umożliwia dostosowanie liczby ukrytych węzłów w architekturze domyślnej. Wpisz liczbę ukrytych węzłów. Wartość domyślna to jedna ukryta warstwa z węzłami 100.

6. **Stawka szkoleniowa**: Zdefiniuj rozmiar kroku wykonany dla każdej iteracji przed poprawką. Większa wartość współczynnika uczenia może spowodować szybsze zbieżność modelu, ale może przekroczyć wartości minimalne.

7. **Liczba iteracji szkoleniowych**: Określ maksymalną liczbę przypadków, w których algorytm powinien przetwarzać przypadki szkoleniowe.

8. **Średnica początkowej wagi uczenia**: Określ wagi węzłów na początku procesu uczenia.

9. **Wartość pędu**: Określ wagę do zastosowania podczas uczenia się do węzłów z poprzednich iteracji.
  
11. **Przykłady losowe**: Wybierz tę opcję, aby losowo rozróżniać przypadki między iteracjami.

    W przypadku zaznaczenia tej opcji przypadki są przetwarzane w dokładnie tym samym porządku przy każdym uruchomieniu eksperymentu.

12. **Inicjator liczb losowych**: Wpisz wartość, która ma być używana jako inicjator, jeśli chcesz zapewnić powtarzalność między przebiegami tego samego eksperymentu.

14. Połącz zestaw danych szkoleniowych i jeden z [modułów szkoleniowych](module-reference.md): 

    - W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj opcji [model uczenia](train-model.md).  
  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

- Aby wyświetlić podsumowanie parametrów modelu wraz z wagami funkcji pochodzącymi z uczenia i innymi parametrami sieci neuronowych, kliknij prawym przyciskiem myszy dane wyjściowe [modelu uczenia](./train-model.md) i wybierz polecenie **Wizualizuj**.  

- Aby zapisać migawkę przeszkolonego modelu, kliknij prawym przyciskiem myszy **wyszkolony model** wyjściowy i wybierz pozycję **Zapisz jako model przeszkolony**. Ten model nie jest aktualizowany po kolejnych uruchomieniach tego samego eksperymentu.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 