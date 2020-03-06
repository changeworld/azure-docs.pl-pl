---
title: Włącz rejestrowanie w Azure Machine Learning
description: Dowiedz się, jak włączyć rejestrowanie w Azure Machine Learning przy użyciu zarówno domyślnego pakietu rejestrowania języka Python, jak i funkcji specyficznych dla zestawu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396140"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Włącz rejestrowanie w Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zestaw Azure Machine Learning Python SDK umożliwia włączenie rejestrowania przy użyciu domyślnego pakietu rejestrowania języka Python, a także korzystanie z funkcji specyficznych dla zestawu SDK zarówno do rejestrowania lokalnego i rejestrowania w obszarze roboczym w portalu. Dzienniki udostępniają deweloperom informacje w czasie rzeczywistym dotyczące stanu aplikacji i mogą pomóc w diagnozowaniu błędów lub ostrzeżeń. W tym artykule przedstawiono różne sposoby włączania rejestrowania w następujących obszarach:

> [!div class="checklist"]
> * Modele szkoleniowe i cele obliczeniowe
> * Tworzenie obrazu
> * Wdrożone modele
> * Ustawienia `logging` Python

[Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md). Aby uzyskać więcej informacji na temat zestawu SDK, Skorzystaj z [przewodnika](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

## <a name="training-models-and-compute-target-logging"></a>Modele szkoleń i rejestrowanie obiektów docelowych obliczeń

Istnieje wiele sposobów włączania rejestrowania podczas procesu szkolenia modelu, a pokazane przykłady pokazują typowe wzorce projektowe. Dane dotyczące uruchamiania można łatwo rejestrować do obszaru roboczego w chmurze przy użyciu funkcji `start_logging` na klasie `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zobacz dokumentację referencyjną dla klasy [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) , aby uzyskać dodatkowe funkcje rejestrowania.

Aby włączyć rejestrowanie lokalne stanu aplikacji podczas szkolenia, użyj parametru `show_output`. Włączenie pełnego rejestrowania pozwala zobaczyć szczegóły procesu szkolenia, a także informacje o zasobach zdalnych lub obiektach docelowych obliczeniowych. Użyj poniższego kodu, aby włączyć rejestrowanie podczas przekazywania eksperymentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Można również użyć tego samego parametru w funkcji `wait_for_completion` w uruchomionym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

Zestaw SDK obsługuje również korzystanie z domyślnego pakietu rejestrowania języka Python w niektórych scenariuszach szkoleniowych. Poniższy przykład umożliwia włączenie poziomu rejestrowania `INFO` w obiekcie `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Podczas tworzenia trwałego elementu docelowego obliczeń można także użyć parametru `show_output`. Określ parametr w funkcji `wait_for_completion`, aby włączyć rejestrowanie podczas tworzenia elementu docelowego obliczeń.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Rejestrowanie wdrożonych modeli

Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci Web, Załaduj usługę i użyj funkcji `get_logs()`. Dzienniki mogą zawierać szczegółowe informacje o błędach, które wystąpiły podczas wdrażania.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Możesz również rejestrować niestandardowe ślady stosu dla usługi sieci Web, włączając Application Insights, co umożliwia monitorowanie czasów żądania/odpowiedzi, częstotliwości niepowodzeń i wyjątków. Wywołaj funkcję `update()` w istniejącej usłudze sieci Web, aby włączyć Application Insights.

```python
service.update(enable_app_insights=True)
```

Aby uzyskać więcej informacji, zobacz [monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web ml](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Natywne ustawienia rejestrowania języka Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który powoduje ustawienie poziomu rejestrowania na Debugowanie. Aby ustawić poziom rejestrowania, Dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web ML](how-to-enable-app-insights.md)