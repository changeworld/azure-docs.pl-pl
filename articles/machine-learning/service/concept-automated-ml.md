---
title: Automatyczne wybieranie algorytmu uczenia Maszynowego i dostosowywania
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak usługa Azure Machine Learning automatycznie wybrać algorytm dla Ciebie i wygenerować model z niego, aby zaoszczędzić czas przy użyciu parametrów i kryteria można wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7e55304fb02078c71b91b2904475a4bee4bcc018
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436963"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczna usługi machine learning?

Automatyczne machine learning to proces wykonywania danych szkoleniowych z funkcją zdefiniowanego celu i iteracja kombinacje algorytmów i wybrane funkcje, aby automatycznie wybrać najlepszy model dla swoich danych, w oparciu o wyniki szkolenia. Tradycyjne maszyny, uczenia modelu procesu opracowywania jest bardzo dużej ilości zasobów i wymaga znaczących domeny wiedzę i czas redukuje inwestycje wymagane do uruchomienia i porównać wyniki z wielu modeli. Uczenie maszynowe automatycznych upraszcza ten proces generowania modeli dostrojone cele i ograniczenia, które są zdefiniowane dla eksperymentu, takie jak czas eksperyment do uruchomienia lub który modeluje do czarnej listy.

## <a name="how-it-works"></a>Jak to działa

1. Możesz skonfigurować typ maszyny nauczanym problemem, który próbujesz rozwiązać. Kategorie uczenia nadzorowanego są obsługiwane:
   + Klasyfikacja
   + Regresji
   + Prognozowanie

   Podczas automatycznego machine learning jest ogólnie dostępna, **prognozowania funkcja jest nadal w publicznej wersji zapoznawczej.**

   Zobacz [listy modeli](how-to-configure-auto-train.md#select-your-experiment-type) usługi Azure Machine Learning można wypróbować podczas szkolenia.

1. Należy określić źródło i format danych szkoleniowych. Dane muszą być oznaczone i mogą być przechowywane w środowisku deweloperskim lub w usłudze Azure Blob Storage. Jeśli dane są przechowywane w środowisku deweloperskim, musi należeć do tego samego katalogu skryptów szkolenia. Ten katalog jest kopiowany do docelowej obliczeń, wybranych szkoleń.

    W skrypcie szkolenia dane mogą być odczytywane w tablicach Numpy lub Pandas dataframe.

    Można skonfigurować opcje podziału służąca do wybierania szkolenia i sprawdzanie poprawności danych, lub można określić oddzielne zestawy danych szkolenia i sprawdzania poprawności.

1. Konfigurowanie [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md) używanego do nauczenia modelu.

1. Konfigurowanie konfiguracji uczenia automatycznych maszyny. W ten sposób kontroluje parametry używane zgodnie z usługi Azure Machine Learning iteruje przez różne modele, ustawienia hiperparametrycznego i jakie metryki, aby przyjrzeć się podczas określania najlepszy model 

1. Prześlij szkolenia, uruchom.

Podczas szkolenia, usługi Azure Machine Learning, powstaje coraz potoki, którzy spróbują różnych algorytmów i parametrów. Zatrzyma się po trafienia limit iteracji, który podasz lub po osiągnięciu wartości docelowej dla metryk, które określisz.

[ ![Automatyczne usługi Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Można sprawdzić zarejestrowane informacje wykonywania zawiera metryki zebrane podczas uruchamiania. Uruchom szkolenia również tworzy obiekt Python serializacji (`.pkl` pliku) zawierający model i wstępnego przetwarzania danych.

## <a name="model-explainability"></a>Explainability modelu

Typowe niedogodności automatycznych machine Learning jest brakiem, aby zapoznać się z procesem end-to-end. Usługa Azure Machine Learning umożliwia wyświetlenie szczegółowych informacji na temat modeli w celu zwiększenia przejrzystości w uruchomionej w zapleczu. W niektórych modelach, takich jak regresji liniowej, są uznawane za dość utrudnione i w związku z tym łatwa do zrozumienia. Jednak firma Microsoft może dodać więcej funkcji, a następnie używać bardziej skomplikowanych modeli uczenia maszynowego, zrozumienie ich pobiera bardziej trudne. Istnieją dwa kluczowe aspekty, przejrzystości działania w usłudze machine learning:

1. Rozpoznawanie potoku i wszystkie kroki uczenia maszynowego zaangażowani, w tym przetwarzanie wstępne danych/cechowania i wartości hiperparametrycznego.
1. Opis relacji między zmienne wejściowe (znany także jako "funkcji") i model danych wyjściowych.  Wiedząc, wielkości i kierunek wpływ każdej funkcji na dostęp do przewidywanych wartości pomaga lepiej zrozumieć i wyjaśnić modelu. Jest to nazywane znaczenie funkcji.

Globalnych funkcji uczenia się na żądanie post znaczenie dla potoku dowolnie lub ją włączyć dla wszystkich potoków w ramach zautomatyzowanej szkolenia uczenia Maszynowego.  Jest to funkcja w wersji zapoznawczej i będziemy inwestować długoterminowo w dostarczaniu bogatsze informacje, aby umożliwić użytkownikom lepsze rozumienie Twoich modeli uczenia Maszynowego.  

Postępuj zgodnie z tym [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) eksperymentować wyjaśnienia modelu w usłudze Azure Machine Learning.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładami i Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego Machine Learning:

+ [Samouczek: Automatycznie wytrenuj model klasyfikacji z usługą Azure automatyczne Machine Learning](tutorial-auto-train-models.md)

+ [Konfigurowanie ustawień automatycznego szkolenia](how-to-configure-auto-train.md)

+ [Użyj automatycznego szkolenie dotyczące zasobu zdalnego](how-to-auto-train-remote.md) 
