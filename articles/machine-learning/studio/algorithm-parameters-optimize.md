---
title: Optymalizacja algorytmów
titleSuffix: ML Studio (classic) - Azure
description: W tym artykule wyjaśniono, jak wybrać zestaw parametrów optymalnych dla algorytmu w usłudze Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218271"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Wybierz parametry, aby zoptymalizować algorytmy w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

W tym temacie opisano sposób wybierania odpowiedniego zestawu hiperparametrycznego dla algorytmu w usłudze Azure Machine Learning Studio (klasyczny). Większość algorytmów uczenia maszynowego ma parametry do skonfigurowania. Podczas trenowania modelu, należy podać wartości dla tych parametrów. Skuteczność przeszkolonego modelu zależy od wybranego parametru modelu. Proces znajdowania optymalnego zestawu parametrów jest znany jako *wybór modelu*.



Istnieją różne sposoby wyboru modelu. W uczeniu maszynowym krzyżowe sprawdzanie poprawności jest jedną z najczęściej używanych metod wyboru modelu i jest domyślnym mechanizmem wyboru modelu w usłudze Azure Machine Learning Studio (klasycznym). Ponieważ usługa Azure Machine Learning Studio (klasyczna) obsługuje zarówno języka R, jak i Pythona, zawsze można zaimplementować własne mechanizmy wyboru modelu przy użyciu języka R lub Python.

Istnieją cztery kroki w procesie znajdowania najlepszego zestawu parametrów:

1. **Zdefiniuj spację parametrów**: Dla algorytmu najpierw zdecyduj dokładne wartości parametrów, które chcesz wziąć pod uwagę.
2. **Zdefiniuj ustawienia sprawdzania poprawności krzyżowej**: Zdecyduj, jak wybrać fałdy sprawdzania poprawności krzyżowej dla zestawu danych.
3. **Zdefiniuj metrykę:** Zdecyduj, jakiej metryki użyć do określenia najlepszego zestawu parametrów, takich jak dokładność, średni błąd kwadratu głównego, precyzja, odwołanie lub wynik f.
4. **Trenuj, oceniaj i porównuj**: Dla każdej unikatowej kombinacji wartości parametrów krzyżowa walidacja jest przeprowadzana przez i na podstawie zdefiniowanej metryki błędu. Po dokonaniu oceny i porównaniu można wybrać model o najlepszej wydajności.

Na poniższej ilustracji przedstawiono, jak można to osiągnąć w usłudze Azure Machine Learning Studio (klasyczny).

![Znajdź najlepszy zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definiowanie obszaru parametrów
Można zdefiniować parametr ustawiony na etapie inicjowania modelu. Okienko parametrów wszystkich algorytmów uczenia maszynowego ma dwa tryby trenera: *Pojedynczy parametr* i *zakres parametrów*. Wybierz tryb zakresu parametrów. W trybie zakres parametrów można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym można wprowadzić wartości oddzielone przecinkami.

![Dwuklasowe drzewo decyzyjne z premią, pojedynczy parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punkty siatki oraz całkowitą liczbę punktów, które mają zostać wygenerowane za pomocą **programu Use Range Builder**. Domyślnie wartości parametrów są generowane w skali liniowej. Ale jeśli **skala dziennika** jest zaznaczona, wartości są generowane w skali dziennika (oznacza to, że stosunek sąsiednich punktów jest stała zamiast ich różnicy). W przypadku parametrów całkowitych można zdefiniować zakres za pomocą łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite między 1 i 10 (zarówno włącznie) tworzą zestaw parametrów. Obsługiwany jest również tryb mieszany. Na przykład zestaw parametrów "1-10, 20, 50" będzie zawierać liczby całkowite 1-10, 20 i 50.

![Dwuklasowe drzewo decyzyjne z premią, zakres parametrów](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiowanie fałd sprawdzania poprawności krzyżowej
[Partycja i przykład moduł][partition-and-sample] może służyć do losowego przypisywania zagęszków do danych. W poniższej konfiguracji przykładowej dla modułu definiujemy pięć fałd i losowo przypisujemy liczbę zagięcia do przykładowych wystąpień.

![Partycja i próbka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Zdefiniuj metrykę
Moduł [Tune Model Hyperparameters][tune-model-hyperparameters] zapewnia obsługę empirycznego wybierania najlepszego zestawu parametrów dla danego algorytmu i zestawu danych. Oprócz innych informacji dotyczących szkolenia modelu, Właściwości okienka tego modułu zawiera metrykę do określania najlepszego zestawu **parametrów.** Ma dwa różne pola listy rozwijanej dla algorytmów klasyfikacji i regresji, odpowiednio. Jeśli algorytm, o który chodzi, jest algorytmem klasyfikacji, metryka regresji jest ignorowana i odwrotnie. W tym konkretnym przykładzie metryką jest **Dokładność**.   

![Parametry wyciągnięcia po ścieżce](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trenuj, oceniaj i porównuj
Ten sam moduł [Tune Model Hyperparameters trenuje][tune-model-hyperparameters] wszystkie modele, które odpowiadają zestawowi parametrów, ocenia różne metryki, a następnie tworzy najlepiej wyszkolony model na podstawie wybranego metryki. Moduł ten posiada dwa obowiązkowe wejścia:

* Nieprzeszkolony uczeń
* Zestaw danych

Moduł posiada również opcjonalne dane wejściowe zestawu danych. Połącz zestaw danych z informacjami o złożeniu z obowiązkowym wejściem zestawu danych. Jeśli zestawowi danych nie są przypisywane żadne informacje o złożeniu, 10-krotne sprawdzanie poprawności krzyżowej jest wykonywane automatycznie domyślnie. Jeśli przypisanie zagięcia nie zostanie wykonane, a zestaw danych sprawdzania poprawności zostanie dostarczony na opcjonalnym porcie zestawu danych, zostanie wybrany tryb train-test i pierwszy zestaw danych jest używany do trenowania modelu dla każdej kombinacji parametrów.

![Promowany klasyfikator drzewa decyzyjnego](./media/algorithm-parameters-optimize/fig6a.png)

Model jest następnie oceniany na zestaw danych sprawdzania poprawności. Lewy port wyjściowy modułu pokazuje różne metryki jako funkcje wartości parametrów. Prawy port wyjściowy daje przeszkolony model, który odpowiada modelowi o najlepszej wydajności zgodnie z wybraną metryką (**Dokładność** w tym przypadku).  

![Zestaw danych sprawdzania poprawności](./media/algorithm-parameters-optimize/fig6b.png)

Możesz zobaczyć dokładne parametry wybrane przez wizualizację odpowiedniego portu wyjściowego. Ten model może służyć do oceniania zestawu testów lub w operacyjnej usłudze sieci web po zapisaniu jako wyszkolony model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
