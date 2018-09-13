---
title: Szczegółowy przewodnik dotyczący sposobu użycia usługi Azure Machine Learning danych przygotowań wykonania interfejsu API | Dokumentacja firmy Microsoft
description: Ten dokument zawiera szczegółowe informacje na temat wykonywania wcześniej zaprojektowana pakiety źródeł danych i przygotowywanie danych
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9f15f949ba76240da2788c1a01e7086ba3b42fd1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648938"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Wykonanie pakietów źródeł danych i przygotowywanie danych za pomocą języka Python

Umożliwia użycie pakietu źródeł danych programu Azure Machine Learning lub Azure Machine Learning danych przygotowań w języku Python:

1. Przejdź do **danych** karty Projekt.

2. Kliknij prawym przyciskiem myszy odpowiedni źródłowy.

3. Wybierz **Generuj plik kodu dostępu do danych.**

Ta akcja powoduje utworzenie krótki skrypt w języku Python, który wykonuje pakietu i zwraca ramkę danych.

## <a name="data-sources"></a>Źródła danych

`azureml.dataprep.datasource` Moduł zawiera pojedynczą funkcję do wykonania źródła danych i zwracają ramkę danych: `load_datasource(path, secrets=None, spark=None)`.
- `path` jest to ścieżka do źródła danych (plik .dsource).
- `secrets` jest słownik opcjonalne, mapujący klucze do wpisów tajnych.
- `spark` jest opcjonalna wartość logiczna określająca, czy zwracać elementów dataframe aparatu Spark lub Pandas dataframe. Domyślnie Azure Machine Learning Workbench określa rodzaj ramki danych do zwrócenia w czasie wykonywania na podstawie kontekstu.

## <a name="data-preparations-packages"></a>Pakiety przygotowywanie danych

`azureml.dataprep.package` Moduł zawiera trzy funkcje, które są wykonywane przepływu danych z pakietu przygotowywanie danych.

### <a name="execution-functions"></a>Wykonanie funkcji

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` przesyła przepływ określone dane do wykonania, ale nie może zwracać ramkę danych.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` uruchamia przepływ określone dane i zwraca wyniki w postaci ramkę danych.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` uruchamia przepływ określone dane na podstawie źródła danych w pamięci i zwraca wyniki w postaci ramkę danych. `user_config` Argument jest słownik mapujący ścieżka bezwzględna źródła danych (plik .dsource) ze źródłem danych w pamięci, reprezentowane jako listę list.

### <a name="common-arguments"></a>Często używanych argumentów

- `package_path` jest to ścieżka do pakietu przygotowywanie danych (plik .dprep).
- `dataflow_idx` jest liczony od zera indeks, który przepływu danych w pakiecie w celu wykonania. Jeśli przepływ określonych danych odwołuje się do innych przepływów danych lub źródła danych, są one wykonywane także.
- `secrets` jest słownik opcjonalne, mapujący klucze do wpisów tajnych.
- `spark` jest opcjonalna wartość logiczna określająca, czy zwracać elementów dataframe aparatu Spark lub Pandas dataframe. Domyślnie usługa Workbench określa rodzaj ramki danych do zwrócenia w czasie wykonywania na podstawie kontekstu.
