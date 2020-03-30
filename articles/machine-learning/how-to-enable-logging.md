---
title: Włączanie rejestrowania w usłudze Azure Machine Learning
description: Dowiedz się, jak włączyć rejestrowanie w usłudze Azure Machine Learning przy użyciu zarówno domyślnego pakietu rejestrowania języka Python, jak i przy użyciu funkcji specyficznych dla zestawu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78396140"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Włączanie rejestrowania w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zestaw SDK języka Python usługi Azure Machine Learning umożliwia włączenie rejestrowania przy użyciu zarówno domyślnego pakietu rejestrowania języka Python, jak i przy użyciu funkcji specyficznych dla zestawu SDK, zarówno do rejestrowania lokalnego, jak i rejestrowania w obszarze roboczym w portalu. Dzienniki zapewniają deweloperom informacje w czasie rzeczywistym o stanie aplikacji i mogą pomóc w diagnozowaniu błędów lub ostrzeżeń. W tym artykule dowiesz się, jak włączyć rejestrowanie w następujących obszarach:

> [!div class="checklist"]
> * Modele szkoleniowe i cele obliczeniowe
> * Tworzenie obrazów
> * Wdrożone modele
> * Ustawienia `logging` języka Python

[Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md). Więcej informacji na temat SDK można znaleźć w [przewodniku.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

## <a name="training-models-and-compute-target-logging"></a>Modele szkoleniowe i rejestrowanie celów obliczeniowych

Istnieje wiele sposobów, aby włączyć rejestrowanie podczas procesu szkolenia modelu, a pokazane przykłady zilustrują typowe wzorce projektowe. Można łatwo rejestrować dane związane z uruchomieniem do `start_logging` obszaru roboczego w chmurze przy użyciu funkcji w `Experiment` klasie.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zobacz dokumentację referencyjną dla [Uruchom](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) klasy dla dodatkowych funkcji rejestrowania.

Aby włączyć lokalne rejestrowanie stanu aplikacji podczas `show_output` postępu szkolenia, należy użyć parametru. Włączenie szczegółowego rejestrowania umożliwia wyświetlenie szczegółów z procesu szkoleniowego, a także informacji o wszelkich zdalnych zasobach lub celach obliczeniowych. Użyj następującego kodu, aby włączyć rejestrowanie podczas przesyłania eksperymentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Można również użyć tego samego `wait_for_completion` parametru w funkcji w wynikowym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

Zestaw SDK obsługuje również przy użyciu domyślnego pakietu rejestrowania języka python w niektórych scenariuszach szkolenia. Poniższy przykład umożliwia poziom `INFO` rejestrowania `AutoMLConfig` w obiekcie.

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

Można również użyć `show_output` parametru podczas tworzenia trwałego obiektu docelowego obliczeń. Określ parametr `wait_for_completion` w funkcji, aby włączyć rejestrowanie podczas tworzenia celu obliczeniowego.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Rejestrowanie dla wdrożonych modeli

Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci web, `get_logs()` należy załadować usługę i użyć tej funkcji. Dzienniki mogą zawierać szczegółowe informacje o wszelkich błędach, które wystąpiły podczas wdrażania.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Można również rejestrować niestandardowe ślady stosu dla usługi sieci web, włączając usługę Application Insights, która umożliwia monitorowanie czasów żądania/odpowiedzi, współczynników niepowodzeń i wyjątków. Wywołanie `update()` funkcji w istniejącej usłudze sieci web, aby włączyć usługę Application Insights.

```python
service.update(enable_app_insights=True)
```

Aby uzyskać więcej informacji, zobacz [Monitorowanie i zbieranie danych z punktów końcowych usługi sieci web ml](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Ustawienia rejestrowania natywnego języka Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który nakazuje ustawienie poziomu rejestrowania na DEBUG. Aby ustawić poziom rejestrowania, dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i zbieranie danych z punktów końcowych usługi sieci web ml](how-to-enable-app-insights.md)