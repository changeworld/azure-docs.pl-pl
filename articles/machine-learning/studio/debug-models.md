---
title: Debugowanie modelu
titleSuffix: Azure Machine Learning Studio
description: Jak debugować błędy wygenerowane przez moduły Train Model i Score Model w usłudze Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 9c505262030e5b5aa13b8d221cf1e39c4a9c7833
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751125"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Debugowanie modelu w usłudze Azure Machine Learning Studio

Gdy używany jest model, mogą występować następujące błędy:

* [Train Model] [ train-model] moduł generuje błąd 
* [Score Model] [ score-model] moduł generuje nieprawidłowe wyniki 

W tym artykule opisano potencjalne przyczyny tych błędów.


## <a name="train-model-module-produces-an-error"></a>Generuje błąd, Train Model modułu

![image1](./media/debug-models/train_model-1.png)

[Train Model] [ train-model] modułu oczekiwane dwie wartości wejściowe:

1. Typ modelu uczenia maszynowego z kolekcji modeli udostępniane przez usługi Azure Machine Learning Studio.
2. Dane szkoleniowe z określonej kolumny etykiety, która określa zmienną do prognozowania (inne kolumny są zakłada się, że funkcje).

Ten moduł może powodować błąd w następujących przypadkach:

1. Kolumna etykiety została określona nieprawidłowo. Może to nastąpić, jeśli wybrano więcej niż jedną kolumnę jako etykieta lub wybrano nieprawidłowy indeks. Na przykład jeśli indeks kolumny, 30 jest używany z wejściowego zestawu danych, która zawiera tylko 25 kolumny, stosuje się drugim przypadku.

2. Zestaw danych nie zawiera żadnych kolumn funkcji. Na przykład jeśli wejściowy zestaw danych zawiera tylko jedną kolumnę, która jest oznaczona jako kolumna etykiety, będą istnieć żadne funkcje, dzięki którym można utworzyć modelu. W tym przypadku [Train Model] [ train-model] moduł generuje błąd.

3. Wejściowy zestaw danych (funkcji lub etykietę) zawiera nieskończoności jako wartość.

## <a name="score-model-module-produces-incorrect-results"></a>Moduł score Model generuje nieprawidłowe wyniki

![image2](./media/debug-models/train_test-2.png)

W typowym eksperymentów szkolenia i testowania w trybie uczenia nadzorowanego [podziału danych] [ split] oryginalnego zestawu danych w module jest podzielony na dwie części: jedną część jest używany do nauczenia modelu, i jedną część jest używany do generowania wyników na sposób wykonuje również trenowanego modelu. Uczony model jest następnie używany do oceniania dane z badań, po upływie którego wyniki są oceniane w celu sprawdzenia prawidłowości modelu.

[Score Model] [ score-model] moduł wymaga dwóch danych wejściowych:

1. Dane wyjściowe trenowanego modelu, z [Train Model] [ train-model] modułu.
2. Oceniania zestawu danych, który różni się od zbioru danych używanego do nauczenia modelu.

Istnieje możliwość, że nawet jeśli eksperymentu zakończy się powodzeniem, [Score Model] [ score-model] moduł generuje nieprawidłowe wyniki. Kilka scenariuszy może być przyczyną tego problemu, do wykonania:

1. Jeśli określona etykieta jest podzielone na kategorie i jest uczony model regresji, danych, niepoprawne dane wyjściowe może zostać wytworzony przez [Score Model] [ score-model] modułu. Jest to spowodowane regresji wymaga zmiennej ciągłej odpowiedzi. W takim wypadku byłoby bardziej odpowiednie do użycia model klasyfikacji. 

2. Podobnie jeśli model klasyfikacji zostały przeszkolone na zestaw danych o liczb zmiennoprzecinkowych w kolumnie Etykieta, może powodować niepożądane wyniki. Jest to spowodowane klasyfikacji wymaga zmiennej dyskretnych odpowiedzi, który dopuszcza tylko wartości zakresu przez ograniczone i małe, zestaw klas.

3. Jeśli oceniania zestawu danych nie zawiera wszystkie funkcje, które są używane do nauczenia modelu, [Score Model] [ score-model] generuje błąd.

4. Jeśli wiersz w oceniania zestaw danych zawiera Brak wartości lub wartość infinite dla żadnej z jej funkcji [Score Model] [ score-model] nie generuje żadnych danych wyjściowych, odpowiadający tego wiersza.

5. [Score Model] [ score-model] może tworzyć identyczne dane wyjściowe dla wszystkich wierszy w zestawie danych oceniania. To może być fakt, na przykład podczas próby klasyfikacji przy użyciu decyzji lasów, jeśli minimalna liczba próbek na węzeł liścia jest wybierany być większa niż liczba dostępnych przykładów szkoleniowych.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

