---
title: "Znajdź działa z dokładnością najlepszym i najniższego czas trwania w Azure Machine Learning Workbench | Dokumentacja firmy Microsoft"
description: "Przypadek użycia end-to-end można znaleźć najlepsze dokładność za pośrednictwem interfejsu wiersza polecenia przy użyciu usługi Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: bd8888d911730831435b87d3a60b48a7797eea98
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Znajdź działa z dokładnością najlepszym i najniższego czas trwania
Podana wielu uruchomień, jeden przypadek użycia jest znalezienie działa z najlepszym dokładności. Jednym z podejść jest użycie interfejsu wiersza polecenia (CLI) z [JMESPath](http://jmespath.org/) zapytania. Aby uzyskać więcej informacji o sposobie używania JMESPath w wiersza polecenia platformy Azure, zobacz [JMESPath Użyj zapytania z Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). W poniższym przykładzie są tworzone cztery uruchamia dokładność wartości 0, 0,98, 1 lub 1. Uruchamia są filtrowane, jeśli są w zakresie `[MaxAccuracy-Threshold, MaxAccuracy]` gdzie `Threshold = .03`.

## <a name="sample-data"></a>Dane przykładowe
Jeśli nie masz istniejących jest uruchamiany z `Accuracy` wartość następujące kroki Generowanie działa podczas wykonywania zapytań.

Najpierw należy utworzyć plik Python w konsoli usługi Azure Machine Learning Workbench, nadaj jej nazwę `log_accuracy.py`i wklej poniższy kod:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Następnie należy utworzyć plik `run.py`i wklej poniższy kod:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Na koniec Otwórz interfejs wiersza polecenia za pomocą narzędzia Workbench i uruchom polecenie `python run.py` przesłać cztery eksperymentów. Po zakończeniu działania skryptu, powinny pojawić się cztery więcej jest uruchamiany w `Run History` kartę.

## <a name="query-the-run-history"></a>Zapytanie Historia uruchomień
Pierwsze polecenie znajduje się wartość maksymalna dokładność.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Przy użyciu tej wartości maksymalnej dokładność `1` i wartość progową `0.03`, jest uruchamiana za pomocą drugiego polecenia filtruje `Accuracy` , a następnie sortuje uruchamia przez `duration` rosnącej.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Jeśli chcesz ściśle wyboru górna granica ma format zapytania ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Jeśli używasz programu PowerShell, w poniższym kodzie użyto zmiennych lokalnych do przechowywania wartości progowej i maksymalna dokładność:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [sposób użycia Historia uruchomień i metryki modelu w konsoli usługi Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
