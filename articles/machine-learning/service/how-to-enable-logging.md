---
title: Włącz rejestrowanie w usłudze Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak włączyć rejestrowanie w usłudze Azure Machine Learning przy użyciu zarówno domyślny pakiet rejestrowania języka Python, a także korzystanie z funkcji specyficznych dla zestawu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: 0d75b983ad6d3b6256852335dc523b481bbe046f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789377"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Włącz rejestrowanie w usłudze Azure Machine Learning

Zestaw SDK języka Python usługi Azure Machine Learning pozwala włączyć rejestrowanie za pomocą pakietu domyślne rejestrowanie języka Python, a także korzystanie z funkcji specyficznych dla zestawu SDK, lokalne rejestrowanie i logowanie się do swojego obszaru roboczego w portalu. Dzienniki umożliwiają deweloperom w czasie rzeczywistym informacje o stanie aplikacji i może ułatwić diagnozowanie błędów i ostrzeżeń. W tym artykule dowiesz się różne sposoby włączania rejestrowania w następujących obszarach:

> [!div class="checklist"]
> * Szkolenie modeli i obliczeniowych elementów docelowych
> * Tworzenie obrazu
> * Wdrożone modeli
> * Python `logging` ustawienia

[Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md). Użyj [przewodnik](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Aby uzyskać więcej informacji o zestawie SDK.

## <a name="training-models-and-compute-target-logging"></a>Szkolenie modeli i rejestrowanie docelowej obliczeń

Istnieje wiele sposobów, aby włączyć rejestrowanie podczas procesu uczenia modelu i przykładów pokazanych przedstawiają często używane wzorce projektowe. Można łatwo rejestrować dane dotyczące wykonywania do obszaru roboczego usługi w chmurze, za pomocą `start_logging` działać na `Experiment` klasy.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zobacz dokumentację referencyjną [Uruchom](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) klasy dla funkcji dodatkowe rejestrowanie.

Aby włączyć rejestrowanie lokalnego stanu aplikacji podczas postępy szkolenia, należy użyć `show_output` parametru. Włączanie pełnego rejestrowania pozwala na szczegółowe informacje z procesu uczenia, a także o wszelkich zasobów zdalnych lub celów obliczeń. Użyj poniższego kodu, aby włączyć rejestrowanie na przesyłanie eksperymentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Można również użyć tego samego parametru w `wait_for_completion` funkcja przy uruchomieniu wynikowej.

```python
run.wait_for_completion(show_output=True)
```

Zestaw SDK obsługuje również przy użyciu domyślnego pakietu rejestrowania języka python w niektórych scenariuszach szkolenia. Poniższy przykład umożliwia poziom rejestrowania `INFO` w `AutoMLConfig` obiektu.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Można również użyć `show_output` parametru podczas tworzenia trwałego obliczeniowego elementu docelowego. Określ parametr w `wait_for_completion` funkcję, aby włączyć rejestrowanie podczas tworzenia docelowego obliczeń.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Rejestrowanie podczas tworzenia obrazu

Włączanie rejestrowania podczas tworzenia obrazu umożliwi są wyświetlane błędy podczas procesu kompilacji. Ustaw `show_output` param na `wait_for_deployment()` funkcji.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Rejestrowanie dla wdrożonych modelach

Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci web, ładowania usługi i użyj `get_logs()` funkcji. Dzienniki mogą zawierać szczegółowe informacje na temat błędów, które wystąpiły podczas wdrażania.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Można również rejestrować niestandardowe stos danych śledzenia dla usługi sieci web przez włączenie usługi Application Insights, który umożliwia przeprowadzenie monitor żądań i czas odpowiedzi, współczynniki błędów i wyjątków. Wywołaj `update()` funkcji w istniejącej usługi sieci web, aby włączyć usługę Application Insights.

```python
service.update(enable_app_insights=True)
```

Zobacz [porad](how-to-enable-app-insights.md) Aby uzyskać więcej informacji na temat pracy z usługą Application Insights w witrynie Azure portal.

## <a name="python-native-logging-settings"></a>Ustawienia rejestrowania natywnego języka Python

Niektóre dzienniki w zestawie SDK może zawierać błąd, który powoduje, że można ustawić poziom rejestrowania do debugowania. Aby ustawić poziom rejestrowania, Dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
