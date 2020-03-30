---
title: 'Ocena modelu: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Oceniaj model w usłudze Azure Machine Learning, aby zmierzyć dokładność uczonych modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456408"
---
# <a name="evaluate-model-module"></a>Ocena modułu modelu

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do pomiaru dokładności przeszkolonego modelu. Podasz zestaw danych zawierający wyniki wygenerowane z modelu, a moduł **Oceń model** oblicza zestaw standardowych metryk oceny branżowej.
  
 Metryki zwracane przez **Oceń model** zależą od typu ocenianego modelu:  
  
-   **Modele klasyfikacji**    
-   **Modele regresji**  
-   **Modele klastrowania**  


> [!TIP]
> Jeśli jesteś nowy w ocenie modelu, zalecamy serię wideo dr Stephena Elstona, w ramach [kursu uczenia maszynowego](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) firmy EdX. 


Istnieją trzy sposoby użycia modułu **Oceń model:**

+ Generowanie wyników na podstawie danych treningowych i ocena modelu na podstawie tych wyników
+ Generowanie wyników w modelu, ale porównać te wyniki z wynikami na zarezerwowanym zestawie testów
+ Porównaj wyniki dla dwóch różnych, ale powiązanych modeli, używając tego samego zestawu danych

## <a name="use-the-training-data"></a>Korzystanie z danych szkoleniowych

Aby ocenić model, należy połączyć zestaw danych zawierający zestaw kolumn wejściowych i wyników.  Jeśli żadne inne dane nie są dostępne, można użyć oryginalnego zestawu danych.

1. Połącz dane **wyjściowe zestawu danych Scored** [modelu wynikowego](./score-model.md) z wejściem **evaluate modelu**. 
2. Kliknij **przycisk Oceń moduł modelu** i uruchom potok, aby wygenerować wyniki oceny.

## <a name="use-testing-data"></a>Korzystanie z danych testowych

Typowym scenariuszem w uczeniu maszynowym jest oddzielenie oryginalnego zestawu danych na zestawy danych szkoleniowych i testowych przy użyciu modułu [Podziału](./split-data.md) lub [modułu Partycja i Przykład.](./partition-and-sample.md) 

1. Połącz dane **wyjściowe zestawu danych Scored** [modelu wynikowego](score-model.md) z wejściem **evaluate modelu**. 
2. Połącz dane wyjściowe modułu Podziel dane, który zawiera dane testowe, z wejściem po prawej **stronie oceny modelu**.
2. Kliknij **przycisk Oceń moduł modelu,** a następnie wybierz pozycję **Uruchom wybraną,** aby wygenerować wyniki oceny.

## <a name="compare-scores-from-two-models"></a>Porównanie wyników z dwóch modeli

Można również podłączyć drugi zestaw wyników do **oceny modelu**.  Wyniki mogą być zestawem oceny udostępnionej, który ma znane wyniki lub zestaw wyników z innego modelu dla tych samych danych.

Ta funkcja jest przydatna, ponieważ można łatwo porównać wyniki z dwóch różnych modeli na tych samych danych. Można też porównać wyniki z dwóch różnych przebiegów za pomocą tych samych danych z różnymi parametrami.

1. Połącz dane **wyjściowe zestawu danych Scored** [modelu wynikowego](score-model.md) z wejściem **evaluate modelu**. 
2. Podłącz dane wyjściowe modułu Score Model dla drugiego modelu do po prawej stronie wejścia **Oceniaj model**.
3. Prześlij potok.

## <a name="results"></a>Wyniki

Po uruchomieniu **opcji Oceń model**kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizacja wyników oceny,** aby wyświetlić wyniki.

Jeśli połączysz zestawy danych z obu danych wejściowych **Evaluate Model,** wyniki będą zawierać metryki dla obu zestaw danych lub obu modeli.
Model lub dane dołączone do lewego portu są prezentowane najpierw w raporcie, a następnie metryki dla zestawu danych lub modelu dołączonego na prawym porcie.  

Na przykład poniższa ilustracja przedstawia porównanie wyników z dwóch modeli klastrowania, które zostały zbudowane na tych samych danych, ale z różnymi parametrami.  

![Porównanie2Modele](media/module/evaluate-2-models.png)  

Ponieważ jest to model klastrowania, wyniki oceny są inne niż w przypadku porównania wyników z dwóch modeli regresji lub porównano dwa modele klasyfikacji. Jednak ogólna prezentacja jest taka sama. 

## <a name="metrics"></a>Metryki

W tej sekcji opisano metryki zwrócone dla określonych typów modeli obsługiwanych do użycia z **oceną modelu:**

+ [modele klasyfikacji](#metrics-for-classification-models)
+ [modele regresji](#metrics-for-regression-models)
+ [modele klastrowania](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metryki dla modeli klasyfikacji

Podczas oceny modeli klasyfikacji są zgłaszane następujące metryki.
  
-   **Dokładność** mierzy dobroć modelu klasyfikacji jako proporcję rzeczywistych wyników do wszystkich przypadków.  
  
-   **Precyzja** to proporcja prawdziwych wyników w stosunku do wszystkich pozytywnych wyników.  
  
-   **Odwołanie** jest ułamek wszystkich poprawnych wyników zwracanych przez model.  
  
-   **Wynik F** jest obliczany jako średnia ważona precyzji i odwołania między 0 i 1, gdzie idealna wartość wyniku F wynosi 1.  
  
-   **AUC** mierzy obszar pod krzywą wykreśloną z prawdziwymi wartościami dodatnimi na osi y i fałszywymi alarmami na osi x. Ta metryka jest przydatna, ponieważ zawiera jedną liczbę, która umożliwia porównywanie modeli różnych typów.  
  
- **Średnia strata dziennika** jest pojedynczy wynik używany do wyrażania kary za złe wyniki. Jest obliczana jako różnica między dwoma rozkładami prawdopodobieństwa — prawdziwym i jednym w modelu.  
  
- **Utrata dziennika szkolenia** jest pojedynczy wynik, który reprezentuje przewagę klasyfikatora nad losowego przewidywania. Utrata dziennika mierzy niepewność modelu, porównując prawdopodobieństwa, które wyprowadza ze znanymi wartościami (prawdy o ziemi) w etykietach. Chcesz zminimalizować utratę dziennika dla modelu jako całości.

### <a name="metrics-for-regression-models"></a>Metryki dla modeli regresji
 
Metryki zwracane dla modeli regresji są przeznaczone do szacowania ilości błędu.  Model jest uważany za dobrze dopasowany do danych, jeśli różnica między obserwowanymi i przewidywanymi wartościami jest niewielka. Jednak patrząc na wzór pozostałości (różnica między jednym przewidywanym punktem a odpowiadającą mu wartością rzeczywistą) może wiele powiedzieć o potencjalnym odchyleniu w modelu.  
  
 Następujące metryki są zgłaszane do oceny modeli regresji.
  
- **Średni błąd bezwzględny (MAE)** mierzy, jak blisko prognoz są rzeczywiste wyniki; w związku z tym lepszy jest niższy wynik.  
  
- **Główny średni kwadratowy błąd (RMSE)** tworzy pojedynczą wartość, która podsumowuje błąd w modelu. Przez kwadratury różnicy, metryki pomija różnicę między przewidywanie i niedoprzewidowania.  
  
- **Względny błąd bezwzględny (RAE)** jest względną różnicą bezwzględną między wartościami oczekiwanymi a rzeczywistymi; względna, ponieważ średnia różnica jest podzielona przez średnią arytmetyczną.  
  
- **Względny kwadratowy błąd (RSE)** podobnie normalizuje całkowity kwadratowy błąd przewidywanych wartości przez podzielenie przez całkowity kwadrat błędu rzeczywistych wartości.  
  

  
- **Współczynnik oznaczalności,** często określany jako R<sup>2,</sup>reprezentuje moc predykcyjną modelu jako wartość między 0 a 1. Zero oznacza, że model jest losowy (nic nie wyjaśnia); 1 oznacza, że jest idealne dopasowanie. Należy jednak zachować ostrożność podczas interpretacji wartości R<sup>2,</sup> ponieważ niskie wartości mogą być całkowicie normalne, a wysokie wartości mogą być podejrzane.

###  <a name="metrics-for-clustering-models"></a>Metryki dla modeli klastrowania

Ponieważ modele klastrowania różnią się znacznie od modeli klasyfikacji i regresji pod wieloma względami, [Funkcja Oceń model](evaluate-model.md) zwraca również inny zestaw statystyk dla modeli klastrowania.  
  
 Statystyki zwracane dla modelu klastrowania opisują, ile punktów danych zostało przypisanych do każdego klastra, ilość separacji między klastrami i jak ściśle punkty danych są zgrupowane w każdym klastrze.  
  
 Statystyki modelu klastrowania są uśredniane w całym zestawie danych, z dodatkowymi wierszami zawierającymi statystyki na klaster.  
  
Następujące metryki są zgłaszane do oceny modeli klastrowania.
    
-   Wyniki w kolumnie **Średnia odległość do innego środka**, reprezentują, jak blisko, średnio, każdy punkt w klastrze jest do centroidów wszystkich innych klastrów.   

-   Wyniki w kolumnie **Średnia odległość do centrum klastra**reprezentują bliskość wszystkich punktów w klastrze z centroidem tego klastra.  
  
-   Kolumna **Liczba punktów** pokazuje, ile punktów danych zostało przypisanych do każdego klastra, wraz z całkowitą całkowitą liczbą punktów danych w dowolnym klastrze.  
  
     Jeśli liczba punktów danych przypisanych do klastrów jest mniejsza niż całkowita liczba dostępnych punktów danych, oznacza to, że nie można przypisać punktów danych do klastra.  
  
-   Wyniki w kolumnie **Maksymalna odległość do Centrum klastra**reprezentują sumę odległości między każdym punktem a środkiem ciężkości klastra tego punktu.  
  
     Jeśli ta liczba jest wysoka, może to oznaczać, że klaster jest szeroko rozproszone. Należy przejrzeć tę statystykę wraz z **Centrum klastra średnia odległość** do klastra, aby określić rozkład klastra.   

-   **Wynik połączonej oceny** u dołu każdej sekcji wyników zawiera listę uśrednionych wyników dla klastrów utworzonych w tym konkretnym modelu.  
  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 