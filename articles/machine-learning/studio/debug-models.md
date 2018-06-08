---
title: Debugowanie modelu w Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak można debugować błędy wygenerowane przez moduły Train Model i Score Model w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.openlocfilehash: 144edca6d2e6fc8d0d8e59e6cff1d3f37eb233aa
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834315"
---
# <a name="debug-your-model-in-azure-machine-learning"></a>Debugowanie modelu w usłudze Azure Machine Learning

W tym artykule opisano potencjalnych przyczyn, dlaczego jedną z następujących awarii dwóch węzłów może napotkać podczas uruchamiania modelu:

* [Train Model] [ train-model] moduł powoduje błąd 
* [Score Model] [ score-model] modułu tworzy niepoprawnych wyników 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Train Model moduł powoduje błąd

![image1](./media/debug-models/train_model-1.png)

[Train Model] [ train-model] modułu oczekuje dwóch parametrów wejściowych:

1. Typ modelu uczenia maszynowego z kolekcji modeli udostępniane przez usługi Azure Machine Learning.
2. Dane szkoleniowe z określonej kolumny etykiety, który określa zmienną do prognozowania (innych kolumn są rozpatrywane funkcji).

Ten moduł może utworzyć wystąpił błąd w następujących przypadkach:

1. Kolumna etykiety została określona nieprawidłowo. Może to nastąpić, jeśli wybrano więcej niż jednej kolumny jako etykietę lub wybrano nieprawidłowy indeks. Na przykład drugim przypadku będą miały zastosowania, jeśli indeks kolumny 30 jest używany z wejściowego zestawu danych, która zawiera tylko 25 kolumn.

2. Zestaw danych nie zawiera żadnych kolumn funkcji. Na przykład jeśli wejściowy zestaw danych zawiera tylko jedną kolumnę, która jest oznaczona jako kolumna etykiety, nie byłoby żadne funkcje umożliwiające tworzenie modelu. W takim przypadku [Train Model] [ train-model] moduł powoduje błąd.

3. Wejściowy zestaw danych (funkcji lub etykiety) zawiera nieskończoności jako wartość.

## <a name="score-model-module-produces-incorrect-results"></a>Moduł score Model tworzy niepoprawnych wyników

![image2](./media/debug-models/train_test-2.png)

W typowych eksperymentu uczenia/testowania dla uczenia nadzorowanego [podziału danych] [ split] modułu oryginalnego zestawu danych jest podzielony na dwie części: jedną część służy do nauczenia modelu, i jedną część służy do wyniku jak wykonuje również trenowanego modelu. Trenowanego modelu jest następnie używany do wyniku testu dane, po upływie którego wyniki są oceniane w celu sprawdzenia prawidłowości modelu.

[Score Model] [ score-model] moduł wymaga dwóch parametrów wejściowych:

1. Dane wyjściowe uczonego modelu z [Train Model] [ train-model] modułu.
2. Oceniania zestawu danych, który różni się od zestawu danych używany do uczenia modelu.

Istnieje możliwość, że nawet jeśli eksperyment zakończy się powodzeniem, [Score Model] [ score-model] modułu tworzy niepoprawnych wyników. Może to spowodować na to kilka scenariuszy:

1. Jeśli etykiety są podzielone na kategorie i model regresji jest uczony na danych, nieprawidłowych danych wyjściowych będzie utworzonej przez [Score Model] [ score-model] modułu. Jest to spowodowane regresji wymaga zmiennej ciągłego odpowiedzi. W takim wypadku byłoby bardziej odpowiednie do użycia z modelem klasyfikacji. 

2. Podobnie jeśli model klasyfikacji jest uczony w zestawie danych o zmiennoprzecinkową liczby w kolumnie Etykieta, może dać niepożądane wyniki. Jest to spowodowane klasyfikacji wymaga zmiennej odrębny odpowiedzi, która zezwala tylko wartości zakresu za pośrednictwem skończona i zwykle nieco mały zestaw klas.

3. Jeśli oceniania zestawu danych nie zawiera wszystkie funkcje, które są używane do nauczenia modelu, [Score Model] [ score-model] powoduje błąd.

4. Jeśli wiersza w zestawie danych oceniania zawiera brakujące wartości lub nieskończona wartość dla każdego z jego funkcji [Score Model] [ score-model] nie przyniesie żadnego wyniku odpowiadającego danego wiersza.

5. [Score Model] [ score-model] może tworzyć identyczne dane wyjściowe dla wszystkich wierszy w zestawie wyników danych. To może wystąpić, na przykład podczas próby klasyfikacji za pomocą lasów decyzji, jeśli wybrano minimalną liczbę próbek na węzeł liścia musi mieć wartość większą niż liczba dostępnych przykłady szkolenia.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

