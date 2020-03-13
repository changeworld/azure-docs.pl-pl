---
title: Optymalizuj algorytmy
titleSuffix: ML Studio (classic) - Azure
description: Wyjaśnia, jak wybrać optymalny zestaw parametrów dla algorytmu w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218271"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Wybierz parametry, aby zoptymalizować algorytmy w Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

W tym temacie opisano, jak wybrać prawy parametr ustawiony dla algorytmu w Azure Machine Learning Studio (klasyczny). Większość algorytmów uczenia maszynowego parametrów wymaganych do zestawu. Ucząc model, konieczne jest podanie wartości tych parametrów. Skuteczność uczonego modelu zależy od parametrów modelu, które wybierzesz. Proces znajdowania optymalnego zestawu parametrów jest znany jako *wybór modelu*.



Istnieją różne sposoby modelu zaznaczenia. W uczeniu maszynowym, krzyżowe sprawdzanie poprawności jest jednym z najczęściej używanych metod wyboru modelu i jest domyślnym mechanizmem wyboru modelu w Azure Machine Learning Studio (klasyczny). Ponieważ Azure Machine Learning Studio (klasyczny) obsługuje zarówno język R, jak i Python, zawsze można zaimplementować własne mechanizmy wyboru modelu przy użyciu języka R lub Python.

Istnieją cztery kroki w procesie wyszukiwania najlepszy zestaw parametrów:

1. **Zdefiniuj miejsce parametru**: dla algorytmu należy najpierw określić dokładne wartości parametrów, które mają być brane pod uwagę.
2. **Zdefiniuj ustawienia dotyczące krzyżowego sprawdzania poprawności**: Zdecyduj, jak wybierać zgięcia krzyżowe dla zestawu danych.
3. **Zdefiniuj metrykę**: Zdecyduj, która Metryka ma być używana do określania najlepszego zestawu parametrów, takich jak dokładność, główny znak średnika, precyzja, odwołanie lub f-Score.
4. **Uczenie, szacowanie i porównywanie**: dla każdej unikatowej kombinacji wartości parametrów, wzajemne sprawdzanie poprawności jest wykonywane w oparciu o określoną metrykę błędu. Po ocenie i porównanie możesz wybrać sprzedające modelu.

Na poniższej ilustracji przedstawiono, jak można to osiągnąć w Azure Machine Learning Studio (klasyczny).

![Znajdź najlepsze zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zdefiniuj obszaru parametrów
Można zdefiniować parametrem ustawionym na etapie inicjowania modelu. Okienko parametrów wszystkich algorytmów uczenia maszynowego ma dwa tryby Trainer: *pojedynczy parametr* i *zakres parametrów*. Wybierz tryb parametru zakresu. W trybie parametru zakresu można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym, można wprowadzić wartości rozdzielanych przecinkami.

![Dwuklasowych wzmocnione drzewo decyzyjnego, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punkty siatki oraz całkowitą liczbę punktów do wygenerowania przy użyciu **konstruktora zakresu użycia**. Domyślne wartości parametrów są generowane na skali liniowej. Jeśli jednak **skalowanie dziennika** jest zaznaczone, wartości są generowane w skali dziennika (to oznacza, że proporcja sąsiadujących punktów jest stała, a nie różnica). Dla parametrów, liczba całkowita można zdefiniować zakres przy użyciu łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite z zakresu od 1 do 10 (włącznie) tworzą zestaw parametrów. Tryb mieszany jest również obsługiwany. Na przykład, zestaw parametrów "1-10, 20, 50" będzie zawierać liczby całkowite 1-10, 20 i 50.

![Dwuklasowych wzmocnione drzewo decyzyjnego, parametr zakresu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Zdefiniuj złożeń krzyżowego sprawdzania poprawności
Na [partycji i przykładowym][partition-and-sample] module można używać do losowego przypisywania zapisywanych danych. W następującej konfiguracji przykładowy moduł firma Microsoft Definiowanie pięciu złożeń i losowo przypisać numer zwijania do wystąpień przykładowe.

![Partycja i próbka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Zdefiniuj wartość metryki
Moduł " [dostrajanie modelu][tune-model-hyperparameters] " pozwala obsłużyć doświadczalnie Wybieranie najlepszego zestawu parametrów dla danego algorytmu i zestawu danych. Oprócz innych informacji dotyczących uczenia modelu, okienko **Właściwości** tego modułu zawiera metrykę służącą do określenia najlepszego zestawu parametrów. Ma dwa pola listy rozwijanej różnych algorytmów klasyfikacji i regresji, odpowiednio. Jeśli algorytm pod uwagę jest to algorytm klasyfikacji, metryki regresji jest ignorowany i na odwrót. W tym konkretnym przykładzie Metryka jest **dokładnością**.   

![Odchylenia parametrów](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Uczenie, ocenę i porównania
Ten sam moduł [strojenia modelu][tune-model-hyperparameters] pociąga za siebie wszystkie modele, które odpowiadają zestawowi parametrów, ocenia różne metryki, a następnie tworzy model najlepiej szkolony na podstawie wybranej metryki. Ten moduł ma dwa obiekty wejściowe wymagane:

* Nieprzeszkolonych learner
* Zestaw danych

Moduł zawiera również opcjonalny zestaw danych wejściowych. Połącz z zestawu danych informacjami zwijania w danych wejściowych obowiązkowe zestawu danych. Jeśli zestaw danych nie przypisano żadnych informacji zwijania, 10-krotnych krzyżowego sprawdzania poprawności zostanie automatycznie wykonany domyślnie. Jeśli przypisanie zwijania nie jest wykonywane sprawdzanie poprawności zestawu danych znajduje się na porcie opcjonalny zestaw danych, jest wybrany tryb train-test i pierwszego zestawu danych jest używany do nauczenia modelu, dla każdej kombinacji parametrów.

![Klasyfikator drzewa decyzyjnego](./media/algorithm-parameters-optimize/fig6a.png)

Model jest następnie oceniany na zestawie danych sprawdzania poprawności. Lewy port wyjściowy modułu różne metryki jest wyświetlany jako funkcji wartości parametrów. Prawidłowy port wyjściowy zapewnia szkolony model, który odpowiada modelowi najlepiej działającemu zgodnie z wybraną metryką (**dokładność** w tym przypadku).  

![Sprawdzanie poprawności zestawu danych](./media/algorithm-parameters-optimize/fig6b.png)

Możesz zobaczyć dokładnie parametrów, wybierany przez wizualizację port wyjściowy prawo. Ten model może służyć w oceniania zestawu testowego lub zoperacjonalizowanej usługi sieci web po zapisaniu jako uczonego modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
