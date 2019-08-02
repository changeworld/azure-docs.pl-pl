---
title: Włączanie rejestrowania w usłudze Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak włączyć rejestrowanie w usłudze Azure Machine Learning przy użyciu domyślnego pakietu rejestrowania w języku Python, a także korzystać z funkcji specyficznych dla zestawu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 384cd2df9c629a73095cc55f4f6d65aa6a727225
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360964"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Włączanie rejestrowania w usłudze Azure Machine Learning

Zestaw Azure Machine Learning Python SDK umożliwia włączenie rejestrowania przy użyciu domyślnego pakietu rejestrowania języka Python, a także korzystanie z funkcji specyficznych dla zestawu SDK zarówno do rejestrowania lokalnego i rejestrowania w obszarze roboczym w portalu. Dzienniki udostępniają deweloperom informacje w czasie rzeczywistym dotyczące stanu aplikacji i mogą pomóc w diagnozowaniu błędów lub ostrzeżeń. W tym artykule przedstawiono różne sposoby włączania rejestrowania w następujących obszarach:

> [!div class="checklist"]
> * Modele szkoleniowe i cele obliczeniowe
> * Tworzenie obrazu
> * Wdrożone modele
> * Ustawienia `logging` języka Python

[Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md). Aby uzyskać [](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) więcej informacji na temat zestawu SDK, Skorzystaj z przewodnika.

## <a name="training-models-and-compute-target-logging"></a>Modele szkoleń i rejestrowanie obiektów docelowych obliczeń

Istnieje wiele sposobów włączania rejestrowania podczas procesu szkolenia modelu, a pokazane przykłady pokazują typowe wzorce projektowe. Dane dotyczące uruchamiania można łatwo rejestrować do obszaru roboczego w chmurze przy użyciu `start_logging` funkcji `Experiment` klasy.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zobacz dokumentację referencyjną dla klasy [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) , aby uzyskać dodatkowe funkcje rejestrowania.

Aby włączyć rejestrowanie lokalne stanu aplikacji podczas szkolenia, użyj `show_output` parametru. Włączenie pełnego rejestrowania pozwala zobaczyć szczegóły procesu szkolenia, a także informacje o zasobach zdalnych lub obiektach docelowych obliczeniowych. Użyj poniższego kodu, aby włączyć rejestrowanie podczas przekazywania eksperymentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Można również użyć tego samego parametru w `wait_for_completion` funkcji w uruchomionym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

Zestaw SDK obsługuje również korzystanie z domyślnego pakietu rejestrowania języka Python w niektórych scenariuszach szkoleniowych. Poniższy przykład włącza poziom `INFO` rejestrowania `AutoMLConfig` w obiekcie.

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

Podczas tworzenia trwałego obiektu `show_output` docelowego obliczeń można także użyć parametru. Określ parametr w `wait_for_completion` funkcji, aby włączyć rejestrowanie podczas tworzenia elementu docelowego obliczeń.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Rejestrowanie podczas tworzenia obrazu

Włączenie rejestrowania podczas tworzenia obrazu umożliwi wyświetlenie błędów podczas procesu kompilacji. `show_output` Ustaw parametr`wait_for_deployment()` w funkcji.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Rejestrowanie wdrożonych modeli

Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci Web, Załaduj usługę i Użyj `get_logs()` funkcji. Dzienniki mogą zawierać szczegółowe informacje o błędach, które wystąpiły podczas wdrażania.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Możesz również rejestrować niestandardowe ślady stosu dla usługi sieci Web, włączając Application Insights, co umożliwia monitorowanie czasów żądania/odpowiedzi, częstotliwości niepowodzeń i wyjątków. Wywołaj `update()` funkcję w istniejącej usłudze sieci Web, aby włączyć Application Insights.

```python
service.update(enable_app_insights=True)
```

Aby uzyskać więcej informacji na temat pracy z Application Insights w Azure Portal, zobacz [instrukcje](how-to-enable-app-insights.md) .

## <a name="python-native-logging-settings"></a>Natywne ustawienia rejestrowania języka Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który powoduje ustawienie poziomu rejestrowania na Debugowanie. Aby ustawić poziom rejestrowania, Dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
