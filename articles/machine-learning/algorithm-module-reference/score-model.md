---
title: 'Model wyników: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu score model w usłudze Azure Machine Learning do generowania prognoz przy użyciu uczonych klasyfikacji lub modelu regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364172"
---
# <a name="score-model-module"></a>Moduł Score Model (Generowanie wyników przez model)

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do generowania prognoz przy użyciu przeszkolonych klasyfikacji lub modelu regresji.

## <a name="how-to-use"></a>Jak stosować

1. Dodaj **wynik modelu** modułu do potoku.

2. Dołącz przeszkolony model i zestaw danych zawierający nowe dane wejściowe. 

    Dane powinny być w formacie zgodnym z typem modelu przeszkolonego, którego używasz. Schemat wejściowego zestawu danych powinien również ogólnie odpowiadać schematowi danych używanych do trenowania modelu.

3. Prześlij potok.

## <a name="results"></a>Wyniki

Po wygenerowaniu zestawu wyników przy użyciu [modelu score:](./score-model.md)

+ Aby wygenerować zestaw metryk używanych do oceny dokładności (wydajności) modelu, można połączyć zestaw danych punktowanych z [oceną modelu,](./evaluate-model.md) 
+ Kliknij prawym przyciskiem myszy moduł i wybierz pozycję **Wizualizuj,** aby wyświetlić próbkę wyników.
<!-- + To Save the results to a dataset. -->

Wynik lub przewidywana wartość mogą być w wielu różnych formatach, w zależności od modelu i danych wejściowych:

- W przypadku modeli klasyfikacji [score model](./score-model.md) wyprowadza przewidywaną wartość dla klasy, a także prawdopodobieństwo przewidywanej wartości.
- Dla modeli regresji [Score Model](./score-model.md) generuje tylko przewidywaną wartość liczbową.


## <a name="publish-scores-as-a-web-service"></a>Publikowanie wyników jako usługi sieci web

Typowym zastosowaniem oceniania jest zwrócenie danych wyjściowych w ramach predykcyjnej usługi sieci web. Aby uzyskać więcej informacji, zobacz [ten samouczek](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) dotyczący wdrażania punktu końcowego w czasie rzeczywistym na podstawie potoku w projektancie usługi Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 