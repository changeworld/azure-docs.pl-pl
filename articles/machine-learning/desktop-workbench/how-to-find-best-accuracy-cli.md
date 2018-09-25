---
title: Znaleźć przebiegi z najlepszą dokładnością i najkrótszym czasem trwania w aplikacji Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: End-to-end przypadek użycia można znaleźć najlepszą dokładnością za pomocą interfejsu wiersza polecenia przy użyciu usługi Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 094fd6d8c6c6d647533cf5409d1a85283c71c80e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953246"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Znaleźć przebiegi z najlepszą dokładnością i najkrótszym czasem trwania
Biorąc pod uwagę wiele przebiegów, jeden przypadek użycia jest znaleźć przebiegi z najlepszą dokładnością. Jedno z podejść jest użycie interfejsu wiersza polecenia (CLI) za pomocą [JMESPath](http://jmespath.org/) zapytania. Aby uzyskać więcej informacji o sposobie używania JMESPath w interfejsie wiersza polecenia platformy Azure, zobacz [z wiersza polecenia platformy Azure zapytań JMESPath użyj](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). W poniższym przykładzie cztery uruchomienia są tworzone przy użyciu wartości dokładności 0 "," 0,98 "," 1 "i" 1. Przebiegi są odfiltrowywane, jeśli są one w zakresie `[MaxAccuracy-Threshold, MaxAccuracy]` gdzie `Threshold = .03`.

## <a name="sample-data"></a>Dane przykładowe
Jeśli nie masz istniejącej jest uruchamiany z `Accuracy` wartość następujące kroki Generowanie przebiegi do wykonywania zapytań.

Najpierw utwórz plik w języku Python w usłudze Azure Machine Learning Workbench, nadaj jej nazwę `log_accuracy.py`i wklej następujący kod:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Następnie należy utworzyć plik `run.py`i wklej następujący kod:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Wreszcie, Otwórz interfejs wiersza polecenia przy użyciu aplikacji Workbench i uruchom polecenie `python run.py` przesłać cztery eksperymentów. Po zakończeniu działania skryptu wyświetlony cztery więcej przebiegów w `Run History` kartę.

## <a name="query-the-run-history"></a>Zapytanie historii uruchamiania
Pierwsze polecenie znajduje się wartość maksymalna dokładność.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Przy użyciu tej wartości maksymalna dokładność `1` i wartość progową `0.03`, drugi filtry polecenia uruchamia się za pomocą `Accuracy` , a następnie sortuje działa przez `duration` rosnąco.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Jeśli chcesz ściśle wyboru górną granicę, ma format zapytania ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Jeśli używasz programu PowerShell, w poniższym kodzie użyto zmiennych lokalnych do przechowywania wartości progowej i maksymalna dokładność:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rejestrowania, zobacz [sposób użycia historię uruchomień i metryk modelu w aplikacji Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
