---
title: 'Ocena modelu: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu Evaluate Model w usłudze Azure Machine Learning do mierzenia dokładności uczonego modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028743"
---
# <a name="evaluate-model-module"></a>Ocena modelu modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do mierzenia dokładności uczonego modelu. Należy podać zestaw danych zawierający wyniki generowane na podstawie modelu i **Evaluate Model** modułu oblicza zestawu metryk oceny będące standardami branżowymi.
  
 Zwracane przez metryki **Evaluate Model** zależą od typu modelu, który jest dokonanie oceny oprogramowania:  
  
-   **Modele klasyfikacji**    
-   **Modele regresji**    



> [!TIP]
> Jeśli jesteś nowym użytkownikiem oceny modelu, firma Microsoft zaleca serii filmów wideo, odzyskiwania po awarii. Autor: Stephen Elston, jako część [machine learning kurs](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) z EdX. 


Istnieją trzy sposoby na wykorzystanie **Evaluate Model** modułu:

+ Generuj wyniki za pośrednictwem danych szkoleniowych i ocenę modelu, w oparciu o te wyniki
+ Generuj wyniki na podstawie modelu, ale Porównaj te wyniki do oceny na zarezerwowanych zestawu testowego
+ Porównaj wyniki dla dwóch różnych, ale powiązanych modeli, przy użyciu tego samego zestawu danych

## <a name="use-the-training-data"></a>Użyj danych szkoleniowych

Aby ocenić modelu, możesz łączyć z zestawu danych, który zawiera zbiór kolumny wejściowe i wyniki.  Jeśli żadne inne dane nie są dostępne, możesz użyć oryginalnego zestawu danych.

1. Połącz **oceniane dataset** danych wyjściowych [Score Model](./score-model.md) w danych wejściowych **Evaluate Model**. 
2. Kliknij przycisk **Evaluate Model** modułu, a następnie uruchom eksperyment, aby wygenerować wyniki oceny.

## <a name="use-testing-data"></a>Korzystanie z danych testowych

Typowy scenariusz w usłudze machine learning polega na rozdzieleniu oryginalnego zestawu danych do szkolenia i testowania zestawów danych, przy użyciu [podziału](./split-data.md) moduł, lub [partycja i próbka](./partition-and-sample.md) modułu. 

1. Połącz **oceniane dataset** danych wyjściowych [Score Model](score-model.md) w danych wejściowych **Evaluate Model**. 
2. Połącz wyjście modułu dzielenia danych, który zawiera dane badania mają być po prawej stronie dane wejściowe **Evaluate Model**.
2. Kliknij przycisk **Evaluate Model** modułu, a następnie wybierz **Uruchom wybrane** do generowania wyników oceny.

## <a name="compare-scores-from-two-models"></a>Porównaj wyniki z dwóch modeli

Można też połączyć drugiego zestawu wyników, aby **Evaluate Model**.  Wyniki mogą być zestaw oceny udostępnionego, który ma znane wyniki lub zestawu wyników z innego modelu dla tych samych danych.

Ta funkcja jest przydatna, ponieważ łatwo można porównać wyniki z dwóch różnych modeli na tych samych danych. Możesz też może porównać wyniki z dwóch różnych tras za pośrednictwem tych samych danych z innymi parametrami.

1. Połącz **oceniane dataset** danych wyjściowych [Score Model](score-model.md) w danych wejściowych **Evaluate Model**. 
2. Połącz wyjście modułu Score Model dla drugiego modelu po prawej stronie wejściem **Evaluate Model**.
3. Kliknij prawym przyciskiem myszy **Evaluate Model**i wybierz **Uruchom wybrane** do generowania wyników oceny.

## <a name="results"></a>Wyniki

Po uruchomieniu **Evaluate Model**, kliknij prawym przyciskiem myszy moduł i wybierz **wyniki oceny** aby zobaczyć wyniki. Możesz:

+ Zapisywanie wyników jako zestaw danych, do prostszych analiz z innymi narzędziami
+ Generowanie wizualizacji w interfejsie

Jeśli chcesz nawiązać połączenie zestawów danych zarówno dane wejściowe **Evaluate Model**, wyniki będzie zawierać metryki dla obu zestawów danych lub oba modele.
Model lub danych dołączonych do portu po lewej stronie są prezentowane najpierw w raporcie, następuje metryki dla zestawu danych lub model dołączony na prawy port.  

Na przykład poniższy obraz przedstawia porównanie wyników z dwóch modeli klastrowania, które zostały utworzone w tych samych danych, ale z różnymi parametrami.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Ponieważ jest to model klastrowania, wyniki oceny są inne niż możesz porównać wyniki z dwóch modele regresji, czy dwa modele klasyfikacji w porównaniu. Jednak ogólny wygląd jest taka sama. 

## <a name="metrics"></a>Metryki

W tej sekcji opisano metryki dla określonych typów modeli obsługiwane do użytku z programem **Evaluate Model**:

+ [modele klasyfikacji](#bkmk_classification)
+ [modele regresji](#bkmk_regression)

###  <a name="bkmk_classification"></a> Metryki dla modeli klasyfikacji

Następujące metryki są zgłaszane podczas oceniania modeli klasyfikacji. Jeśli porównasz modeli one są uporządkowane według metrykę, wybrany dla oceny.  
  
-   **Dokładność** mierzy zgodność model klasyfikacji jako część true wyniki, aby łączna liczba przypadków.  
  
-   **Dokładność** to true wyników za pośrednictwem wszystkich pozytywnych wyników.  
  
-   **Odwołaj** jest ułamek wszystkie poprawne wyniki zwracane przez model.  
  
-   **Wynik F** jest obliczany jako średnia ważona dokładności i wycofaniu zakresu od 0 do 1, gdzie idealna wartość F wynik jest 1.  
  
-   **AUC** miary powierzchni pod krzywą wykreślić z true alarmów na y osi i wartość false alarmów na osi x. Ta metryka jest przydatne, ponieważ zawiera ono pojedyncza liczba, która umożliwia porównywanie modele o różnych typach.  
  
- **Średnia strata dziennika** jest pojedynczy wynik, stosowany w celu kary za błędne wyniki. Jest obliczana jako różnica między dwa dystrybucje prawdopodobieństwo — co prawda i jeden w modelu.  
  
- **Szkolenie utraty dziennika** jest pojedynczy wynik, który reprezentuje zalet klasyfikatora względem prognoz losowych. Utrata dziennika mierzy niepewność modelu przez porównanie prawdopodobieństw, wynikowe znane wartości (podstaw prawdziwych danych) w etykietach. Chcesz zminimalizować ryzyko utraty dziennika dla całego modelu.

##  <a name="bkmk_regression"></a> Metryki dla modele regresji
 
Metryki dla modele regresji zazwyczaj są przeznaczone do oszacowania ilości błąd.  Model jest uznawany za do rozmiaru danych oraz, jeśli jest to różnica między wartościami obserwacji oraz dostęp do przewidywanych jest mała. Jednak spojrzenie na wzorcu reszty (różnica między każdym punkcie przewidywane i jego wartość rzeczywista) może określić wiele o potencjalnych odchylenie w modelu.  
  
 Następujące metryki są zgłaszane do oceny, modele regresji. Podczas porównywania modeli one są uporządkowane według metrykę, wybrany dla oceny.  
  
- **Średni bezwzględny błąd (dostosowania)** mierzy się, jak blisko prognozy są rzeczywiste wyniki; w związku z tym, lepiej jest niższe wynik.  
  
- **Główny oznacza pierwiastek błędu (RMSE)** tworzy jedną wartość, która znajduje się podsumowanie błędów w modelu. Przez squaring różnica, metryki pomija różnicę między nadmiernego prognoz i prognozowania niepełną.  
  
- **Względny błąd absolutny (RAE)** jest względna bezwzględnej wartości różnicy między oczekiwanymi i rzeczywistymi wartościami; względną, ponieważ różnica średnich jest dzielona przez arytmetycznej.  
  
- **Względna kwadrat błędu (Röse)** podobnie normalizuje łączna liczba błędów kwadratów przewidywane wartości przez podzielenie przez łączna liczba błędów kwadratów rzeczywistych wartości.  
  
- **Oznacza Zero jeden błąd (MZOE)** wskazuje, czy Prognozowanie była poprawna.  Innymi słowy: `ZeroOneLoss(x,y) = 1` podczas `x!=y`; w przeciwnym razie `0`.
  
- **Determinacji**, często są nazywane R<sup>2</sup>, reprezentuje możliwości predykcyjnego modelu jako wartość z zakresu od 0 do 1. Wartość zero oznacza, że model jest losowa (wyjaśnia, nothing); 1 oznacza, że istnieje dokładne dopasowanie. Jednak ostrożność w interpretacji języka R<sup>2</sup> wartości, jak niskich wartości może być całkowicie normalne i o wysokiej wartości może być podejrzany.
  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 