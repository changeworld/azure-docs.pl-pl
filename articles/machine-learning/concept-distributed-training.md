---
title: Co to jest szkolenie rozproszone?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o szkoleniach rozproszonych i o tym, jak obsługuje je usługa Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385547"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Szkolenia rozproszone za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się o szkoleniach rozproszonych i o tym, jak usługa Azure Machine Learning obsługuje je dla modeli uczenia głębokiego. 

W szkoleniach rozproszonych obciążenie do szkolenia modelu jest dzielone i współużytkowane przez wiele mini procesorów, nazywanych węzłami procesu roboczego. Te węzły procesu roboczego działają równolegle, aby przyspieszyć szkolenie modelu. Szkolenia rozproszone mogą być używane dla tradycyjnych modeli uczenia maszynowego, ale lepiej nadają się do zadań obliczeniowych i czasochłonnych, takich jak [uczenie głębokie](concept-deep-learning-vs-machine-learning.md) do szkolenia głębokich sieci neuronowych.

## <a name="deep-learning-and-distributed-training"></a>Głębokie uczenie się i szkolenia rozproszone 

Istnieją dwa główne typy szkoleń rozproszonych: [równoległość danych](#data-parallelism) i [równoległość modelu.](#model-parallelism) W przypadku szkoleń rozproszonych dotyczących modeli uczenia głębokiego [zestaw SDK usługi Azure Machine Learning w języku Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) obsługuje integracje z popularnymi strukturami, PyTorch i TensorFlow. Obie struktury wykorzystują równoległość danych do szkoleń rozproszonych i mogą wykorzystywać [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) do optymalizacji prędkości obliczeniowej. 

* [Szkolenie rozproszone z PyTorch](how-to-train-pytorch.md#distributed-training)

* [Szkolenia rozproszone z TensorFlow](how-to-train-tensorflow.md#distributed-training)

W przypadku modeli uczenia maszynowego, które nie wymagają szkolenia rozproszonego, zobacz [modele pociągów z usługą Azure Machine Learning,](concept-train-machine-learning-model.md#python-sdk) aby uzyskać różne sposoby uczenia modeli przy użyciu zestawu SDK języka Python.

## <a name="data-parallelism"></a>Równoległość danych

Równoległość danych jest najłatwiejsza do zaimplementowania dwóch rozproszonych metod szkoleniowych i jest wystarczająca dla większości przypadków użycia.

W tym podejściu dane są podzielone na partycje, gdzie liczba partycji jest równa całkowitej liczbie dostępnych węzłów w klastrze obliczeniowym. Model jest kopiowany w każdym z tych węzłów procesu roboczego, a każdy proces roboczy działa na własnym podzbiorze danych. Należy pamiętać, że każdy węzeł musi mieć zdolność do obsługi modelu, który jest szkolony, czyli model musi całkowicie zmieścić się w każdym węźle.

Każdy węzeł niezależnie oblicza błędy między jego prognoz dla jego próbek szkoleniowych i oznaczone dane wyjściowe. Z kolei każdy węzeł aktualizuje swój model na podstawie błędów i musi komunikować wszystkie jego zmiany do innych węzłów, aby zaktualizować ich odpowiednich modeli. Oznacza to, że węzły procesu roboczego muszą synchronizować parametry modelu lub gradienty na końcu obliczeń wsadowych, aby upewnić się, że szkolą spójny model. 

## <a name="model-parallelism"></a>Równoległość modelu

W równoległości modelu, znany również jako równoległości sieci, model jest podzielony na różne części, które można uruchomić jednocześnie w różnych węzłach, a każdy z nich będzie działać na tych samych danych. Skalowalność tej metody zależy od stopnia równoległości zadań algorytmu i jest bardziej złożona do zaimplementowania niż równoległość danych. 

W równoległości modelu węzły procesu roboczego wystarczy tylko zsynchronizować parametry udostępnione, zwykle raz dla każdego kroku propagacji do przodu lub do tyłu. Ponadto większe modele nie są problemem, ponieważ każdy węzeł działa na podsekcji modelu na tych samych danych szkoleniowych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [skonfigurować środowiska szkoleniowe](how-to-set-up-training-targets.md) za pomocą zestawu SDK języka Python.
* Przykład techniczny można znaleźć w [scenariuszu architektury referencyjnej](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Trenuj modele ML z TensorFlow](how-to-train-tensorflow.md).
* [Trenuj modele ML z PyTorch](how-to-train-pytorch.md). 