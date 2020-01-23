---
title: 'Oceń model: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu oceny modelu w Azure Machine Learning do mierzenia dokładności przeszkolonego modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: a906e5f354f332cebb0656c6fc40b17c8a5016a2
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546694"
---
# <a name="evaluate-model-module"></a>Oceń moduł modelu

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do mierzenia dokładności nauczonego modelu. Dostarczasz zestaw danych zawierający wyniki wygenerowane na podstawie modelu, a moduł **oceny modelu** obliczy zestaw metryk oceny standardowej w branży.
  
 Metryki zwracane przez **ocenę modelu** zależą od typu ocenianego modelu:  
  
-   **Modele klasyfikacji**    
-   **Modele regresji**    


> [!TIP]
> Jeśli dopiero zaczynasz korzystać z wersji ewaluacyjnej modelu, zalecamy korzystanie z serii wideo przez Dr. Stephen Elston w ramach [kursu uczenia maszynowego](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) z usługi EdX. 


Istnieją trzy sposoby używania modułu **szacowania modelu** :

+ Generuj wyniki na danych szkoleniowych i oceniaj model na podstawie tych wyników
+ Generuj wyniki na modelu, ale Porównaj te wyniki z wynikami w zestawie testów zarezerwowanych
+ Porównanie wyników dla dwóch różnych, ale powiązanych modeli, przy użyciu tego samego zestawu danych

## <a name="use-the-training-data"></a>Korzystanie z danych szkoleniowych

Aby oszacować model, należy połączyć zestaw danych, który zawiera zestaw kolumn wejściowych i wyników.  Jeśli żadne inne dane nie są dostępne, możesz użyć oryginalnego zestawu danych.

1. Podłącz **wynikowy zestaw danych** wyjściowych z [modelu wynikowego](./score-model.md) do danych wejściowych **oceny modelu**. 
2. Kliknij pozycję **Oceń moduł modelu** i uruchom potok, aby wygenerować wyniki oceny.

## <a name="use-testing-data"></a>Korzystanie z danych testowych

Typowy scenariusz w uczeniu maszynowym polega na rozdzieleniu oryginalnego zestawu danych na szkolenia i testowanie zestawów DataSet przy użyciu modułu [Split](./split-data.md) lub [partycji i przykładowego](./partition-and-sample.md) modułu. 

1. Podłącz **wynikowy zestaw danych** wyjściowych z [modelu wynikowego](score-model.md) do danych wejściowych **oceny modelu**. 
2. Połącz dane wyjściowe modułu Split Data, który zawiera dane testowe do prawej strony **oceny modelu**.
2. Kliknij pozycję **Oceń moduł modelu** i wybierz pozycję **Uruchom wybrane** , aby wygenerować wyniki oceny.

## <a name="compare-scores-from-two-models"></a>Porównaj wyniki z dwóch modeli

Możesz również połączyć drugi zestaw wyników do **oceny modelu**.  Wyniki mogą być udostępnionym zestawem ewaluacyjnym ze znanymi wynikami lub zestawem wyników z innego modelu dla tych samych danych.

Ta funkcja jest przydatna, ponieważ można łatwo porównać wyniki z dwóch różnych modeli na tych samych danych. Lub można porównać wyniki z dwóch różnych przebiegów nad tymi samymi danymi z różnymi parametrami.

1. Podłącz **wynikowy zestaw danych** wyjściowych z [modelu wynikowego](score-model.md) do danych wejściowych **oceny modelu**. 
2. Połącz dane wyjściowe modułu modelu wynikowego dla drugiego modelu z prawej strony **oceny modelu**.
3. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po uruchomieniu **oceny modelu**kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj wyniki oceny** , aby wyświetlić wyniki.

W przypadku łączenia zestawów danych z obydwoma danymi wejściowymi **modelu szacowania**wyniki będą zawierać metryki dla zestawu danych lub obu modeli.
Model lub dane dołączone do lewego portu są przedstawiane jako pierwsze w raporcie, a następnie metryki dla zestawu danych lub modelu dołączonego do właściwego portu.  

Na przykład poniższa ilustracja przedstawia porównanie wyników z dwóch modeli klastrowania utworzonych na podstawie tych samych danych, ale z innymi parametrami.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Ponieważ jest to Model klastrowania, wyniki oceny różnią się od tego, czy porównano wyniki z dwóch modeli regresji, czy porównano dwa modele klasyfikacji. Jednak ogólna prezentacja jest taka sama. 

## <a name="metrics"></a>Metryki

W tej sekcji opisano metryki zwracane dla określonych typów modeli obsługiwanych do użycia z **modelem oszacowania**:

+ [modele klasyfikacji](#bkmk_classification)
+ [modele regresji](#bkmk_regression)

###  <a name="bkmk_classification"></a>Metryki dla modeli klasyfikacji

Podczas oceniania modeli klasyfikacji są raportowane następujące metryki. W przypadku porównywania modeli są one klasyfikowane według metryki wybranej do oceny.  
  
-   **Dokładność** mierzy dobrą jakość modelu klasyfikacji jako proporcje prawdziwych wyników do łącznych przypadków.  
  
-   **Precyzja** jest proporcją prawdziwych wyników dla wszystkich pozytywnych wyników.  
  
-   Funkcja **odwoływania** jest częścią wszystkich poprawnych wyników zwracanych przez model.  
  
-   **F-Score** jest obliczana jako średnia ważona precyzji i odzyskanie między 0 i 1, gdzie idealna wartość F-score to 1.  
  
-   **AUC** mierzy obszar pod krzywą na podstawie prawdziwej liczby dodatniej na osi y i fałszywych wartości dodatnich na osi x. Ta Metryka jest przydatna, ponieważ udostępnia pojedynczą liczbę, która umożliwia porównywanie modeli różnych typów.  
  
- **Średnia utrata dzienników** to pojedynczy wynik służący do wyrażania grzywny dla nieprawidłowych wyników. Jest ona obliczana jako różnica między dwoma rozkładami prawdopodobieństwa — wartość true i jeden w modelu.  
  
- **Utrata dzienników szkoleń** to pojedynczy wynik, który reprezentuje zalety klasyfikatora w przypadku prognoz losowych. Utrata dziennika mierzy niepewność modelu, porównując prawdopodobieństwa, które wyprowadza do znanych wartości (podstawa prawdy) w etykietach. Chcesz zminimalizować utratę dzienników dla modelu jako całości.

##  <a name="bkmk_regression"></a>Metryki dla modeli regresji
 
Metryki zwracane dla modeli regresji zaprojektowano w celu oszacowania ilości błędu.  Model jest traktowany jak dopasować dane, jeśli różnica między wartościami obserwowanymi a przewidywanymi jest mała. Jednak, patrząc na wzorzec reszt (różnica między dowolnym przewidzianym punktem a odpowiadającą jej wartością rzeczywistą), może postanowić dużo o potencjalną bias w modelu.  
  
 Następujące metryki są zgłaszane do oceny modeli regresji. Porównując modele, są one klasyfikowane według metryki wybranej do oceny.  
  
- **Średnia wartość błędu bezwzględnego (Mae)** mierzy, jak blisko prognoz są rzeczywiste wyniki; w rezultacie niższy jest lepszy.  
  
- **Element główny średniej wartości kwadratowej (RMSE)** tworzy pojedynczą wartość, która podsumowuje błąd w modelu. Podniesienie różnica polega na tym, że Metryka nie uwzględnia różnicy między przekroczeniem prognozowania a podpowiadaniem.  
  
- **Względny błąd względny (Rae)** jest względną absolutną różnicą między wartościami oczekiwanymi i rzeczywistymi. względne, ponieważ średnia różnica jest dzielona przez średnią arytmetyczną.  
  
- **Względny kwadratowy błąd (RSE)** podobnie normalizuje łączny kwadratowy błąd przewidywanych wartości przez podzielenie przez łączny kwadrat błędów rzeczywistych wartości.  
  
- **Średnia zero jeden błąd (mzoe)** wskazuje, czy Prognoza była poprawna.  Innymi słowy: `ZeroOneLoss(x,y) = 1`, gdy `x!=y`; w przeciwnym razie `0`.
  
- **Współczynnik wyznaczania**, często określany jako R<sup>2</sup>, reprezentuje siłę predykcyjną modelu jako wartość z przedziału od 0 do 1. Zero oznacza, że model jest losowo (wyjaśnia nic); 1 oznacza, że jest idealnym dopasowaniem. Należy zachować ostrożność w interpretacji wartości R<sup>2</sup> , ponieważ niskie wartości mogą być całkowicie normalne i mogą być podejrzane wysokie wartości.
  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 