---
title: 'Score Model: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Score Model w usłudze Azure Machine Learning do generowania przewidywań za pomocą uczonego klasyfikacji lub modelu regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029268"
---
# <a name="score-model-module"></a>Moduł Score Model (Generowanie wyników przez model)

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do generowania prognozy przy użyciu nauczony model klasyfikacji lub regresji.

## <a name="how-to-use"></a>Jak stosować

1. Dodaj **Score Model** modułu do eksperymentu.

2. Dołącz uczonego modelu i zestawu danych zawierającego nowe dane wejściowe. 

    Dane powinny być w formacie zgodnym z typem trenowanego modelu, którego używasz. Zazwyczaj także schematu wejściowy zestaw danych powinien być zgodny schemat danych użytych do nauczenia modelu.

3. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po wygenerowaniu zestawu wyników przy użyciu [Score Model](./score-model.md):

+ Aby wygenerować zestaw metryk służący do oceny dokładności modelu (wydajność).  Możesz połączyć z ocenami zestawu danych na [Evaluate Model](./evaluate-model.md), 
+ Kliknij prawym przyciskiem myszy moduł, a następnie wybierz pozycję **Visualize** aby zobaczyć przykład wyników.
+ Zapisz wyniki do zestawu danych.

Wynik lub przewidywane wartości może być w różnych formatach, w zależności od modelu i swoich danych wejściowych:

- W przypadku modeli klasyfikacji [Score Model](./score-model.md) generuje wartość przewidziana dla klasy, a także prawdopodobieństwa wystąpienia przewidzianej wartości.
- W przypadku modeli regresji [Score Model](./score-model.md) generuje po prostu przewidywane wartości liczbowej.
- W przypadku modeli klasyfikacji obrazów wynik może być klasę obiektu, obrazu lub wartość Boolean wskazującą, czy dana funkcja został znaleziony.

## <a name="publish-scores-as-a-web-service"></a>Publikuj wyniki jako usługi sieci web

Typowym zastosowaniem oceniania jest zwraca wynik jako część predykcyjna usługa internetowa. Aby uzyskać więcej informacji zobacz ten samouczek na temat tworzenia usługi sieci web, w oparciu o eksperyment w usłudze Azure Machine Learning:


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 