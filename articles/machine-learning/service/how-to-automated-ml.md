---
title: Co to jest automatyczne Machine Learning — usługi Azure Machine Learning
description: Dowiedz się o usługi Azure Machine Learning można automatycznie wybrać algorytm dla Ciebie i wygenerować model w celu zapisania czasu przy użyciu parametrów i kryteria, aby wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4b9356c7cba14cf5bd112c7d7ae1aab2842fb9d1
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010896"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczna usługi machine learning?

W tym artykule można dowiedzieć się o automatycznych machine learning. Usługa Azure Machine Learning można wybrać algorytm dla Ciebie i automatycznie wygenerować model z niego. Automatyczne usługi machine learning pozwala zaoszczędzić czas, generując modeli dostrojone cele i ograniczenia, które są zdefiniowane dla eksperymentu, takie jak czas eksperyment do uruchomienia lub który modeluje do czarnej listy.

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

1. Konfigurowanie konfiguracji uczenia automatycznych maszyny. W ten sposób kontroluje parametry używane zgodnie z usługi Azure Machine Learning iteruje przez różne modele, ustawienia hiperparametrycznego i jakie metryki, aby przyjrzeć się podczas określania najlepszy model. 

1. Prześlij szkolenia, uruchom.

Podczas szkolenia, usługi Azure Machine Learning, powstaje coraz potoki, którzy spróbują różnych algorytmów i parametrów. Zatrzyma się po trafienia limit iteracji, który podasz lub po osiągnięciu wartości docelowej dla metryk, które określisz.

[ ![Automatyczne usługi Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Można sprawdzić zarejestrowane informacje wykonywania zawiera metryki zebrane podczas uruchamiania. Uruchom szkolenia również tworzy obiekt Python serializacji (plik .pkl), który zawiera model i wstępnego przetwarzania danych.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładami i Dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego Machine Learning:

+ [Samouczek: Automatycznie wytrenuj model klasyfikacji z usługą Azure automatyczne Machine Learning](tutorial-auto-train-models.md)

+ [Jak skonfigurować ustawienia automatycznego szkolenia](how-to-configure-auto-train.md)

+ [Jak używać automatycznego szkolenia na zasób zdalny](how-to-auto-train-remote.md) 
