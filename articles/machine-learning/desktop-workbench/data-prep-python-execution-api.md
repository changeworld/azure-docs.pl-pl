---
title: "Szczegółowe wskazówki na temat używania wykonywania Azure Machine Learning danych przygotowania interfejsu API | Dokumentacja firmy Microsoft"
description: "Niniejszy dokument zawiera szczegółowe informacje o poprzednio wykonywania przeznaczony pakietów źródeł danych i przygotowania danych"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Wykonanie pakietów źródła danych i przygotowania danych w języku Python

Aby użyć źródeł danych Learning maszyny Azure lub usługi Azure Machine Learning danych przygotowania pakietu w języku Python:

1. Przejdź do **danych** karty Projekt.

2. Kliknij prawym przyciskiem myszy odpowiednie źródłowe.

3. Wybierz **Generowanie pliku kodu dostępu do danych.**

Ta akcja tworzy krótki skrypt w języku Python, który wykonuje pakietu i zwraca dataframe.

## <a name="data-sources"></a>Źródła danych

`azureml.dataprep.datasource` Moduł zawiera jednej funkcji do wykonywania źródła danych i zwracać dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` to ścieżka do źródła danych (plik .dsource).
- `secrets` jest opcjonalne słownik mapujący klucze do kluczy tajnych.
- `spark` jest opcjonalna wartość logiczna określająca, czy mają być zwracane Spark dataframe lub Pandas dataframe. Domyślnie program Azure Machine Learning Workbench Określa, jakiego rodzaju dataframe zwracać w czasie wykonywania na podstawie kontekstu.

## <a name="data-preparations-packages"></a>Pakiety przygotowań danych

`azureml.dataprep.package` Moduł zawiera trzy funkcje, których wykonanie przepływu danych w pakiecie przygotowań danych.

### <a name="execution-functions"></a>Wykonanie funkcji

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` przesyła przepływ danych określony dla wykonywania, ale nie zwraca dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` uruchamia przepływ określone dane i zwraca wynik jako dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` uruchamia przepływ określone dane na podstawie źródła danych w pamięci i zwraca wynik jako dataframe. `user_config` Argument jest słownik, który mapuje bezwzględna ścieżka źródła danych (plik .dsource) reprezentowane jako listę list źródłu danych w pamięci.

### <a name="common-arguments"></a>Typowe argumentów

- `package_path` to ścieżka do pakiecie przygotowań danych (plik .dprep).
- `dataflow_idx` jest liczony od zera indeks, który przepływu danych w pakiecie w celu wykonania. Jeśli przepływ określonych danych odwołuje się do innych przepływów danych lub źródeł danych, wykonywane są również.
- `secrets` jest opcjonalne słownik mapujący klucze do kluczy tajnych.
- `spark` jest opcjonalna wartość logiczna określająca, czy mają być zwracane Spark dataframe lub Pandas dataframe. Domyślnie Workbench Określa, jakiego rodzaju dataframe zwracać w czasie wykonywania na podstawie kontekstu.
